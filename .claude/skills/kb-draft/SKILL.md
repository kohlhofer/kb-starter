---
name: kb-draft
description: "Draft a new wiki article, or substantially enrich an existing one, in a knowledge base — with web research, sources landed in RAW first. Use when the user says \"draft an article on\", \"enrich <topic>\", \"write up <topic> for <kb>\", or asks to act on the article candidates a health check held."
---

# Draft or enrich a wiki article

Question answering conserves the corpus; drafting expands it. Web research is part of the job here, not a fallback.

## Procedure

1. Read `<kb>/CLAUDE.md` (focus areas, article shapes, status semantics), `<kb>/STATE.md`, `<kb>/Wiki/INDEX.md`, and any existing article on the topic. Check `Wiki/QUESTIONS.md` for a held candidate or a related tension.
2. Search the web freely. Prefer primary sources (the author's own site, the original paper, the vendor's documentation). Use secondary sources only when primary is unreachable, and label them.
3. Land every source you will cite in `<kb>/RAW/` first, following the `kb-ingest` skill: verbatim where you have the text, `Web search digest` where you do not, full frontmatter, registered in `_INGESTED.md` before you cite it.
4. Write the article to the standard in the root `CLAUDE.md`. Default `Status: emerging`. Set `Provenance` from the sources you cited; an article resting only on digests is `digest`, and its Summary should say what the primary sources are and that they were not read in full. Load the `humanizer` skill, and the voice skill named in the root `CLAUDE.md` (*Writing*) if one is set: write in the voice, then run the humanizer pass over the prose before saving. Where they disagree, the voice wins. Direct quotes, tables, frontmatter and links are untouched by the pass.
5. Add inbound links from existing articles that mention the topic; add the article to `INDEX.md`; move any resolved candidate line in `QUESTIONS.md` to its *Resolved* section.
6. If the evidence turns out too thin for an honest article, stop: log `Article candidate held: <name> (insufficient evidence)` in the CHANGELOG entry and leave a line in `QUESTIONS.md`. A gap is better than filler.

## Finish

1. `bin/kb-lint --fix <kb>` then `bin/kb-lint <kb>`, zero errors.
2. CHANGELOG entry at the top: `## YYYY-MM-DD — Article drafted: <slug>` with the sources landed and anything held.
3. Rewrite `<kb>/STATE.md` if the KB's shape changed (a new cluster, a gap closed).
4. Commit with explicit paths, message `kb(<short-name>): draft <slug>`.
5. End with the article's path (root manual, *Presenting a file*).
