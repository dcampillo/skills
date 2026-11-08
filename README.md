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

### Writing

| Skill | What it does | Use when |
| --- | --- | --- |
| [`human-voice`](skills/writing/human-voice/SKILL.md) | Helps you write with a natural, authentic human voice that feels genuine and conversational while maintaining clarity and impact. | You want to improve the tone and authenticity of your writing, make it more relatable, or ensure your voice comes through clearly in prose, emails, or documentation. |
| [`minto-writer`](skills/writing/minto-writer/SKILL.md) | Writes or restructures emails, memos, reports, and slides using Barbara Minto's Pyramid Principle and the SCQA framework (Situation, Complication, Question, Answer), so the reader gets the answer first and the supporting reasoning underneath. | You need a clear, answer-first (BLUF) message for executives or other busy readers ("write this in the Minto pyramid", "use SCQA", "help me write an email to my exec team about X"). |
| [`tldr`](skills/writing/tldr/SKILL.md) | Produces a structured TLDR of pasted text or a URL: a short headline, what the source is, 3 to 6 key points, and one key takeaway, then offers to save it as markdown or PDF. | You want the gist of an article, doc, or link ("TLDR", "summarize this", "what's this about?"). |

### Personal

Skills tied to my own setup (Outline wiki, homelab). Useful mostly to me, but available in the plugin.

| Skill | What it does | Use when |
| --- | --- | --- |
| [`to-wiki`](skills/personal/to-wiki/SKILL.md) | Writes or updates structured documentation in my Outline wiki, stores the actual config files and scripts next to it, publishes, and marks pages for review. | You explicitly ask to document something in the wiki ("add this to Outline", "/to-wiki"), or ask what wiki pages need review. |

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
