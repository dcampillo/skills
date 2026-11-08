# dcampillo-skills

A personal collection of [Claude Code](https://code.claude.com) skills, packaged as
an installable plugin. Each skill teaches Claude how to perform a specific workflow;
they activate automatically when your request matches a skill's trigger, or you can
invoke one explicitly.

## Install

Add this repo as a plugin from within Claude Code:

```
/plugin install dcampillo/skills
```

Once installed, the skills below become available. Claude loads each one on demand
based on what you ask for.

## Skills

### Engineering

| Skill | What it does | Use when |
| --- | --- | --- |
| [`ralph-it`](skills/engineering/ralph-it/SKILL.md) | Scaffolds a **Ralph Loop** — a self-driving bash loop that repeatedly invokes Claude to implement one task at a time until the work is done. Drives a single GitHub issue or a whole PRD's linked child issues in sequence, with a hardened `ralph.sh`, completion gates, and a no-progress guard. | You want to set up an unattended implement → test → commit loop against a GitHub issue or PRD ("ralph it", "grind through this PRD"). |
| [`handoff`](skills/engineering/handoff/SKILL.md) | Compacts the current conversation into a handoff document in the OS temp directory, so a fresh agent can pick up the work. References existing artifacts (PRDs, issues, commits) instead of copying them, suggests skills to invoke, and redacts secrets. | You are ending a session and want another agent to continue it, optionally telling it what the next session will focus on (`/handoff <focus>`). |

### Writing

| Skill | What it does | Use when |
| --- | --- | --- |
| [`human-voice`](skills/writing/human-voice/SKILL.md) | Helps you write with a natural, authentic human voice that feels genuine and conversational while maintaining clarity and impact. | You want to improve the tone and authenticity of your writing, make it more relatable, or ensure your voice comes through clearly in prose, emails, or documentation. |
| [`minto-writer`](skills/writing/minto-writer/SKILL.md) | Writes or restructures emails, memos, reports, and slides using Barbara Minto's Pyramid Principle and the SCQA framework (Situation, Complication, Question, Answer), so the reader gets the answer first and the supporting reasoning underneath. | You need a clear, answer-first (BLUF) message for executives or other busy readers ("write this in the Minto pyramid", "use SCQA", "help me write an email to my exec team about X"). |
| [`tldr`](skills/writing/tldr/SKILL.md) | Produces a structured TLDR of pasted text or a URL: a short headline, what the source is, 3 to 6 key points, and one key takeaway, then offers to save it as markdown or PDF. | You want the gist of an article, doc, or link ("TLDR", "summarize this", "what's this about?"). |
| [`to-outline`](skills/writing/to-outline/SKILL.md) | Writes or updates structured pages in your Outline wiki and stores the real config files and scripts next to them, verbatim with secrets redacted. Routing, page types, and templates live in the wiki itself; pages publish right away with a "Needs review" notice. Includes a first-run setup that creates the pages it needs. | You want something documented in Outline ("add this to the wiki", "store this config", "what needs review?"), or you want to set the skill up for your wiki. |

### SEO

| Skill | What it does | Use when |
| --- | --- | --- |
| [`topical-authority-map`](skills/seo/topical-authority-map/SKILL.md) | Builds a pillar-and-cluster topical authority map for a niche, exported as a CSV with titles, slugs, search intent, estimated volume, and keyword difficulty. | You want to plan a site's content around a niche, build topical authority, or need a keyword/content map ("topical map", "content plan", "what should I write to rank"). |

## Repository layout

```
.
├── .claude-plugin/
│   └── plugin.json          # plugin manifest (lists each skill)
├── skills/
│   └── <category>/
│       └── <skill-name>/
│           └── SKILL.md     # the skill (plus any bundled resources)
├── CLAUDE.md                # contributor guide for adding skills
├── LICENSE
└── README.md
```

## Contributing

See [CLAUDE.md](CLAUDE.md) for the conventions on adding a new skill to this repo.

## License

[MIT](LICENSE) © David Campillo
