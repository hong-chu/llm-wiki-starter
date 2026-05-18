# llm-wiki-starter

A minimal, domain-agnostic template for building a personal
LLM-maintained wiki, following the pattern from
[Andrej Karpathy's *llm-wiki* gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

Drop sources into `sources/`. Point an agent (Claude Code, Codex,
Cursor) at `CLAUDE.md`. The agent does the rest.

## What you get

```
sources/   raw, immutable inputs (you curate)
wiki/      derived markdown (the agent maintains)
  index.md       hand-curated entry points
  overview.md    a 1-page tour of the whole knowledge base
  log.md         append-only change log
  concepts/      ideas, frameworks, terminology
  entities/      people, orgs, products, papers, datasets
  syntheses/     thematic write-ups across multiple sources
  comparisons/   side-by-side: A vs. B
  questions/     open questions / FAQ
derived/   optional: charts, slides, exports built from wiki/
CLAUDE.md  the schema — read this first
```

## Quick start

1. **Clone this repo** and pick a topic you actually care about.
2. **Open `CLAUDE.md`** and replace the `TL;DR for the agent` block
   with one sentence describing your topic.
3. **Drop 2–3 sources** into `sources/` (papers, blog posts as `.md`,
   PDFs, transcripts, notes — anything immutable).
4. **Open the folder in your agent** of choice. Say `ingest`. Watch
   what happens.

That's it. Read [`WORKSHOP.md`](WORKSHOP.md) if you came here from a
workshop pre-work email; it has the full setup recipe.

## Reading order
1. [`CLAUDE.md`](CLAUDE.md) — the schema. Agents read this first.
2. [`WORKSHOP.md`](WORKSHOP.md) — workshop pre-work instructions.
3. [`wiki/index.md`](wiki/index.md) — your top-level navigation
   (currently empty; fills in as you ingest).

## Recommended setup

- **Editor:** [Obsidian](https://obsidian.md) — opens this folder
  as a vault; `[[wiki-links]]` work natively, plus graph view +
  backlinks.
- **Agent:** [Claude Code](https://docs.claude.com/en/docs/claude-code/overview)
  or [Codex](https://github.com/openai/codex). Either reads
  `CLAUDE.md` / `AGENTS.md` automatically.

## License

MIT — see [`LICENSE`](LICENSE).

## Credit

Pattern by [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
This template is just one concrete instantiation of it.
