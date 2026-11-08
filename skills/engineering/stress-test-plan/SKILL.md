---
name: stress-test-plan
description: Question the user one decision at a time about a plan, design, or proposal until every open branch is resolved, offering a recommended answer for each question, then summarize the decisions. Use when the user asks to stress-test, pressure-test, challenge, or poke holes in a plan or design, or wants to be questioned hard on it before committing.
---

# Stress-test a plan

Interview the user about every part of the plan until you both share the same understanding of it. The goal is a plan with no unexamined decisions, not a long conversation.

## How to question

- **Walk the decision tree.** Start with the decisions everything else depends on (goal, scope, constraints), then follow each branch down. Settle a decision before asking about the ones that depend on it.
- **One question at a time.** Wait for the answer before asking the next one. A batch of questions gets shallow answers.
- **Recommend an answer every time.** Give your recommendation and the reason in a sentence, so the user can just agree, adjust, or push back.
- **Look it up instead of asking.** If the answer is in the codebase, the docs, or another source you can read, check it yourself and state what you found. Only ask the user about intent, preferences, and trade-offs.
- **Push on weak spots.** Probe vague answers, untested assumptions, missing failure handling, and conflicts with earlier answers. Do not accept "we'll figure it out later" for a decision other parts depend on: name what it blocks.
- **Stay on the plan.** Do not start implementing anything.

## When to stop

Stop when every branch is resolved or explicitly parked, or when the user says to stop. Then reply with a short summary:

- **Decisions:** each one with its answer
- **Parked:** open questions deliberately left for later, with what each one blocks
- **Risks:** anything the questioning surfaced that still worries you

Offer to save the summary where the user keeps plans (a file in the repo, an issue, or a doc), but do not write it without a yes.
