---
name: kb-compile
description: "Compile new RAW sources in one knowledge base into its Wiki. Use when the user says \"compile\", \"process RAW\", \"compile <kb>\", \"ingest and compile\", or drops files into a RAW/ folder and asks the librarian to work through them. One KB per invocation."
---

# Compile RAW into Wiki

Reads every RAW file that is not yet registered, writes or updates the wiki articles it supports, registers the sources, and leaves the KB lint-clean.

## Before you start

1. Pick the KB. If the user did not name one and more than one exists, ask which.
2. Run `bin/kb-lint <kb>` from the workspace root. The `WARN: not in _INGESTED.md (uncompiled?)` lines are your work list. Fix any `ERROR` lines that are mechanical (a broken link, a missing field) as you go.
3. Read, in this order: `<kb>/CLAUDE.md` (focus areas, source notes, article shapes), `<kb>/STATE.md`, `<kb>/Wiki/INDEX.md`, `<kb>/Wiki/QUESTIONS.md`. Do not read `CHANGELOG.md` in full; open it only to check a specific past entry.
4. Load the `humanizer` skill, and the voice skill named in the root `CLAUDE.md` (*Writing*) if one is set. The voice is how article prose is written; the humanizer is the pass every new or updated article gets before it is saved. Where they disagree, the voice wins.

## Per RAW file

1. Read the whole file. If the lint reported a missing frontmatter field, add it as `unknown` rather than guessing; that is the only edit ever made to a RAW file.
2. Register it in `<kb>/RAW/_INGESTED.md`. Newest batch at the top, under a `## YYYY-MM-DD — <batch label>` heading, one line per file in exactly this form so the lint can see it:

   ```
   - `filename.md` — YYYY-MM-DD — <origin: URL, repo path, "stated in conversation", …> — <one-line summary>
   ```

   Never compress several files into one line.
3. Decide what the source supports: a new article, an update to an existing one, or only a line in `QUESTIONS.md`. The KB's focus areas decide; off-topic sources get registered and flagged in the CHANGELOG entry, not compiled.
4. Write or update articles to the standard in the root `CLAUDE.md` (*Wiki article standard*): Status, Last updated, Sources, Provenance, Summary, Body, Related, Open Questions. Every claim traces to a cited RAW file. Set `Provenance` from the cited sources' `type` fields (primary / digest / owner-stated / mixed); `bin/kb-lint --fix` derives it if you leave it out.
5. Before saving any article you wrote or changed, run the humanizer pass over its prose, with the voice skill winning where they disagree (root `CLAUDE.md`, *Writing*). Direct quotes, tables, frontmatter and links stay as they are.
6. Add inbound links from existing articles that mention the topic. Add new articles to `INDEX.md` with a one-line description.
7. Contradictions are cross-referenced, not reconciled (root manual, *wiki article standard*); log the tension under *Held tensions* in `QUESTIONS.md`.
7. Anything the user told you in conversation that ends up in an article needs its own RAW file first; follow the *Owner statements* section of the `kb-ingest` skill.

## Finish

1. `bin/kb-lint --fix <kb>` then `bin/kb-lint <kb>`. Zero errors before you write the log. Warnings are fine if they are deliberate; say which.
2. Add one entry at the top of `<kb>/CHANGELOG.md`:

   ```
   ## YYYY-MM-DD — Compile pass
   - N new sources registered (see RAW/_INGESTED.md)
   - Created: slug-a, slug-b
   - Updated: slug-c
   - Held: <what did not earn an article and why>
   - Pending: <anything that needs the user's judgement>
   ```
3. Rewrite `<kb>/STATE.md` (replace, do not append) so it describes the KB after this pass.
4. `bin/kb-lint --record <kb> compile`.
5. Commit the pass with explicit paths (`git add <kb>/…`), message `kb(<short-name>): compile — <what>`.
6. Report in chat: what you read, what you wrote, what you held, and the CHANGELOG path.
