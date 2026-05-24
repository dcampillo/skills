# Contributor guide

This repo is a Claude Code **plugin** (`dcampillo-skills`) that bundles a collection
of skills. This file documents how to add and maintain skills here. It is guidance for
an agent or human working **inside this repo** — it is not loaded as context when the
plugin is installed elsewhere.

## Layout

Skills live under `skills/<category>/<skill-name>/`, one directory per skill:

```
skills/
└── engineering/
    └── ralph-it/
        ├── SKILL.md       # required: the skill definition
        └── REFERENCE.md   # optional: bundled resource, referenced by relative path
```

`<category>` groups related skills (e.g. `engineering`, `productivity`). Pick the
category that best fits; create a new one when none applies.

## Adding a skill

1. **Create the directory.** `skills/<category>/<name>/` where `<name>` is
   lowercase-kebab-case and matches the skill's `name` frontmatter.

2. **Write `SKILL.md`** with YAML frontmatter:

   ```markdown
   ---
   name: my-skill
   description: One sentence on what it does, followed by the trigger conditions ("Use when the user wants to …", plus quoted trigger phrases). This text is how Claude decides to load the skill, so make it specific.
   ---

   # My Skill

   …instructions for the agent…
   ```

   - `name` must equal the directory name.
   - `description` is the only signal Claude uses to route to the skill — front-load
     *what it does* and *when to use it*, including the literal phrases a user might say.
   - Keep `SKILL.md` focused; push long reference material into sibling files (e.g.
     `REFERENCE.md`) and link them with **relative** paths so they survive a move.

3. **Register it** in `.claude-plugin/plugin.json` by adding its directory to the
   `skills` array:

   ```json
   "skills": [
     "./skills/engineering/ralph-it",
     "./skills/<category>/<name>"
   ]
   ```

   The array is explicit because of the nested `<category>/` layout — the default
   `skills/` scan only auto-discovers skills that are direct children of `skills/`, so
   every skill here must be listed.

4. **Add it to the README catalog** under the matching category heading in
   [README.md](README.md).

5. **Bump `version`** in `plugin.json` if you want installers to pick up the change.

## Conventions

- One skill = one directory. Don't put two skills in one folder.
- Paths in `plugin.json` are relative and start with `./` and point at the **directory**
  containing `SKILL.md`, not the file.
- Bundled resources are referenced from `SKILL.md` by relative path so they keep working
  if the skill directory is moved or the repo is reorganised.
