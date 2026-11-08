# First-run setup

Run this when no `to-outline config` block is in context, or when the user asks to set up the skill. It creates the pages the skill depends on and gives the user the config block to save. The hard guardrails in SKILL.md still apply, in particular: never create collections and never touch permissions or sharing.

## 1. Check the connector

Load the Outline tools as described in SKILL.md, then call `fetch` with resource `user` and ID `current_user`. If that fails, stop: the user needs to connect the Outline connector first. Note the Outline base URL from any URL the connector returns.

If the user already has a config block but a page in it cannot be fetched, do not start over. Report which page is missing and ask whether to recreate only that one.

## 2. Ask a few questions

Ask these in one message, with the defaults shown, and wait for the answers:

1. **Collections:** which collections will hold documentation, with a one-line scope each (for example "Homelab: servers, network, Docker"). Suggest two or three based on what the user has told you, if anything.
2. **Off-limits topics:** anything that must never be stored without asking first, such as an employer's systems or data. Default: none.
3. **Language:** the default language for pages. Default: the language the user writes in.
4. **Reserved collection name:** the collection that holds the skill's own pages. Default: `Wiki`.

Then call `list_collections`. For each collection the user named, including the reserved one, that does not exist yet, tell the user to create it in Outline (and to keep the reserved collection private) and wait until they say it is ready. Do not create collections yourself.

## 3. Create the pages

Before writing, call `list_collection_documents` on the reserved collection. If it already has a page titled "Routing" or "Page types and conventions", stop and ask whether to reuse it (just put its ID in the config) or replace it. Never overwrite silently.

Create these in the reserved collection, published, each starting with a review notice as described in SKILL.md:

1. **Routing**, from [starter/routing.md](starter/routing.md). Fill in one row per collection with the scope the user gave, plus the reserved collection's row. Under "Top-level pages", add one H3 per collection with no pages listed yet: the skill proposes parent pages as topics come in. Fill in or remove the "Out of scope" section based on the off-limits answer.
2. **Page types and conventions**, from [starter/conventions.md](starter/conventions.md). Set the language rule from the user's answer.
3. **One draft page per template**, from the files in [starter/templates/](starter/templates/), titled exactly by the template name (for example "Service or system"). Create them with `publish: false` so they stay out of search. The connector cannot create Outline templates, so the user turns each draft into a template.

In the Routing and conventions pages, fill in the placeholders (`<RESERVED>`, `<LANGUAGE>`, and `YYYY-MM-DD` as today's date) and remove the `<!-- -->` instruction comments. Copy the template drafts exactly as they are, including their literal `Last updated: YYYY-MM-DD` line. Do not change the content otherwise.

## 4. Hand over

Tell the user, in this order:

1. **Turn the drafts into templates.** For each draft: open it in Outline, use "Templatize" (or create a template in Settings > Templates and paste the body), keep the exact title as the template name, then delete the draft. Template names must match the Conventions page exactly.
2. **Save the config block.** Print it filled in, with the real document IDs:

   ```
   to-outline config
   - Wiki URL: <base URL>
   - Routing page: <Routing page ID>
   - Conventions page: <Conventions page ID>
   - Off-limits: <their answer, or remove this line>
   ```

   In Claude Code, it goes in `~/.claude/CLAUDE.md` (all projects) or a project's `CLAUDE.md`. In claude.ai, it goes in personal preferences or project instructions. Offer to add it to the `CLAUDE.md` file yourself if you have file access, but do not write it without a yes.
3. **Review the new pages.** Link the Routing and conventions pages. They carry review notices, and the user can edit either page at any time to change where pages go or how they are written.

Do not document any topics in the same run. Setup ends here.
