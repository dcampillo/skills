---
name: session-handoff
description: Write a handoff document that lets a fresh agent session continue the current work without the conversation history. Use when the user is ending or resetting a session and asks for a handoff, a session summary for the next agent, or notes to pick the work up later, optionally saying what the next session will focus on.
argument-hint: "What will the next session be used for?"
---

# Session handoff

Write a handoff document so a fresh agent, with none of this conversation, can continue the work.

## Where to save it

Save it as `handoff-<short-slug>-<YYYY-MM-DD>.md` in the operating system's temporary directory (for example `/tmp` on Linux and macOS, `%TEMP%` on Windows), not in the current workspace, so it never ends up in a commit. If you have a session scratchpad directory, that works too. Reply with the full path.

## What to include

- **Goal:** what the work is for, in one or two sentences.
- **State:** what is done, what is in progress, and what is next, in that order.
- **Decisions:** choices made and why, especially ones a newcomer might undo by accident.
- **Open questions and blockers:** anything waiting on the user or on something external.
- **Where things are:** branches, files, and commands that matter (how to run, test, or check the work).
- **Suggested skills:** skills the next agent should invoke, and when.

If the user passed an argument, treat it as what the next session will focus on: lead with what that session needs and trim what it does not.

## Rules

- **Reference, do not copy.** If something is already captured in another artifact (a PRD, plan, ADR, issue, commit, or diff), link it by path or URL instead of repeating it.
- **Redact sensitive information.** API keys, passwords, tokens, and personal information become `<redacted>`, with a note on where the real value lives if that matters.
- **Write for a stranger.** Spell out names and context the next agent cannot infer, and keep it as short as that allows.
