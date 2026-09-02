---
name: topical-authority-map
description: Build a topical authority map for SEO as a downloadable CSV file with pillar content topics and supporting cluster content topics, including search intent, suggested titles, URL slugs, estimated search volume, and keyword difficulty. Use this skill whenever the user asks for a topical map, topical authority map, content map, pillar and cluster plan, keyword map, SEO content plan, content hub structure, or wants to plan blog/website content around a niche, even if they only mention a niche and ask "what should I write about to rank". Trigger for any language or country market.
---

# Topical authority map

Create a pillar-and-cluster topical map that helps a site build topical authority in a niche: complete coverage of one subject area so search engines treat the site as an authoritative source on the topic.

## Inputs

Collect these from the user's message. Ask only if the niche is missing; default the rest.

| Input | Default |
|---|---|
| Niche | required, ask if absent |
| Language | English (or the language the user wrote in) |
| Country | infer from language/context, else global |
| Pillar topics | 5 |
| Clusters per pillar | 10 |

## Process

1. **Research the niche.** Run a few web searches on the niche in the target language/country to understand what subtopics, brands, regulations, and local terms matter to that market. This grounds the map in real search behavior rather than generic guesses. Look for any available search volume data while researching.
2. **Choose pillars.** Pick broad, head-term subtopics that together cover the niche completely. Each pillar should be a topic a site could own with a comprehensive hub page.
3. **Go deep, not wide, for clusters.** Each pillar's clusters must drill into that pillar (subtopics, questions, comparisons, how-tos, local angles), not drift into adjacent niches. Topical authority comes from exhausting a pillar's subtopics. Clusters must not overlap or duplicate each other across pillars.
4. **Assign search intent.** Use exactly these labels: informational, commercial, transactional, navigational. Most clusters are informational; include commercial and transactional where buying behavior exists, and at least one navigational row in the full map (e.g., a known brand, tool, or institution users search by name in that market).
5. **Write titles and slugs.** Titles in the target language, compelling and keyword-bearing, fit for the country's audience and culture. Slugs: lowercase, hyphen-separated, ASCII (transliterate non-Latin scripts), no stop-word bloat, e.g. `best-vfr-flight-planning-apps`.
6. **Estimate volume and difficulty.** Search Volume: a monthly estimate or range (e.g. `1K-10K`) based on web research where available, otherwise informed estimation. Keyword Difficulty: Low, Medium, or High. These are estimates, not Keyword Planner exports; say so once in the chat message (never inside the CSV) and recommend validating in Google Keyword Planner, Ahrefs, or Semrush before committing resources.

## Output format

Write a CSV file named `topical-map-<niche-slug>.csv` and present it to the user.

Rules for the CSV:

- Header row, then exactly these columns in this order: `Pillar Content,Cluster Content,Search Intent,Suggested Title,Suggested URL Slug,Search Volume,Keyword Difficulty`
- Pillar Content appears only on the first row of each pillar's cluster block; leave it empty on the remaining rows of that block.
- No commentary, markdown, code fences, or extra spaces around commas inside the file. Quote fields that contain commas.
- All rows present: pillars x clusters per pillar (default 5 x 10 = 50 cluster rows).

**Example rows:**

```
Pillar Content,Cluster Content,Search Intent,Suggested Title,Suggested URL Slug,Search Volume,Keyword Difficulty
VFR flight planning,Reading aviation weather reports,informational,How to read METARs and TAFs like a pro,how-to-read-metars-tafs,1K-10K,Medium
,Best flight planning apps,commercial,The 7 best VFR flight planning apps compared,best-vfr-flight-planning-apps,1K-10K,High
,ForeFlight subscription plans,navigational,ForeFlight plans and pricing explained,foreflight-plans-pricing,10K-100K,Low
```

## Chat response

Keep the chat message short: link the file, name the pillars in one sentence, note that volume/difficulty are estimates to validate with keyword tools. Do not paste the CSV contents into chat.
