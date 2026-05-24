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
| [`ralph-it`](skills/engineering/ralph-it/) | Scaffolds a **Ralph Loop** — a self-driving bash loop that repeatedly invokes Claude to implement one task at a time until the work is done. Drives a single GitHub issue or a whole PRD's linked child issues in sequence, with a hardened `ralph.sh`, completion gates, and a no-progress guard. | You want to set up an unattended implement → test → commit loop against a GitHub issue or PRD ("ralph it", "grind through this PRD"). |

_More skills will be added here as the collection grows._

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
