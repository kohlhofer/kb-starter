---
name: kb-health-check
description: "Audit one knowledge base: run the mechanical lint, apply its fixes, then do the judgement pass (writing voice, promotions, contradictions, provenance sanity, stale articles, output promotion candidates, article candidates) and log the result. Use when the user says \"run a health check\", \"audit the <name> KB\", \"check the wiki\", \"action the latest health check\", or when the session-start nudge says a KB's last check is old. One KB per invocation; run it once per KB if asked for all."
---

# Knowledge base health check

Two halves. The script does the mechanical half; you do the judgement half. Never re-derive by hand what `bin/kb-lint` already reports.

## 1. Mechanical

From the workspace root:

```
bin/kb-lint --fix <kb>
bin/kb-lint <kb>
```

`--fix` fills missing `Provenance` lines from the cited RAW types, refreshes stale `Last updated` fields from git, and creates missing folders. Read the remaining output:

- `ERROR` lines: fix them now if the fix is mechanical (repoint a link whose target exists under another slug, add an article to `INDEX.md`, add a missing frontmatter field as `unknown`, register a RAW file the last compile missed). Anything else goes in the pending list.
- `WARN` lines: uncompiled RAW files mean a compile is due; say so and offer, do not compile inside the check. Reports without citations get either citations or a truer `kind:`.

## 2. Scope

Read `<kb>/kb.json` (`state.last_health_check`, `state.last_full_check`) and `<kb>/STATE.md`.

- **Full** if `last_full_check` is missing or more than 90 days old, or the user asks for it: read every article.
- **Delta** otherwise: articles changed since the last check (`git log --since=<date> --name-only -- <kb>/Wiki`), plus the three returned by `bin/kb-lint --sample <kb> 3`, which picks the least-recently-audited ones and records the pick so nothing goes unsampled forever.

Also read RAW files added since the last check (the `_INGESTED.md` batches after that date) and outputs filed since.

If nothing in the KB changed since the last check and the lint is clean, write `## YYYY-MM-DD — Health check skipped (no changes since <date>)` to the CHANGELOG, record it (step 5), and stop.

## 3. Judgement pass

Load the `humanizer` skill, and the voice skill named in the root `CLAUDE.md` (*Writing*) if one is set; together they are the writing rule. Without a voice skill, the humanizer pass alone applies. Then, for the articles in scope:

- **Voice.** Check the prose against the voice skill if one is set, then run the humanizer pass, with the voice winning where they disagree. The lint warns on more than two dashes in prose. This is a backstop; the writing skills already ran the same pass at write time, so a clean article is the expected result. Do not change spelling conventions, do not restyle bullets, do not touch direct quotes. Navigation files (`INDEX.md`, `QUESTIONS.md`, `STATE.md`, `CHANGELOG.md`, `_INGESTED.md`) are exempt.
- **Provenance sanity.** `Status: established` with `Provenance: owner-stated` or `digest` is a warning sign: either the article says so plainly in its Summary, or it drops to `emerging`. This is where false confidence hides.
- **Promotions.** `emerging` → `established` when two or more independent sources support the load-bearing claims and the article's own Open Questions do not set a stricter gate. Note the reason in the CHANGELOG.
- **Contradictions.** Cross-reference per the root manual; log under *Held tensions* in `QUESTIONS.md`. Do not reconcile.
- **Stale articles.** New RAW that changes what an article says: update it if the change is local; flag it as pending if the rewrite would change the article's shape.
- **QUESTIONS hygiene.** Move anything resolved to the file's `## Resolved` section. Process the `## Inbox from other KBs` section if present. Mirror new gaps.
- **Output promotion candidates.** Reports with synthesis the wiki lacks: flag, do not promote.
- **Article candidates.** List orphan topics referenced from three or more articles, outputs whose synthesis is not in the wiki, held tensions that deserve their own article. Rank them, list them under *Article candidates* in the CHANGELOG entry, and offer to run `kb-draft` on any of them. Do not draft inside the check.

## 4. Log

At the top of `<kb>/CHANGELOG.md`:

```
## YYYY-MM-DD — Health check (delta | full)

Lint: N errors fixed, N warnings left (<which, why>). Audit: N of M articles read. RAW since last check: N. Outputs since: N.

Fixed:
- voice: N edits across N files
- provenance: <slug> established → emerging (owner-stated only)
- promotions: <slug> emerging → established (<sources>)
- contradictions: <a> ↔ <b>
- stale: <slug> updated for <raw-file>

Article candidates (not drafted):
- <slug>: <why, how much support>

Pending judgement:
- <category>: <one line>
```

Replace the three lists with `Clean — no fixes, no candidates, no pending items.` when that is true.

Then rewrite `<kb>/STATE.md` so it reflects the KB after the check.

## 5. Record and commit

```
bin/kb-lint --record <kb> health-check --type delta|full
bin/kb-lint <kb>
```

Commit with explicit paths, message `kb(<short-name>): health check YYYY-MM-DD (delta|full)`.

## 6. Report

```
Health check — <KB>, YYYY-MM-DD (<delta|full>)
- Lint: N fixed, N warnings left
- Read: N articles
- Fixed: N | candidates: N | pending: N
- CHANGELOG: <path>
```

If pending items exist, follow with a numbered list of the pending categories and wait for the user to pick. "Action the latest health check" means: read the latest entry's pending bucket and walk it the same way.

## Boundaries

- One KB per invocation.
- Never deletes or edits RAW content (frontmatter `unknown` fills excepted).
- Never edits `CLAUDE.md` files or `kb.json` `routes`.
- Never drafts articles or runs web research; it lists candidates and offers.
- No schedule. The session-start nudge (`bin/kb-lint --nudge`) says when a check is overdue; the user decides when to run it.
