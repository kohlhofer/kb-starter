---
name: kb-ingest
description: "Put a source into a knowledge base's RAW/ folder correctly: verbatim text, required frontmatter, registration, the Owner statement path for facts the user states in conversation, and the handoff path for material that belongs in a different KB. Use when the user pastes or shares a source, says \"add this to <kb>\", \"save this to RAW\", tells the librarian a fact that should be in the wiki, or when another KB skill needs to land a source before citing it."
---

# Ingest a source into RAW

RAW is the evidence layer. Everything the wiki claims traces to a file here, so the rules are strict.

## Verbatim, always

Save the source word for word. Never summarise, paraphrase, condense, or reorder on ingest. Preserve the source's own headings. Distillation happens in the wiki.

Two honest exceptions, both labelled in `type`:

- A source you could not fetch in full (auth-gated Notion, Slack, a paywalled article): save what you have and mark the type as an excerpt, e.g. `Slack thread (excerpt)`, `Google Doc (inaccessible)`. Never present a partial capture as the full source.
- A digest you wrote from web research because the primary source is unreachable: `type: Web search digest` (or `Book digest`). Document the search context inside the file. This is a secondary source and the lint grades it `digest`.

Secrets never enter RAW. Replace an API key, token, or password with `[redacted]` and keep the surrounding prose.

## Frontmatter

```
---
title: <exact title of the source>
author: <name, or unknown>
source_url: <URL, or unknown (<why>)>
date_added: YYYY-MM-DD
date_published: YYYY-MM-DD or unknown
type: <see vocabulary>
tags: [optional]
---
```

`unknown` is always better than a guess. `date_added` is today.

Type vocabulary (free text is allowed, but reuse these so the provenance grade derives correctly):

- Primary: `Article`, `Blog Post`, `Paper`, `Book`, `Podcast`, `Video`, `Tweet`, `Documentation page`, `Product page`, `Product spec`, `Internal doc`, `Notion page`, `Slack thread`, `Meeting transcript`, `Repo README`, `Dataset`, `Founder update`, `Proposal`
- Digest (secondary, librarian-written): `Web search digest`, `Book digest`, `Dataset digest`
- Owner-stated: `Owner statement`, `Note`, `Decision record`, `Implementation log`, `Strategy note (chat capture)`, `Status update`

Filename: keep the source's own name where possible; otherwise descriptive kebab-case. RAW files are never edited or deleted after ingest, except to add a missing frontmatter field as `unknown`.

## Owner statements

When the user states a fact in conversation that the wiki should carry ("the app runs on iPad too", "we killed the Q3 plan"), it is a source like any other and it needs a file before an article can cite it. Do not write it into a wiki article directly.

1. Create `<kb>/RAW/YYYY-MM-DD_owner-<slug>.md`:

   ```
   ---
   title: <one-line statement>
   author: <the owner's name, from the root CLAUDE.md>
   source_url: unknown (stated in conversation, YYYY-MM-DD)
   date_added: YYYY-MM-DD
   date_published: YYYY-MM-DD
   type: Owner statement
   ---

   <The statement, as close to the user's wording as possible. One short line of context if needed: what was being discussed.>
   ```

2. Register it in `_INGESTED.md` (line form below).
3. Cite it in the article. The article's `Provenance` becomes `owner-stated` or `mixed`; that is the point.

Several statements in one session can share one file if they are about the same topic; unrelated ones get separate files.

## Register

Every RAW file has a line in `<kb>/RAW/_INGESTED.md`, newest batch at the top, under a `## YYYY-MM-DD — <batch label>` heading:

```
- `filename.md` — YYYY-MM-DD — <origin> — <one-line summary>
```

One file per line, exact filename in backticks. `bin/kb-lint` treats an unregistered RAW file as "not compiled yet".

## Handoff to another KB

A source or a lesson sometimes belongs in a different KB (a marketing lesson learned while running a side project belongs in `marketing_kb`).

- A **source**: ingest it into the other KB's `RAW/` following this skill, register it there, and note the handoff in both CHANGELOGs.
- A **lesson or gap** that has no source yet: append a bullet to the other KB's `Wiki/QUESTIONS.md` under a `## Inbox from other KBs` heading, with the date, the origin KB, and a pointer to the article or output it came from. The other KB's next compile or health check processes it.
- Cross-KB `[[links]]` are allowed only between KBs that list each other in `kb.json` `routes`; add the route if it is missing, and keep the link count small. Linking is for the odd reference, handoff is for anything that should live there.

## Finish

Run `bin/kb-lint <kb>`; the new file should show no frontmatter errors and no longer appear as unregistered. If the ingest was a one-off (no compile requested), say so and offer to compile.
