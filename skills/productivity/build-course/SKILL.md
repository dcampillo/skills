---
name: build-course
description: >
  Build a standalone, multi-lesson training course as Markdown files that a student or
  employee can work through step by step on their own. Every claim is grounded in real
  sources — supplied documents or researched primary sources — never in model knowledge.
  Trigger on requests like "build a course", "create a training", "build a training course",
  "make an onboarding course", "write a tutorial for new hires", "turn these docs into a
  training", "create a curriculum". Do NOT trigger on "explain X to me", "teach me X",
  "document this module", "write a README", or ordinary documentation and explanation
  requests — those are not course-building.
---

# Build a course

Produces a **self-contained Markdown course**: a syllabus, numbered lessons, a glossary and
cheat sheets, a capstone assessment, and a source register. The learner is not the person
running this skill — it is a student or employee who will work through the files alone,
later, without you present. Everything they need must be in the files.

Two rules govern everything below:

1. **No parametric knowledge.** If a claim is not traceable to a source recorded in
   `SOURCES.md`, it does not go in a lesson. Research happens *before* drafting, not during.
2. **Never silently compress.** When the topic is bigger than the time budget, say so and
   let the author choose what to cut.

The run is one pass with two approval gates and a verification pass:

```
intake → GATE 1: BRIEF.md → research → GATE 2: outline → generate → verify → hand over
```

Templates live beside this file: [BRIEF-TEMPLATE.md](./BRIEF-TEMPLATE.md),
[LESSON-TEMPLATE.md](./LESSON-TEMPLATE.md), [SOURCES-TEMPLATE.md](./SOURCES-TEMPLATE.md).
Copy them; do not re-derive their structure from memory.

## Step 0 — Check for an existing course

Before intake, look for `BRIEF.md` and `SOURCES.md` in the target directory. If they exist,
this is a re-run: **skip intake**, read both, and ask the author which they want:

- **Extend** — add lessons to the existing course, reusing the brief and source register.
- **Refresh** — re-check sources whose `checked-on` date is stale, and correct any lesson
  whose facts have changed.
- **Regenerate** — rebuild from the brief, discarding the current lessons.

Confirm before overwriting anything. Otherwise continue to intake.

## Step 1 — Intake interview

Interview the author until the brief is airtight. Ask in rounds: put every question you can
ask *now* in one message, numbered, with your recommended answer; wait for replies; then ask
what those answers unblocked. A wrong audience wrecks every lesson downstream, so this is
worth blocking on.

**Always required — do not proceed without these:**

| Field | What you need |
| --- | --- |
| Audience | Role, and specifically what they already know. "New backend hires who know Python but have never used Kafka." |
| Outcome | What they can **do** afterwards that they cannot now, stated observably. Not "understand X" — "configure and verify a consumer group". |
| Time budget | Total learner time, e.g. 90 minutes, half a day. |
| Source mode | **Web research** or **supplied corpus** — one or the other, chosen here. |
| Corpus location | If corpus mode: paths, globs, URLs, or pasted text. |
| Web supplementation | If corpus mode: may you fill gaps from the web, or must gaps stop the run? |

**Ask when relevant:**

- **Environment** (any hands-on topic): do learners have a laptop, sandbox account, admin
  rights, the tool installed? Hands-on tasks must be doable with what they actually have.
- **Delivery target** (if the author mentions a platform): GitHub, Notion, an LMS, print.
  Affects how much you can rely on `<details>` blocks rendering.
- **Graded or self-paced** — ask once, cheaply. Graded means instructor-led and changes the
  capstone and README.
- **Tone and language** — offer the menu, do not ask open-ended:
  neutral-instructional (default), friendly-conversational, formal-corporate,
  terse-reference; plus free text. And ask the **output language** (default English).
  Sources are always cited in their original language whatever the course language.
- **Stale-by date and review owner** — who owns keeping this current.

Then write `BRIEF.md` from the template and **stop for approval (gate 1)**.

## Step 2 — Research

Nothing is drafted until `SOURCES.md` exists. Lessons may only cite what is in it.

### Corpus mode

Run a discovery pass over the supplied material first. Report back a **coverage summary**
before any drafting: what you found, what looks authoritative, and — critically — what the
outcome requires that the corpus does not cover.

Then apply the gap policy the author chose at intake:

- **Web supplementation authorized:** fill the gap, and mark those entries `external` in
  `SOURCES.md` so internal-policy content stays traceable to internal documents.
- **Not authorized:** stop and list the gaps for the author. This is the cheapest possible
  moment for them to hand over the missing document. If they decline to fill a gap, the
  affected lesson carries a visible callout:

  ```markdown
  > **Gap:** not covered by the supplied sources. <what is missing>
  ```

Internal sources are cited by repo path plus section heading, never a bare filename.

### Web mode

Research primary sources. Respect the quality hierarchy — **official documentation,
standards, specifications and primary records outrank vendor blogs, which outrank
tutorials and forum posts.** A tutorial is acceptable for showing *how* something is
commonly done; it is not acceptable as the authority for *what is true*.

### Both modes

For anything non-trivial, fan out **parallel sub-agents, one per outline topic**, each
returning cited findings rather than prose. Record every source in `SOURCES.md` with its
tier and a `checked-on` date — training documents go stale, and that date is what a refresh
run reads.

**Watch for claims a source is credited with but never makes.** Derived material routinely
attributes its own conventions to an authority — "Google says put the brand last" when the
documentation says either end is fine. These are harder to catch than outright errors,
because the claim is plausible and the citation looks valid. When the corpus attributes
something to a primary source, check the primary source says it. Where a convention is worth
teaching anyway, teach it as reasoning and say plainly whose reasoning it is.

**When sources disagree:** resolve by tier and record the conflict in the `SOURCES.md`
notes column. When both conflicting sources are *internal*, escalate to the author at gate
2 — that is not a research problem, it is a signal their documentation is broken, and only
they can decide which one a new hire should follow. Where the ambiguity is genuinely part
of the job, teach it as a callout in the lesson.

## Step 3 — Outline (gate 2)

Derive the lesson breakdown from the time budget: **one tangible skill per lesson, 10–15
minutes each.** Propose the breakdown — title, outcome, time, sources it draws on — and get
approval before drafting.

This gate is where trade-offs get decided:

- **Scope exceeds the budget:** present it honestly. "This outcome needs about four hours.
  Here is the 90-minute version and here is what it drops." Offer `further-reading.md` as
  the release valve for what falls outside. Never quietly make every lesson shallower.
- **Internal source conflicts** surfaced in research.
- **Structure:** flat numbered lessons up to ~10. Past that, group into
  `module-01-<name>/` directories, each with its own mini-README. Past ~20 lessons,
  recommend splitting into separate courses with prerequisite links between them.

## Step 4 — Generate

Write to `./<course-slug>/` under the current directory unless the author redirected it at
intake.

**The capstone and the reference documents are course files like any other.** They carry
footnotes, they get a sources block, and they follow the same citation rule as a lesson. A
capstone that states facts without citations is the single most common way this course fails
review.

```
<course-slug>/
├── README.md              # syllabus and progress checklist
├── BRIEF.md               # the approved brief
├── SOURCES.md             # source register
├── 01-<dash-case>.md      # lessons (or module-01-<name>/ when large)
├── 02-<dash-case>.md
├── reference/
│   ├── glossary.md        # mandatory when the topic has its own jargon
│   └── <cheat-sheet>.md   # syntax, commands, steps worth compressing
├── assessment.md          # capstone
├── trainer/               # graded courses only — rubric and answer key
└── further-reading.md     # only when scope was trimmed at gate 2
```

### `README.md` — the syllabus

Course title · who it is for · what you will be able to do at the end · prerequisites and
required environment · total time · the lesson checklist (`- [ ] 01 — Title (12 min)`) ·
how to use this course (work in order, do the tasks, do not skip the self-checks) · link to
the capstone · last-reviewed date and review owner.

Add a **For trainers** section only when the author said graded or instructor-led.

### Lessons

Follow [LESSON-TEMPLATE.md](./LESSON-TEMPLATE.md). Mandatory in every lesson: the outcome,
the content, **Check yourself**, the sources block, and navigation links. **Do it** is
required for any skill-based lesson; for a pure-knowledge topic it may degrade to applying
the idea to the learner's own situation, but push hard against dropping it — a lesson with
no doing produces no skill. If the lesson's stated outcome starts with a verb the learner
performs ("look at any page and say what…"), it needs a Do it, however conceptual the
content felt to write.

Retrieval practice, per lesson:

- 2–4 **Check yourself** questions in `<details>` blocks, testing this lesson.
- Plus **one deliberate callback** to an earlier lesson. This is the cheapest way to buy
  long-term retention in a static document.
- Courses over 6 lessons get a **cumulative checkpoint** at the midpoint.
- Quiz answer options are equal in length with no formatting tells. Never let the shape of
  an option give away the answer.

### Citations

Markdown footnotes, resolving in the lesson's own sources block:

```markdown
Kafka retains messages for seven days by default.[^kafka-retention]

[^kafka-retention]: [Kafka broker configs](https://kafka.apache.org/documentation/#brokerconfigs) — checked 2026-09-02.
```

Every specific claim — numbers, commands, API names, policy rules, defaults — carries one.
Every footnote resolves to a `SOURCES.md` entry. No lesson ships with an uncited numeric or
procedural claim.

### `reference/`

A **glossary is mandatory** whenever the topic has its own jargon. Once it exists, every
lesson uses its terms consistently — no synonyms drifting in. Add cheat sheets where the
topic has syntax, commands, or a repeatable procedure worth compressing to one page.
Lessons get read once; references get revisited.

### `assessment.md`

One realistic end-to-end task that exercises the whole course, with an observable success
condition, plus a rubric of criteria and levels. This is the only part of the course that
proves the brief's outcome was met, so it must map back to that outcome directly.

Self-paced: rubric lives in `assessment.md`, self-scored. **Graded: rubric and answer key
move to `trainer/`** and the learner's copy carries only the task.

### Prose style

Second person, present tense. Plain words. Short paragraphs. Define jargon on first use and
then use it consistently. State things directly rather than hedging. Cut filler. Apply the
author's tone and language choice from the brief on top of these defaults.

## Step 5 — Verify

Dispatch a **fresh sub-agent with no drafting context** to review the finished course
against the checklist below. This matters: a self-review by the drafting context remembers
*why* it believed something and waves it through, which is exactly where the no-parametric-
knowledge rule quietly fails. The reviewer gets the files and the checklist, nothing else.

- [ ] Every factual claim carries a footnote — **in every file**, including the capstone, the
      cheat sheets and the glossary. These are the ones that get missed.
- [ ] Every footnote resolves to a `SOURCES.md` entry with a tier and a `checked-on` date, and
      every `SOURCES.md` row's "Cited in" column matches where it is actually cited.
- [ ] No claim is attributed to a source that does not state it — especially superlatives
      ("the main source", "the most important field") layered onto a source that ranks nothing.
- [ ] Every lesson has an outcome, content, Check yourself, sources, and nav links.
- [ ] Every skill-based lesson has a Do it task with an observable pass condition.
- [ ] Each lesson after the first carries a callback question to an earlier lesson.
- [ ] Internal links resolve; lesson numbering is contiguous.
- [ ] Glossary terms are used consistently across lessons.
- [ ] No lesson exceeds its time estimate; estimates sum to the brief's budget.
- [ ] The capstone maps to the brief's stated outcome.
- [ ] Any gap callouts are still accurate and none were silently dropped.

Fix what it finds, then hand over: where the course lives, how long it takes, what it
assumes the learner already knows, and anything the author still needs to fill in.
