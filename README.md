# KB starter

A blank workspace for running local knowledge bases with Claude Code as the librarian. It follows Andrej Karpathy's LLM knowledge base pattern: you drop sources into a knowledge base's `RAW/` folder, Claude compiles them into linked wiki articles, and your questions get answered from that corpus with every claim traced back to a source.

## Get Started

You need Claude Code, git and python3.

1. Clone this repo, or download it, wherever you like and rename the folder if you want (`~/knowledge` works well).
2. Open a terminal in it and run `claude`.
3. Say "set up". Claude asks your name, sets up git and the pre-commit hook, and offers to create your first knowledge base.

## The Three Moves

1. **Add to RAW.** Drop files into `<topic>_kb/RAW/` in Finder, or paste a source into the chat and say "add this to <topic>".
2. **Compile.** Say "compile" and Claude turns the new sources into wiki articles.
3. **Ask.** Ask questions about the topic. Bigger questions become reports in `Outputs/`.

Run "health check" now and then. Claude nudges you at session start when a knowledge base needs a compile or a check.

## What's in Here

- `CLAUDE.md` is the manual Claude reads every session: the folder layout and the rules every knowledge base follows.
- `.claude/skills/kb-*` hold the procedures: `kb-new`, `kb-ingest`, `kb-compile`, `kb-question`, `kb-draft`, `kb-health-check`.
- `.claude/skills/humanizer` is the editing pass that keeps the prose from reading like a chatbot (MIT, Siqi Chen).
- `bin/kb-lint` does the mechanical checks: broken links, unregistered sources, missing frontmatter, index drift. The session-start hook in `.claude/settings.json` and the pre-commit hook in `.githooks/` both run it.
- `_KB_CLAUDE_TEMPLATE.md` is the template `kb-new` uses for each knowledge base's own `CLAUDE.md`.

## Reading Your Knowledge Bases

Every file here is plain markdown, so any editor works. I read mine in [Markdown Library ML-42](https://fieldbw.com/ml-42), a Mac app I made. Point it at this folder and you get the wiki as rendered documents with search across every knowledge base. When Claude rewrites an article, ML-42 reloads it on the spot.

## Where This Came From

The system started as a Karpathy-style knowledge base kit for Claude Code. Its creator's [walkthrough video](https://youtu.be/ib74sLgjIBM) is the best introduction to the idea, so watch it before your first build. Most of what's here has been rebuilt since, including the skills and the lint.
