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
| [`herdr`](skills/engineering/herdr/SKILL.md) | Customizes and drives the **Herdr** desktop app (the `herdr-app` window and `herdr-core`/`hc`): layouts, `keys.json` shortcuts, settings, themes, mods, `programs.json`, and machines such as ssh hosts and WSL distros. Reads Herdr's live `AGENTS.md` manual first and checks every edit for errors. | You want to change Herdr's shortcuts, look, layouts or mods, or add a machine. Runs only when you type `/herdr`. |
| [`session-handoff`](skills/engineering/session-handoff/SKILL.md) | Writes a handoff document to the OS temp directory so a fresh agent can continue the work: goal, state, decisions, open questions, where things are, and suggested skills. Links existing artifacts (PRDs, issues, commits) instead of copying them, and redacts secrets. | You are ending a session and want another agent to pick up the work, optionally saying what the next session will focus on (`/session-handoff <focus>`). |
| [`prd-from-context`](skills/engineering/prd-from-context/SKILL.md) | Turns what the conversation and codebase already establish into a PRD (problem, solution, user stories, implementation and testing decisions, out of scope) and publishes it to the project's issue tracker (GitHub, GitLab, or a connected tracker), or saves it as Markdown when there is none. Checks the planned modules with you before writing. | You have discussed a feature and want it captured as a PRD ("turn this into a PRD", "file a PRD issue for this"). |
| [`stress-test-plan`](skills/engineering/stress-test-plan/SKILL.md) | Questions you one decision at a time about a plan or design, with a recommended answer for each, checking the codebase or docs itself where it can. Ends with a summary of decisions, parked questions, and risks. | You want a plan or design challenged before committing to it ("stress-test this plan", "poke holes in my design"). |

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
