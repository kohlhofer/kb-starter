---
name: kb-question
description: "Answer a question against one knowledge base's corpus and, when it is a synthesis question, file the answer as a report in Outputs/. Use whenever the user asks a question about the subject of a KB, or says \"ask the KB\", \"what does the wiki say about\", \"write me a report on\"."
---

# Answer a question from the corpus

## Decide the shape first

Two kinds of question come in:

- **Synthesis question.** The answer draws on more than one article, weighs evidence, or would take more than a paragraph. File a report.
- **Quick lookup.** One fact, one article, one paragraph. Answer in chat, cite the article, and say "not filed" so the user can ask for a report if they want one.

If in doubt, file. The user can also say "don't file this" or "just answer in chat".

## Answer

1. Read `<kb>/STATE.md` and `<kb>/Wiki/INDEX.md`, then the articles that matter, then RAW files where the article's wording is not enough. Wiki first, RAW second.
2. Web search is offered, never run on its own. If the corpus cannot answer, say what is missing and offer to search; if the user accepts, anything you cite lands in `RAW/` first (see the `kb-ingest` skill) and gets registered before you cite it.
3. Watch the article's `Provenance` line. An answer resting on `digest` or `owner-stated` sources says so; do not present it with the confidence a `primary` source would earn.
4. Facts the user states while asking ("actually we dropped that plan in July") are owner statements. Capture them per the `kb-ingest` skill before they influence the wiki.

## The report

Path: `<kb>/Outputs/YYYY-MM-DD_<kebab-slug>.md` (`-v2`, `-v3` if the date and slug collide). Load the `humanizer` skill, and the voice skill named in the root `CLAUDE.md` (*Writing*) if one is set: write the report in the voice, then run the humanizer pass over its prose before saving.

```
---
kind: report
---

# <The question, restated cleanly>

**Asked:** YYYY-MM-DD
**Drew on:** [[article-a]], [[article-b]], [[raw-file]] (RAW where it was the primary source)

## Answer
Structured for re-reading, not for the chat window.

## Tensions
Where the corpus disagrees with itself or the evidence is thin. Name the articles.

## Open questions and next moves
What follows from the answer; what the corpus cannot say yet.
```

Every claim in the report cites the article or RAW file it came from with a `[[link]]`. `bin/kb-lint` warns on a report with no citations.

## Finish

1. `bin/kb-lint <kb>` must show no errors.
2. If the report contains synthesis the wiki does not have yet, say so in one line and propose promotion; do not promote unprompted. Anything newly unresolved goes into `Wiki/QUESTIONS.md`.
3. Commit the report (`git add <kb>/Outputs/<file>`), message `kb(<short-name>): report — <slug>`.
4. End the chat summary with the report's path (root manual, *Presenting a file*).
