# Reference: modes, fallback path, and tuning

The primary path (GitHub issue(s) as the single source of truth, no persistent local copy)
lives in SKILL.md, including both **single-issue mode** (`ISSUE=N`) and **PRD mode**
(`PRD=N`, drives the PRD's linked children). This file covers PRD-mode discovery details, the
local-file fallback, and tuning knobs.

## PRD mode: child discovery and fail-fast

PRD mode (`PRD=N`) drives the PRD's **linked children** — discovered, never baked in, so the
set always reflects current GitHub state.

- **Linkage is parsed child→parent**, because that is how the issues are actually authored:
  each child's body carries a `## Parent` heading followed by `#N`. The PRD body does *not*
  list its children. `next_child()` greps the open `ready-for-agent` issues for that pattern.
- **The discovery regex is anchored, deliberately.** It matches `#N` only as the token
  immediately after the `## Parent` heading (`^##\s+Parent\b[[:space:]]*#N\b`), *not* anywhere
  in the body. A lazy `.*?#N` would false-match a child of a *different* PRD whose body merely
  mentions `#N` in prose — verified failure mode. The trailing `\b` stops `#4` matching inside
  `#44`.
- **Drive order is ascending issue number**, relying on the convention that children are
  *created* in dependency order (tracer-bullet slices, lower number = earlier slice). No
  explicit dependency graph is parsed; a linear slice sequence does not need one.
- **Re-enumerate every outer step**, never snapshot. The remote is the only source of truth
  for "which children remain" just as it is for the spec — so a just-closed child drops out and
  cross-run resume is free. The cost is one `gh issue list` per child transition (cheap).
- **Zero children → fail fast (`exit 1`).** Almost always a wrong `PRD=` or unlabeled
  children; a loud stop beats grinding the wrong target. There is **no** silent fallback to
  driving the PRD itself as a single issue — that would mask the bug.
- **No PRD-type validation.** The baked number is trusted; the zero-children check is the only
  guard, and it is sufficient.
- **PRD close.** When no open children remain, re-fetch the PRD body and `gh issue close N`.
  PRDs here carry user stories as prose, not `- [ ]` checkboxes, so "all children closed" *is*
  done — there is no separate criterion state to gate the PRD close on.

## Local-file fallback (no GitHub issue) — single-issue only

Use this only when the user explicitly names a local PRD file instead of an issue. **PRD mode
does not apply** — "linked child issues" is meaningless for a flat local file — so this is a
single-issue variant. There is no remote to be the source of truth, so the model differs:

- Copy the named file to `ralph-it/PRD.md` once at scaffold time, with a provenance header:
  ```
  <!-- Ralph loop PRD — copied from docs/prd.md on YYYY-MM-DD. Local working copy; edits here do not flow back to the original. -->
  ```
- In `ralph.sh`, leave both `PRD=` and `ISSUE=` empty, and add a third sentinel
  (`LOCAL=ralph-it/PRD.md`) selecting this branch; in `drive_issue()`, `@`-reference
  `@ralph-it/PRD.md` instead of fetching a mirror — drop the per-iteration `gh issue view`.
- The prompt's check-off step targets the local file: *"In ralph-it/PRD.md, check off
  (`- [x]`) the acceptance criteria you satisfied."* (no `gh`).
- The completion gate greps `ralph-it/PRD.md` directly (no fresh fetch) and, with no issue to
  close, just `exit 0`s on a clean PRD. Tell the user to close/track the work themselves.

Everything else (no-commit guard, logging, single-task contract, `[#…]`-tagged progress) is
identical — in the local-file case tag progress lines with the file stem or just leave them
untagged, since there is no issue number.

## Tuning knobs

- **`NO_COMMIT_LIMIT`** — consecutive no-commit iterations before bailing. Default 2. Raise
  only if tasks legitimately span iterations without committing (rare; the prompt asks for a
  commit every iteration). In PRD mode the counter is a **single counter spanning the whole
  run**, not per child — advancing to a new child does not reset it, so a stuck agent can't
  hide behind a child transition. A real child *close* counts as progress and resets it.
- **`ITERATIONS` (the `<iterations>` arg)** — in PRD mode this is the **global** cap on total
  claude calls across all children, one cost ceiling for the unattended run. It is not
  per-child; a re-run resumes within budget because closed children are skipped.
- **`--permission-mode`** — `acceptEdits` lets the loop edit files without prompting. Use
  `bypassPermissions` only in a sandbox where the loop may also run arbitrary commands; riskier,
  make it a deliberate user choice.
- **Iteration prompt** — keep it terse and single-task. `ONLY WORK ON A SINGLE TASK` is
  load-bearing: it stops the agent doing the whole PRD in one giant, unreviewable commit.
- **Per-iteration timeout** — wrap the `claude` call in `timeout 1800 claude ...` (30 min) to
  kill a runaway iteration; `set -e` then aborts the loop.

## Why a sentinel string

Completion is detected by string-matching `<promise>COMPLETE</promise>` in stdout because
`claude -p` prints free-form text. The sentinel is **per-issue**: it means "*this* issue is
done," and the bash wrapper decides what that triggers — close-and-exit (single-issue) or
close-and-advance-to-next-child (PRD mode). The agent never knows which mode it is in. Keep
the sentinel distinctive and instruct the agent to emit it only when every criterion on the
current issue is met. A stray match is caught by the completion gate (it re-fetches and
refuses to close while `- [ ]` remain); in PRD mode that refusal **aborts the whole run**
rather than skipping ahead, because later children depend on earlier ones. In the local-file
path a stray match just exits early, so the gate's checkbox grep is the only guard.

The agent also prints a `NOTE: <one line>` on its last line instead of editing
`progress.txt` itself; the wrapper extracts it (`sed -n 's/^NOTE: *//p' | tail -1`) and
appends it tagged with the issue number (`[#N] <note>`). This keeps the prompt mode-agnostic
while still producing a PRD-wide, per-child timeline — and avoids the double-logging that
would occur if the agent wrote an untagged line *and* the wrapper wrote a tagged one.

## Why fetch a fresh body at the completion gate

The per-iteration mirror (`.prd-current.md`) was fetched at the *top* of the iteration, before
the agent edited the issue body. So at completion it is stale by one edit. The gate re-fetches
into `.prd-final.md` to grep the agent's just-written checkbox state, not last iteration's.
