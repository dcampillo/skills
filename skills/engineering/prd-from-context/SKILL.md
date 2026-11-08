---
name: prd-from-context
description: Turn what is already known in the current conversation and codebase into a PRD (product requirements document) and publish it to the project's issue tracker, or save it as a Markdown file when there is no tracker. Use when the user asks to write up, capture, or publish a PRD from the current discussion, for example "turn this into a PRD", "write the PRD for what we just discussed", or "file a PRD issue for this". Do not use it to interview the user about a new idea from scratch.
---

# PRD from context

Synthesize a PRD from what you already know: the conversation so far and the codebase. Do not interview the user about requirements. The only question you ask is the module check in step 2, plus where to publish if that is unclear.

## Process

### 1. Understand the codebase

Explore the repo if you have not already. If the project has a domain glossary (for example `GLOSSARY.md` or `CONTEXT.md`), use its vocabulary throughout the PRD. If it has architecture decision records (ADRs) in the area you are touching, respect them, and say so in the PRD where they constrain a decision.

### 2. Sketch the modules and check them

List the major modules you would build or modify. Look for chances to extract deep modules that can be tested in isolation. A deep module hides a lot of functionality behind a simple, stable, testable interface; a shallow one has an interface nearly as complex as what it does.

Show the list to the user in one message and ask two things:

- Do these modules match what they expect?
- Which modules do they want tests written for?

Wait for the answer before writing the PRD.

### 3. Find where to publish

Pick the first that applies:

1. **The user named a destination** (a tracker, a project, a file path). Use it.
2. **The project says where.** Check `CLAUDE.md`, `AGENTS.md`, or `CONTRIBUTING.md` for an issue tracker, project key, or labels to use.
3. **The repo's remote has an issue tracker you can reach:** GitHub (`gh`, when `gh repo view --json hasIssuesEnabled` says issues are enabled) or GitLab (`glab`). For Jira, Linear, or another tracker, use a connected tool for it if one is available.
4. **Nothing found.** Save the PRD as `docs/prd/<short-slug>.md` in the repo, or ask the user if the repo has an obvious other place for docs.

If more than one destination is plausible, ask once which to use.

**Labels.** Apply a label only if the project names one for PRDs or agent-ready work (for example `prd` or `ready-for-agent`) and it already exists on the tracker. Never create labels. If none applies, publish without one and mention it.

### 4. Write and publish

Write the PRD using the template below. Title it after the feature, in plain words. Publish it, then reply with the link (or file path), the label applied if any, and a one-line summary.

## PRD template

<prd-template>

## Problem statement

The problem the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User stories

A long, numbered list of user stories, each in the form:

1. As a <actor>, I want <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see the balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

Cover every aspect of the feature, including edge cases, errors, and the less obvious actors (admins, operators, other systems).

## Implementation decisions

The decisions that were made, such as:

- The modules that will be built or modified
- The interfaces of those modules that will change
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do not include file paths or code snippets, because they go stale quickly.

Exception: if a prototype produced a snippet that states a decision more precisely than prose can (a state machine, reducer, schema, or type shape), inline it within the relevant decision and note that it came from a prototype. Keep only the parts that carry the decision.

## Testing decisions

- What makes a good test here (test external behavior, not implementation details)
- Which modules will be tested, as agreed in step 2
- Prior art: similar tests already in the codebase

## Out of scope

What this PRD deliberately does not cover.

## Further notes

Anything else worth knowing about the feature.

</prd-template>
