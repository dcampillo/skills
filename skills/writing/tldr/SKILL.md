---
name: tldr
description: >
  Generate a structured TLDR summary from pasted text or a URL. Use this skill
  whenever the user wants a quick summary, digest, or overview of any content —
  whether they paste it directly into chat or share a link. Trigger on phrases
  like "TLDR", "summarize this", "give me the gist", "what's this about",
  "digest this", "too long didn't read", "can you summarize", "quick summary",
  or when a URL is shared and the user asks what it's about. Also trigger when
  a long document is pasted and no specific question is asked — the user likely
  wants a summary. Always use this skill for any summarization request, even
  casual ones.
---

# TLDR Skill

Generate a crisp, structured TLDR summary from pasted text or a URL.

---

## Input handling

### Pasted text
The user pastes content directly into the chat. Use it as-is.

### URL input
When a URL is provided, fetch the page content with your web fetch tool before summarizing. Do not ask the user to paste the content — fetch it yourself.

If the page is behind a login wall or returns an error, tell the user clearly and offer to summarize if they paste the content manually.

---

## Output format

Always produce the TLDR in this exact structure:

```
## TLDR: [Punchy headline: 10 words max, sentence case]

**What it is:** One sentence explaining the source/context (article, report, announcement, etc.)

**Key points:**
- [Point 1]
- [Point 2]
- [Point 3]
- [Point 4: add more only if genuinely needed, max 6]

**Key takeaway:** One sentence. The single most important thing to remember.
```

### Tone and style rules
- Use sentence case throughout (no ALL CAPS headlines, no Title Case bullets)
- Be specific — avoid vague filler like "the article discusses various aspects"
- Keep bullets to one idea each, max 20 words per bullet
- No em dashes. Use commas or colons instead
- American English spelling
- Do not editorialize or add opinions — stick to what the source says

---

## After the TLDR

Always end with this exact line (no variations):

> *Want me to save this as a file (markdown or PDF)?*

If the user says yes, ask whether they prefer markdown or PDF, then produce the file: a plain .md file for markdown, or use the pdf skill for PDF.

---

## Edge cases

| Situation | What to do |
|---|---|
| Content is very short (< 100 words) | Summarize it but note it was already brief |
| Content is highly technical | Keep technical terms, do not oversimplify |
| Multiple documents or URLs pasted | Summarize each separately, then offer a combined TLDR |
| URL is a video or audio page | Note that you can only summarize the page description/transcript if available |
| No clear "takeaway" exists | Write "No single takeaway: see key points above." |

---

## Example output

**Input:** User pastes a 1,200-word blog post about Retrieval Augmented Generation.

**Output:**

## TLDR: RAG combines retrieval and generation for more accurate AI answers

**What it is:** A technical blog post explaining the RAG architecture pattern for LLM applications.

**Key points:**
- RAG retrieves relevant documents at query time and injects them into the LLM prompt, reducing hallucinations
- Unlike fine-tuning, RAG keeps knowledge external and updatable without retraining the model
- Chunking strategy and embedding quality are the two biggest factors in retrieval accuracy
- Hybrid search (dense + sparse) consistently outperforms pure vector search in benchmarks
- Latency overhead is typically 100-400ms depending on index size and retrieval depth

**Key takeaway:** RAG is the pragmatic path to grounded LLM output when your data changes frequently.

---

*Want me to save this as a file (markdown or PDF)?*
