## Purpose

This page defines how documentation pages are shaped and written. The to-outline skill reads it at the start of every run. The section lists of each page type live in the Outline templates named below, which are the only place they are defined. Safety rules (secrets, off-limits content, sharing, deletion) live in the skill itself and cannot be changed from this page.

## Page types

Choose exactly one page type per page. The template name is the exact name the skill looks up.

| Type | Template | Use for |
|------|----------|---------|
| Service or system | Service or system | A container, VM, device, tool, or anything that "runs somewhere". |
| How-to or runbook | How-to or runbook | A procedure someone can follow step by step. The title starts with a verb: "Restore the NAS backup". |
| Reference or decision | Reference or decision | An IP plan, a design choice, a policy note, or any fact sheet. |
| Topic overview | Topic overview | General documentation of a subject that is not a system, a procedure or a decision: a product or vendor catalog, research notes, material compiled from external sources. "Scope and sources" says what was covered and where it came from (URLs, capture date, method); "Content" is free-form, with H3 subsections and tables as needed; "Open questions" lists gaps, inconsistencies in the sources, and follow-ups. |

Do not invent new types. A new type means a new template, then a new row here.

## Conventions for every page

* **Language:** <LANGUAGE>, even if the material is described in another language. Another language only if explicitly requested, for that one page.
* **Case:** sentence case for titles and headings. Proper nouns and acronyms keep their normal casing (Docker, NAS, VLAN).
* **Titles:** short and noun-based, naming the thing: "Nginx reverse proxy", not "How I set up Nginx". No dates or versions in titles. If a page with a matching name exists, update it instead of creating a variant.
* **Body:** never start with an H1 heading, because Outline stores the title separately. Start with body text or an H2. The only exception is the review notice, which always comes first.
* **Code and config:** fenced code blocks with a language tag when there is one. Stored configuration files follow the rules in the skill: verbatim, a caption above each block (host, full path, capture date), one file on one page, and secrets redacted.
* **Skip empty sections:** if a section has nothing to say yet, leave it out instead of writing "N/A".
* **Last updated line:** end every page with a final line `Last updated: YYYY-MM-DD` (the date of the write), and replace it on every update.
* **Related section:** list links to existing pages found during the search. Leave the section out if there are none.

Last updated: YYYY-MM-DD
