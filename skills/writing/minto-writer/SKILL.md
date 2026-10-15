---
name: minto-writer
description: Write or restructure emails, memos, reports, slides, or any business content using Barbara Minto's Pyramid Principle and the SCQA framework (Situation, Complication, Question, Answer). Use this skill whenever the user asks to write something in the "Minto framework," "Minto pyramid," "pyramid principle," or "SCQA," and also whenever the user asks for help writing a persuasive or executive-facing email, memo, update, report, or presentation and wants it clear, structured, and answer-first (BLUF, "bottom line up front"). Trigger even if the user just says "help me write an email to my exec team about X" or "structure my update on Y" without naming the framework explicitly, since that is exactly the kind of top-down, conclusion-first business writing this skill produces.
---

# Minto writer

Barbara Minto's Pyramid Principle is a way of organizing writing so that the reader gets the point immediately, then can go as deep as they want into the reasoning. It inverts how most people naturally write: instead of building up to a conclusion, you lead with it, then unpack it. This matters most in business writing, where the reader is busy and the writer's job is to save them time, not build suspense.

## The two building blocks

### SCQA: how you find the message

Before writing, work out the story in four moves. This is the thinking step, not the output.

- **Situation**: the stable, uncontroversial context the reader already agrees with. One or two sentences, no tension yet.
- **Complication**: what changed, what's at risk, or what problem/opportunity disrupts that situation. This is the reason the message exists at all. If you cannot name a real complication, ask the user what triggered the need for this piece of writing.
- **Question**: the question the reader is now implicitly asking, given the complication. Usually something like "so what should we do?" or "does this affect us?"
- **Answer**: the direct answer to that question. This becomes the headline/opening line of the piece.

Do this thinking out loud with the user if the complication or answer isn't obvious from what they've told you. A pyramid built on a fuzzy SCQA produces a fuzzy piece of writing, so it's worth a sentence or two of back and forth before drafting rather than guessing.

### The pyramid: how you lay out the message

Once you have the SCQA answer, structure the piece top-down:

1. **Governing message** (top of the pyramid): the Answer from SCQA, stated as a single clear sentence. This is the first thing the reader sees, not the last.
2. **Supporting arguments** (2 to 4 of them): the main reasons the governing message is true, or the main pillars of the recommendation. These should be mutually exclusive-ish and jointly cover the argument (a rough MECE check: could you group all your supporting data under just these headings without a leftover pile?).
3. **Supporting data/evidence**: under each argument, the facts, numbers, or examples that back it up. Only as much detail as the reader needs to trust the argument, not everything you know.

Each level answers "why?" or "how?" for the level above it, and the levels above always summarize the levels below rather than the other way round.

## Workflow

### Step 1: Interview until the SCQA is solid, don't draft on a guess

A pyramid is only as good as the message at its top, and the message at its top only comes from a clear SCQA. So before writing a single line of the actual piece, work through Situation, Complication, Question, and Answer with the user as a short interview, the same way you'd stress-test a plan: one question at a time, and offer your best-guess answer alongside each question so the user can just confirm or correct rather than having to compose an answer from scratch.

Go through this checklist and only stop asking once every item is actually answered, not once you've asked enough questions to feel thorough:

- **Situation**: what's the context the reader already knows and agrees with? (Often obvious from what the user already said, in which case state it back to them instead of asking.)
- **Complication**: what changed, went wrong, or created the need for this piece of writing? This is usually the part people under-explain. If the user's request doesn't make the complication clear, ask directly, e.g. "what's driving this, what happened that means we need to say something now?"
- **Answer**: what's the actual governing message, the one sentence the reader should walk away with? If the user hasn't told you their conclusion or recommendation, ask for it rather than inferring one. Inferring a plausible-sounding conclusion from thin context is the single biggest way this skill goes wrong.
- **Audience and stakes**: who's reading this, and what do you want them to do or believe afterward? This shapes tone and which supporting arguments matter.
- **Evidence**: what facts, numbers, or examples back up the answer? Only use what the user provides or what you've found together, never invent plausible-sounding data, causes, or figures to fill a gap.

Keep the questions tight and skippable: if the user's original message already answers one of these, don't re-ask it, just restate your understanding of it in a half-sentence so they can correct you if you got it wrong ("sounds like the complication is X, is that right?"). If they say "just write something" or clearly want a fast first draft over a Q&A, honor that: write the best version you can from what's there, mark any assumed piece explicitly (e.g. "I'm assuming the cause is X, let me know if it's actually Y"), and offer to refine once they react to it. The interview is the default because a guessed-at governing message wastes more of the user's time fixing a wrong draft than a few quick questions would have, but the user's stated preference for speed always wins.

### Step 2: Draft

1. **Draft the governing message.** One sentence. If it takes more than one sentence, the message probably isn't sharp enough yet, so tighten it before moving on.
2. **Pick 2 to 4 supporting arguments** that carry the governing message. Resist the urge to list everything the user gave you; select and group instead.
3. **Attach evidence** under each argument, using the user's own facts and figures wherever provided.
4. **Write in the format the user needs** (email, memo, slide outline, report section) using the structure below, matching whatever tone the user asks for.

## Output structure

Adapt this to the medium, but the top-down order never changes: message first, then arguments, then evidence. Never bury the governing message in a middle paragraph or save it for the end.

**Email or memo:**
```
Subject: [governing message, compressed to a few words]

[Governing message as the opening line/paragraph — the reader should
be able to stop reading here and still know the point.]

[Supporting argument 1, with its evidence]
[Supporting argument 2, with its evidence]
[Supporting argument 3, with its evidence — if needed]

[Optional: what you need from the reader / next step]
```

**Report or longer document:**
```
# [Title]

## Bottom line
[Governing message, one or two sentences]

## [Supporting argument 1 as a heading]
[evidence, detail]

## [Supporting argument 2 as a heading]
[evidence, detail]

## [Supporting argument 3 as a heading, if needed]
[evidence, detail]
```

**Slide/presentation outline:** one supporting argument per slide, each slide headline written as a full sentence that states the point of that slide (not a topic label like "Q3 results" but a claim like "Q3 revenue grew 12% driven by EMEA expansion"), so that reading just the headlines top to bottom tells the whole story.

## Common failure modes to avoid

- **Burying the lede.** If the reader has to get through background before finding out what you actually want to say, restructure. The most common fix is to take the last paragraph (where the conclusion usually ends up in a first draft) and move it to the top.
- **Too many supporting arguments.** More than four means the arguments probably aren't distinct enough or need grouping into higher-level categories.
- **Arguments that are just topics, not claims.** "Budget" is a topic. "The budget overrun is fully explained by the vendor price increase" is a claim. Supporting arguments should be assertable sentences, not headers.
- **Evidence with no argument above it, or an argument with no evidence below it.** Every level needs both a parent (why does this matter) and, where relevant, children (what proves it).

## A short example

Raw input from user: "We're behind on the migration, need more budget, client is getting impatient, engineering says infra costs doubled, we lost two people from the team in Q2."

SCQA:
- Situation: the platform migration is underway and was budgeted at X.
- Complication: costs have doubled and headcount dropped, putting the timeline and client relationship at risk.
- Question: what do we need to get this back on track?
- Answer: we need an additional budget allocation and a two-week timeline reset.

Governing message: "We need an additional [amount] and a two-week extension to deliver the migration; without it we risk missing the client deadline entirely."

Supporting arguments: (1) infrastructure costs have doubled since the original estimate, (2) losing two engineers in Q2 cut delivery capacity by roughly a third, (3) the client relationship is more at risk from a silent slip than from a proactive ask now.

This is the shape to aim for regardless of topic: one claim at the top, a handful of claims under it, evidence under those.
