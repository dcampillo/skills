---
name: ralph-it
description: Scaffold a Ralph Loop — a self-driving bash loop that repeatedly invokes Claude to implement one task at a time until the work is complete. Drives either a single GitHub issue or a whole PRD's linked child issues in sequence. Lists open ready-for-agent GitHub issues, lets the user pick one, and creates a ralph-it/ folder at the repo root with a hardened ralph.sh tailored to that target plus a README. Use when the user wants to set up a Ralph loop, autonomously grind through a PRD or issue, "ralph it", or run an unattended implement-test-commit loop.
---

# Ralph It

Scaffolds a **Ralph Loop**: a bash script that calls `claude` in a loop, doing one
task per iteration (implement → test → check off criteria → log progress → commit) until
the work signals completion. The target is a **GitHub issue** that stays the single source
of truth — the loop never keeps a persistent local copy. Output lands in `ralph-it/` at the
repo root.

The loop runs in one of **two modes**, selected by which variable the scaffold bakes in:

- **Single-issue mode (`ISSUE=N`):** drive one issue to completion, then close it. This is
  the original behavior.
- **PRD mode (`PRD=N`):** drive a PRD's **linked child issues** — every open
  `ready-for-agent` issue whose body names `#N` under a `## Parent` heading — one at a time
  in ascending issue-number order, closing each as it completes, then closing the PRD itself
  when no open children remain. GitHub-only (see [REFERENCE.md](REFERENCE.md) for why the
  local-file fallback is single-issue only).

Both modes share the same inner per-issue loop (`drive_issue()`); PRD mode just wraps it in
an outer iterator over the children.

## Workflow

1. **Find the repo root.** Run `git rev-parse --show-toplevel`. `ralph-it/` goes there. If
   not in a git repo, ask where to scaffold (the loop commits, so git is required).

2. **Pick the target and determine the mode.**
   - **Explicit override (skip the picker):** if the user names a target directly, use it:
     - `"ralph PRD 4"` / `"ralph the PRD #4"` → **PRD mode**, `PRD=4`.
     - `"ralph issue 3"` → **single-issue mode**, `ISSUE=3`.
     - a local file path (`"ralph it for docs/prd.md"`) → **single-issue, local-file
       fallback** (PRD mode is GitHub-only) — see [REFERENCE.md](REFERENCE.md).
   - **Default (no target named):** list open issues labeled `ready-for-agent`:
     ```
     gh issue list --label ready-for-agent --state open \
       --json number,title,body \
       --jq '.[] | {number, title, body}'
     ```
     Present them with `AskUserQuestion` — one option per issue, label `#<num> <title>`,
     description a one-line snippet of the body. **If more than 4 issues**, print a numbered
     list instead and ask the user to type the number (AskUserQuestion caps at 4 options).
     **If zero issues** carry the label, stop and say so; offer to broaden the filter once
     (show all open issues) or point the user at the `triage` / `to-prd` skills. Do not
     silently proceed.

     The list mixes PRDs and their children (a PRD is typically titled `PRD: …`). **Ask the
     user whether the picked issue is a PRD** (drive its children) **or a single issue** —
     don't guess from the title. A wrong guess is also caught at runtime: PRD mode fails fast
     if the baked number has no children (see Source-of-truth model).

3. **Capture the number and mode.** Record the picked issue as either `PRD=<N>` (PRD mode)
   or `ISSUE=<N>` (single-issue mode) to bake into the script — **exactly one is set, the
   other left empty; presence selects the mode at runtime.** This makes `gh` a **runtime
   dependency** (read + write + close), not just scaffold-time. Note the chosen mode to the
   user.

4. **Write `ralph-it/ralph.sh`** from the template below, substituting the real `PRD=<N>`
   *or* `ISSUE=<N>` (leave the other empty). The loop fetches each issue body fresh every
   iteration into a throwaway mirror, so there is no persistent local copy to drift. Make it
   executable (`chmod +x`).

5. **Write `ralph-it/README.md`** from the README template **matching the baked mode** (the
   templates below are mode-conditional — write only the one that applies), and **create
   `ralph-it/progress.txt`** (empty). Add `ralph-it/.prd-*.md` and `ralph-it/logs/` to
   `.gitignore` (the mirror and run logs are transient; ask before gitignoring `progress.txt`
   — it's a committed breadcrumb by default).

6. **Tell the user how to run it** — do **not** run the loop yourself (long-running, commits,
   mutates GitHub, meant to run unattended). Show: `./ralph-it/ralph.sh <iterations>`.

## Source-of-truth model (read this before editing the template)

- **The GitHub issue is authoritative** for both the spec and the checkbox progress state.
  Nothing about the work — not the spec, not progress, not which children remain — is ever
  cached locally in a way that can drift.
- Each iteration **re-fetches** the *current target issue's* body into
  `ralph-it/.prd-current.md` (gitignored, rewritten every iteration, never committed) purely
  so `claude` can `@`-reference it. It cannot go stale — it's regenerated from the remote at
  the top of each loop.
- The agent **writes checkbox progress back to the issue body** via `gh issue edit`, so the
  next iteration's fetch reflects it.
- `ralph-it/progress.txt` is a **local committed breadcrumb** for debugging — never the
  source of truth. Losing it corrupts nothing. In PRD mode each line is tagged `[#N]` with
  the child it concerns (the wrapper prepends the tag, not the agent).
- **Per-issue completion gate:** when the agent signals `<promise>COMPLETE</promise>`, the
  gate **re-fetches** that issue's body and closes it only if no `- [ ]` remain. A false
  `COMPLETE` (sentinel fired but a `- [ ]` survives) aborts the whole run with `exit 1` — in
  PRD mode it does **not** skip ahead to the next child, because later slices depend on
  earlier ones.

### PRD mode specifics

- **Children are discovered fresh each outer step**, never snapshotted. The wrapper re-runs
  discovery and drives the **lowest-numbered open child**, looping until none remain. This
  makes "skip closed, drive open" and cross-run resume fall out for free: a re-run simply
  picks up at the lowest open child.
- **Discovery rule:** an issue is a child of `PRD=N` if it is open, carries `ready-for-agent`,
  and its body names `#N` under a `## Parent` heading. Ascending issue number is the drive
  order (children are authored as dependency-ordered tracer slices, numbered in order).
- **No PRD-type validation.** The baked number is trusted. If discovery finds **zero**
  children, the loop **fails fast** (`exit 1`) — that almost always means a wrong `PRD=` or
  unlabeled children, and a loud stop beats burning iterations on the wrong thing.
- **Global iteration budget.** `<iterations>` caps total `claude` calls across *all*
  children, not per child — one hard cost ceiling for the whole unattended run. Hitting it
  mid-PRD exits 1; just re-run to resume.
- **The no-commit guard spans the whole run** (a single counter), not per child. A real
  child close *is* progress and resets it; advancing to a new child never masks a stuck agent.
- **PRD close:** when no open children remain, re-fetch the PRD body and `gh issue close N`
  with a summary comment, exit 0. (PRDs here carry user stories as prose, not `- [ ]`
  checkboxes, so there is no separate criterion state to gate on — "all children closed" *is*
  the PRD being done.)
- The **agent prompt is mode-agnostic**: from inside one `claude` call it always works
  exactly one issue and emits `COMPLETE` when *that issue* is done. All cross-child
  orchestration lives in the bash wrapper.

## Hardened loop template

Write to `ralph-it/ralph.sh`. Bake **exactly one** of `PRD=<N>` / `ISSUE=<N>`; leave the
other empty. Presence selects the mode at runtime.

```bash
#!/bin/bash
set -euo pipefail

if [ -z "${1:-}" ]; then
  echo "Usage: $0 <iterations>"
  exit 1
fi

# --- Target (bake exactly one; leave the other empty) ------------------------
# PRD mode:          PRD=4   ISSUE=   -> drive #4's linked child issues in order.
# Single-issue mode: PRD=    ISSUE=3  -> drive issue #3 to completion.
PRD=
ISSUE=3
# -----------------------------------------------------------------------------

ITERATIONS="$1"          # GLOBAL cap on total claude calls (across all children in PRD mode)

LOG_DIR="ralph-it/logs"
mkdir -p "$LOG_DIR"
RUN_LOG="$LOG_DIR/run-$(date +%Y%m%d-%H%M%S).log"
MIRROR="ralph-it/.prd-current.md"

# Guard: stop if N consecutive claude calls make no new commit (loop is stuck).
# Single counter for the WHOLE run — spans child transitions in PRD mode.
NO_COMMIT_LIMIT=2
no_commit_streak=0
last_head="$(git rev-parse HEAD 2>/dev/null || echo none)"

# Global iteration counter, shared across children in PRD mode.
iter=0

log() { echo "[$(date +%H:%M:%S)] $*" | tee -a "$RUN_LOG"; }

command -v gh >/dev/null 2>&1 || { log "FATAL: gh not found; this loop needs it at runtime."; exit 1; }

# Lowest-numbered open ready-for-agent issue whose body names "#$PRD" as the
# parent — i.e. "#$PRD" is the first token right after a "## Parent" heading.
# Re-run fresh every outer step — never snapshotted, so a just-closed child drops
# out and re-runs resume at the next open child. Prints the issue number, or
# nothing if there are none.
#
# The "[[:space:]]*" (not a lazy ".*") between the heading and "#$PRD" is
# load-bearing: it spans only the newline to the parent token, so a child of a
# DIFFERENT prd whose body merely mentions "#$PRD" later in prose does NOT match.
# The trailing "\b" stops "#4" from matching inside "#44".
next_child() {
  gh issue list --label ready-for-agent --state open --json number,body \
    --jq "[.[] | select(.body | test(\"(?m)^##[[:space:]]+Parent\\\\b[[:space:]]*#${PRD}\\\\b\"))] | sort_by(.number) | .[0].number // empty"
}

# Drive ONE issue ($1) until it signals completion or a guard fires.
# Returns 0 when the issue was completed and closed; the no-progress / failed-gate
# guards exit the whole script directly (exit 1).
drive_issue() {
  local target="$1"
  log "--- Driving issue #$target ---"

  while (( iter < ITERATIONS )); do
    iter=$((iter + 1))
    log "=== Iteration $iter/$ITERATIONS (issue #$target) ==="

    # Per-iteration mirror: fresh fetch of the remote issue (transport only, never edited).
    gh issue view "$target" --json title,body \
      --jq '"# " + .title + "\n\n" + .body' > "$MIRROR"

    result=$(claude --permission-mode acceptEdits -p "@$MIRROR @ralph-it/progress.txt \
    You are working GitHub issue #$target (mirrored read-only into $MIRROR). \
    1. Find the highest-priority unmet acceptance criterion and implement it. \
    2. Run your tests and type checks. \
    3. In the issue body, check off (\`- [x]\`) only the acceptance criteria you fully \
       satisfied this iteration. Preserve all other text exactly — do not reword, reorder, \
       summarize, or remove anything. Write it back with: gh issue edit $target --body-file <file>. \
    4. On the LAST line of your output, print a one-line progress note prefixed with NOTE: \
       (just the note text; do not edit ralph-it/progress.txt yourself). \
    5. Commit your changes. \
    ONLY WORK ON A SINGLE TASK. \
    If every acceptance criterion on this issue is met, output <promise>COMPLETE</promise>.")

    echo "$result" | tee -a "$RUN_LOG"

    # Append the agent's NOTE: line to progress.txt, tagged with the issue number.
    note="$(printf '%s\n' "$result" | sed -n 's/^NOTE: *//p' | tail -1)"
    [ -n "$note" ] && echo "[#$target] $note" >> ralph-it/progress.txt

    if [[ "$result" == *"<promise>COMPLETE</promise>"* ]]; then
      # Completion gate: re-fetch the body and only close if no unchecked criteria remain.
      gh issue view "$target" --json body --jq '.body' > ralph-it/.prd-final.md
      if grep -q '^[[:space:]]*- \[ \]' ralph-it/.prd-final.md; then
        log "COMPLETE signaled but unchecked criteria remain on #$target — NOT closing. Review manually."
        exit 1
      fi
      log "Issue #$target complete. Closing it."
      gh issue close "$target" --comment "Completed by Ralph loop (iteration $iter)."
      return 0
    fi

    # No-op guard: did this iteration actually commit anything?
    new_head="$(git rev-parse HEAD 2>/dev/null || echo none)"
    if [[ "$new_head" == "$last_head" ]]; then
      no_commit_streak=$((no_commit_streak + 1))
      log "WARNING: no new commit this iteration ($no_commit_streak/$NO_COMMIT_LIMIT)."
      if (( no_commit_streak >= NO_COMMIT_LIMIT )); then
        log "Stopping: $NO_COMMIT_LIMIT consecutive iterations made no progress."
        exit 1
      fi
    else
      no_commit_streak=0
      last_head="$new_head"
    fi
  done

  return 1   # ran out of global budget before completing this issue
}

if [ -n "$PRD" ]; then
  # ---- PRD mode: drive linked children in ascending order, then close the PRD ----
  log "PRD mode: driving children of #$PRD (global budget $ITERATIONS)."
  child="$(next_child)"
  if [ -z "$child" ]; then
    log "FATAL: no open ready-for-agent children reference PRD #$PRD. Wrong PRD number, or children unlabeled?"
    exit 1
  fi
  while [ -n "$child" ]; do
    if ! drive_issue "$child"; then
      log "Reached $ITERATIONS iterations before completing child #$child. Re-run to resume."
      exit 1
    fi
    child="$(next_child)"   # re-enumerate: the one we just closed drops out
  done
  # No open children remain → the PRD is delivered. Close it.
  gh issue view "$PRD" --json body --jq '.body' > ralph-it/.prd-final.md
  log "All children of #$PRD complete. Closing the PRD."
  gh issue close "$PRD" --comment "All linked children completed by Ralph loop ($iter iterations)."
  exit 0
elif [ -n "$ISSUE" ]; then
  # ---- Single-issue mode: drive one issue to completion ----
  log "Single-issue mode: driving #$ISSUE (budget $ITERATIONS)."
  if drive_issue "$ISSUE"; then
    log "Done after $iter iterations."
    exit 0
  fi
  log "Reached $ITERATIONS iterations without completing #$ISSUE."
  exit 1
else
  log "FATAL: neither PRD nor ISSUE is set. Bake exactly one."
  exit 1
fi
```

Hardening over a bare loop: `set -euo pipefail`; a `gh` preflight check; a timestamped
per-run log under `ralph-it/logs/`; `[HH:MM:SS]` markers; a no-commit guard (single counter
for the whole run) that aborts a spinning loop; and a **completion gate** that re-fetches the
issue and refuses to close it while any `- [ ]` criterion remains. The inner contract —
single task, `acceptEdits`, `<promise>COMPLETE</promise>` sentinel — is unchanged and shared
by both modes via `drive_issue()`. PRD mode wraps it in `next_child()` re-enumeration and a
final PRD close.

## README template (mode-conditional)

Write `ralph-it/README.md` describing **only the mode that was baked** — don't make the
reader filter out the mode they aren't using.

### If single-issue mode (`ISSUE=<N>`)

```markdown
# Ralph Loop (single-issue mode)

A self-driving loop that repeatedly invokes Claude to implement one acceptance
criterion at a time, against GitHub issue #<N>, until the issue is complete.

## Usage

Run from the **repo root**:

    ./ralph-it/ralph.sh <iterations>

`<iterations>` caps how many times the loop runs. Each iteration: fetch the issue,
implement the highest-priority unmet criterion, run tests/type checks, check off the
criterion on the issue, log a tagged note to `progress.txt`, and commit.

## Source of truth

**GitHub issue #<N> is authoritative** — both the spec and the checkbox state live there.
The loop keeps no persistent local copy, so there is nothing to drift. Each iteration
re-fetches the issue into `ralph-it/.prd-current.md` (transient, gitignored, never edited)
just so Claude can read it; progress is written back to the issue body.

`gh` must be installed and authenticated — the loop reads, edits, and closes the issue.

## Files

- `ralph-it/ralph.sh` — the loop. Holds `ISSUE=<N>` (`PRD=` empty).
- `ralph-it/progress.txt` — committed, append-only breadcrumb (`[#N] note` per line). Not
  the source of truth.
- `ralph-it/.prd-current.md`, `ralph-it/.prd-final.md` — transient mirrors (gitignored).
- `ralph-it/logs/` — timestamped per-run logs (gitignored).

## Stopping conditions

- Every criterion met (`<promise>COMPLETE</promise>`) **and** no `- [ ]` remain on a fresh
  fetch → the loop closes the issue, exit 0.
- `COMPLETE` signaled but unchecked criteria remain → loop refuses to close, exit 1.
- The iteration cap is reached → exit 1.
- Two consecutive iterations make no new commit (stuck) → exit 1.

## If the issue body gets mangled

The loop edits the issue body each iteration. If an iteration corrupts it, restore a prior
version from the issue's **edit history** on GitHub (the "edited" pencil dropdown).

## Inspecting runs

    tail -f ralph-it/logs/$(ls -t ralph-it/logs | head -1)
```

### If PRD mode (`PRD=<N>`)

```markdown
# Ralph Loop (PRD mode)

A self-driving loop that drives the **linked child issues** of PRD #<N> — every open
`ready-for-agent` issue whose body names `#<N>` under a `## Parent` heading — one at a
time, in ascending issue-number order, until they are all complete. Then it closes the PRD.

## Usage

Run from the **repo root**:

    ./ralph-it/ralph.sh <iterations>

`<iterations>` is a **global** cap on total Claude calls across *all* children — one cost
ceiling for the whole run, not per child. Each iteration drives the lowest-numbered open
child: implement its highest-priority unmet criterion, run tests/type checks, check it off
on that issue, log a `[#child] note` to `progress.txt`, and commit. When a child's criteria
are all met it is closed and the loop advances to the next open child.

The loop is **resumable**: if it hits the iteration cap mid-PRD, just re-run — closed
children are skipped and it picks up at the next open one.

## Source of truth

**GitHub is authoritative** — spec, per-issue checkbox state, and the very set of remaining
children all live there. Nothing is cached locally. The children are re-discovered fresh on
every step (a just-closed child drops out automatically), and the current child's body is
re-fetched into `ralph-it/.prd-current.md` (transient, gitignored) just so Claude can read it.

`gh` must be installed and authenticated — the loop reads, edits, and closes issues, and
closes the PRD at the end.

## Files

- `ralph-it/ralph.sh` — the loop. Holds `PRD=<N>` (`ISSUE=` empty).
- `ralph-it/progress.txt` — committed, append-only PRD-wide timeline (`[#child] note` per
  line). Not the source of truth.
- `ralph-it/.prd-current.md`, `ralph-it/.prd-final.md` — transient mirrors (gitignored).
- `ralph-it/logs/` — timestamped per-run logs (gitignored).

## Stopping conditions

- No open children remain → re-fetch and **close PRD #<N>** with a summary comment, exit 0.
- A child signals `COMPLETE` but still has unchecked `- [ ]` → loop refuses to close it and
  **aborts the whole run**, exit 1 (later children depend on earlier ones, so it will not
  skip ahead). Review and re-run.
- The global iteration cap is reached mid-PRD → exit 1 (re-run to resume).
- Two consecutive iterations make no new commit (stuck) → exit 1.
- **No open children reference the PRD at all** → fail fast, exit 1 (wrong `PRD=`, or
  children unlabeled / already all closed).

## If an issue body gets mangled

The loop edits issue bodies each iteration. If an iteration corrupts one, restore a prior
version from that issue's **edit history** on GitHub (the "edited" pencil dropdown).

## Inspecting runs

    tail -f ralph-it/logs/$(ls -t ralph-it/logs | head -1)
```

## Notes

- Don't run the loop yourself. Hand the run command to the user.
- The loop mutates GitHub unattended (edits issue bodies each progress iteration, closes
  issues on completion — and in **PRD mode also closes the PRD itself** once every child is
  done). Make sure the user understands this before they run it.
- PRD mode is **GitHub-only** — "linked child issues" has no meaning for a flat local file.
  For the single-issue local-file fallback, see [REFERENCE.md](REFERENCE.md).
