# llm-wiki-starter

A minimal, domain-agnostic template for building a personal knowledge
base that an LLM keeps maintained for you. You curate sources; an
agent reads them, writes the wiki, keeps it tidy. Pattern from
[Andrej Karpathy's *LLM Wiki* gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

## What is LLM-wiki?

Most LLM-over-documents setups today are some flavor of RAG: you
upload a pile of files, the model retrieves a few chunks at query
time, generates an answer, and forgets everything. Ask the same
question tomorrow and the model rediscovers it from scratch. Nothing
accumulates.

An LLM-wiki flips that. Sources still arrive as raw files, but an
agent now *maintains* a derived markdown layer between you and them —
a structured, interlinked wiki it owns and updates. Each ingest
leaves durable prose behind: a new concept page, an extended entity,
a flagged contradiction, an updated index. Future questions read
what's already been thought through. The wiki compounds.

The unlock is maintenance cost. A personal wiki has always been a
tempting idea (Vannevar Bush proposed the Memex in 1945), but no
human has the patience to keep one current. LLMs do.

### The three-layer architecture

| Layer | What lives there | Owner |
|---|---|---|
| **Sources** | Immutable raw inputs — papers, transcripts, blog clippings, screenshots, your own notes. | You curate; the agent reads but never writes. |
| **Wiki** | Derived markdown — concepts, entities, syntheses, comparisons, questions, plus an `index.md` and `log.md`. | The agent owns and maintains it. |
| **Schema** | A single `CLAUDE.md` (or `AGENTS.md`) file: page templates, ingest/query/lint workflows, style rules. | You and the agent co-evolve it. |

### The existence format

On disk, an LLM-wiki is just a directory of markdown files:

```
your-wiki/
├── CLAUDE.md          ← schema (the agent reads this first)
├── README.md
├── sources/           ← Layer 1 — raw, immutable inputs (you curate)
├── wiki/              ← Layer 2 — agent-owned derived markdown
│   ├── index.md           hand-curated entry points
│   ├── overview.md        narrative tour
│   ├── log.md             append-only change log
│   ├── concepts/          ideas, frameworks, terminology
│   ├── entities/          people, orgs, products, papers
│   ├── syntheses/         multi-source thematic essays
│   ├── comparisons/       side-by-side: A vs. B
│   └── questions/         open questions / FAQ
└── derived/           ← optional: charts, decks, exports
```

Four operations run against this structure:

- **`ingest`** — a new source appeared. The agent reads it, decides
  whether it extends an existing page or warrants a new one, makes
  the change, and appends to `log.md`.
- **`query`** — you ask a question. The agent searches `wiki/` first,
  falls back to `sources/`, and surfaces gaps as new entries in
  `questions/`.
- **`lint`** — a periodic hygiene pass: orphans, dead links, missing
  `## Sources` blocks, stale stubs.
- **`recompile`** — the escape hatch. Delete `wiki/`, re-derive every
  page from `sources/` + `CLAUDE.md`. Useful as a reproducibility
  check.

Two files do disproportionate work: `index.md` (the curated table of
contents, hand-tended; not a wall of auto-generated links) and
`log.md` (an append-only audit trail of every change, dated).

## What's good about LLM-wiki (vs. traditional RAG)

The intro called this out — RAG re-derives, the wiki compounds — but
the contrast deserves more.

**RAG and the wiki aren't competing for the same job.** RAG retrieves
at query time: index a corpus, fetch the top-k chunks, let the model
answer. Fast, cheap, scales to millions of documents. But the model's
thinking never persists — every query starts from scratch.

A wiki shifts the work upstream. An agent reads the same sources but,
at ingest time, *transforms* them: synthesizes a concept page,
cross-links it to related concepts, flags contradictions across
sources, queues open questions. That transformation is more expensive
than embedding text — but every subsequent query reads the
already-thought-through pages. The cost amortizes; the knowledge
compounds.

A few practical wins fall out of this:

- **Cross-references are first-class.** Every wiki page links to ≥ 2 others; the link graph is part of the artifact. RAG chunks have no relationship to each other.
- **Contradictions surface.** When two sources disagree, the wiki captures both in a `## Contradictions` section. RAG silently picks one chunk or averages them away.
- **You can read it.** The wiki is just markdown — browse it, edit it, share it, version it with git, open it in Obsidian. A vector index is opaque.
- **Audit is easy.** Every page cites its sources; `log.md` records every change. You can answer *"why does the system think X?"* by opening a file.
- **Failures are fixable.** RAG's failure mode (retrieval miss) is silent. The wiki's failure mode (a bad summary baked into a page) is visible right there in the file — and `recompile` rebuilds from sources.

|   | RAG | LLM-wiki |
|---|---|---|
| **What persists between queries** | Vectors of raw chunks | Synthesized markdown pages |
| **Per-query cost** | Cheap | Cheap |
| **Per-ingest cost** | Low (embedding) | Higher (synthesis) |
| **Cross-references** | None | Every page links |
| **Contradictions** | Silently averaged | Explicitly flagged |
| **Auditability** | Opaque vectors | Cited markdown + log |
| **Human-readable** | No | Yes (`index.md`) |
| **Sweet spot** | Massive, query-diverse | 10–1000 sources, repeated questions |

Where the wiki *doesn't* win, see [**When not to use it**](#when-not-to-use-it) below.

## Use cases

### Personal

- **Literature review** — papers in a domain, synthesized as you go.
- **Research area** — a topic you're trying to master across textbooks, papers, and blog posts.
- **Onboarding to a new domain** — a new job, a new stack, a new sub-field. The wiki becomes your ramp-up artifact.
- **Long-running personal interest** — a hobby with a literature: climbing, fermentation, tea, Go.

### Team / enterprise

The same pattern, applied as a *context layer* for an AI-augmented
data or engineering team:

- **Domain context** — the team's shared understanding of *what this thing actually is* (a payment, a market, an alert, a customer).
- **Playbooks** — runbooks, alert response, on-call procedures.
- **Incident postmortems** — postmortems are sources; the wiki finds patterns across them.
- **ML experiment & ablation log** — every experiment is a source; the wiki synthesizes *"what we've learned about X."*
- **Architecture decisions (ADRs)** — proposals are sources; the wiki tracks themes, reversals, contradictions.
- **Customer / user feedback** — support tickets, interviews, NPS responses; the wiki maintains a living *"why customers ask about X."*

### When *not* to use it

- **Very large corpora** (>~1000 sources). Vector RAG scales better at that point.
- **Pure lookup / search** ("find me the doc that says X"). Wiki transformations get in the way.
- **Highly dynamic data** (real-time prices, logs, telemetry). Wikis are for slow-changing knowledge.
- **Regulated content requiring verbatim retrieval and immutable lineage.** The transformation step complicates audit; raw retrieval is often the right call.

## Implementation

### Prerequisites

- An LLM agent that reads a project-level instructions file: **Claude Code**, **Codex CLI**, or **Cursor**.
- A markdown editor — **Obsidian** is recommended for graph view and native `[[wiki-links]]`, but anything that opens `.md` works.

### Setup (5 minutes)

1. **Clone the template.**
   ```bash
   git clone https://github.com/hong-chu/llm-wiki-starter.git my-wiki
   cd my-wiki
   ```

2. **Pick a topic.** Narrow enough that ~10 sources can cover it meaningfully. *"Retrieval-augmented generation"* works; *"machine learning"* does not.

3. **Customize `CLAUDE.md`.** Open it and replace the `TL;DR for the agent` block with one sentence describing your topic. This is the only edit required to make the template yours; everything else in the schema you can iterate on after the first ingest.

4. **Drop 2–3 starter sources into `sources/`.** PDFs, blog posts pasted as `.md`, transcripts, your notes. Anything you've already engaged with and might want to remember.

5. **Open the folder in your agent and say `ingest`.** The agent reads `sources/`, decides what pages to create, and starts filling out `wiki/`.

> **Don't want to clone?** Open Claude Code in an empty folder and
> paste this prompt:
>
> ```
> Scaffold an LLM-wiki for the topic <YOUR TOPIC>, following the
> pattern in Andrej Karpathy's gist
> (https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
> Create:
>
> - CLAUDE.md (the schema, with page templates for concepts/entities/
>   syntheses/comparisons/questions and ingest/query/lint workflows)
> - An empty sources/ directory I'll fill in
> - An empty wiki/ tree: concepts/, entities/, syntheses/, comparisons/,
>   questions/, plus index.md, overview.md, log.md
>
> Use kebab-case.md filenames, [[wiki-links]] for cross-references, and
> end every page with a ## Sources section.
> ```
>
> The agent will create the structure from scratch. The result should
> look like the tree above.

### Your first ingest — what to expect

Once you say `ingest`, the agent will:

- Read each source. For any source without a sibling `*.meta.md`, generate one (title, author, date, URL, why-it-matters).
- Decide for each source whether it warrants a new page or extends an existing one.
- Generate concept and entity pages, draft a synthesis if multiple sources align on a theme, queue open questions in `questions/`.
- Append a dated entry to `log.md`.
- Update `index.md` if new top-level pages were created.

A first ingest of 2–3 sources typically produces 5–10 wiki pages.
Read what came out. Some will be exactly right; some will surprise
you. Both are useful.

### The three operations

- **`ingest`** — new sources arrive; the wiki updates. The agent never modifies `sources/`.
- **`query`** — ask the wiki a question. It answers from `wiki/` first, cites pages inline, and surfaces gaps as new `questions/` entries.
- **`lint`** — a periodic hygiene pass. Every page has a one-liner, ≥ 2 outgoing links, and a `## Sources` section; `log.md` is dated; no orphans.

### Editing the schema is the loop

After your first ingest, you'll notice things you'd do differently —
a page was split where you'd have merged, the tone is off, a template
doesn't fit your domain. **Don't fix the wiki by hand.** Edit
`CLAUDE.md` instead. Update the rule, ask the agent to re-ingest, and
the next pass produces what you wanted.

The schema is the contract; the wiki is its output. Iterating on the
schema is how a generic template becomes *your* wiki.

You don't have to type the edits manually — you can ask the agent to
update `CLAUDE.md` for you (*"add a rule that every entity page
includes a `## Timeline` section"*). The agent just doesn't change
the schema on its own; that part stays human-initiated.

### Optional: Obsidian

Open the folder as an Obsidian vault (*Open folder as vault* →
select `my-wiki/`). The `[[wiki-links]]` resolve natively, you get
backlinks and graph view for free, and the file tree maps directly
to the wiki structure.

## Credit

Pattern by [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
This template is just one concrete instantiation of it.

## License

MIT — see [`LICENSE`](LICENSE).
