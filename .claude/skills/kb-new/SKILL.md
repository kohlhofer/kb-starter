---
name: kb-new
description: "Create a new knowledge base folder in the knowledge workspace from the template, with all state files in place. Use when the user says \"new knowledge base\", \"spin up a KB for <topic>\", \"create <topic>_kb\"."
---

# Create a knowledge base

1. Confirm the folder name (`<topic>_kb`, lowercase snake_case) and two to five focus areas. Ask if the user has not said.
2. Create `<topic>_kb/` with `RAW/`, `Wiki/`, `Outputs/`.
3. Copy `_KB_CLAUDE_TEMPLATE.md` to `<topic>_kb/CLAUDE.md`. Delete the banner comment, fill in the H1, *What This Is*, *Focus Areas*, and the KB-specific sections; delete any placeholder section that does not apply.
4. Create:
   - `CHANGELOG.md` with one entry: `## YYYY-MM-DD — Created`
   - `STATE.md` with the standard headings and "nothing yet" content
   - `RAW/_INGESTED.md` with the heading `# RAW ingest registry` and the line-format note
   - `Wiki/INDEX.md` (`# Index`), `Wiki/QUESTIONS.md` (`# Open Questions`)
   - `kb.json`: `{"routes": [], "external": [], "state": {"audited": {}}}`
5. Run `bin/kb-lint <topic>_kb`; it should be clean.
6. Commit with explicit paths, message `kb(<short>): create knowledge base`.
7. Tell the user the two ways to add sources (drop files into `RAW/`, or paste in chat) and that "compile" processes them.
