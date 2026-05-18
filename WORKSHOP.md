# Workshop pre-work

Hey — before the workshop, please do these four things (~15 min). It
ensures we can spend our time on the *interesting* part instead of
setup, and that everyone arrives with a real example to work with.

## What you'll do

1. **Install tools** (5 min)
   - [Obsidian](https://obsidian.md) — free, local, no account
     required.
   - An agent of your choice:
     [Claude Code](https://docs.claude.com/en/docs/claude-code/overview)
     or [Codex CLI](https://github.com/openai/codex).

2. **Clone this template** (1 min)
   ```bash
   git clone https://github.com/hong-chu/llm-wiki-starter.git my-wiki
   cd my-wiki
   ```
   Open the folder in Obsidian: *Open folder as vault* → select
   `my-wiki/`. You should see the empty scaffold and the
   `[[wiki-links]]` will render once content arrives.

3. **Pick a topic + customize `CLAUDE.md`** (5 min)

   Choose a topic narrow enough that 5–10 sources could
   meaningfully cover it. Good examples:
   - A research area (e.g. *retrieval-augmented generation*,
     *MEV in DeFi*, *crypto-market microstructure*).
   - Your team's runbooks or alert playbooks.
   - A hobby with a literature (climbing, tea, fermentation, Go).
   - A specific technical sub-domain at work.

   Open `CLAUDE.md` and replace the `TL;DR for the agent` block
   with one sentence describing your topic. Don't over-engineer
   the schema yet — the agent will help you iterate it.

4. **Gather 2–3 sources** (5 min)

   Drop them into `sources/`. Anything immutable:
   - Papers (`.pdf`)
   - Blog posts (paste body into a `.md`)
   - Transcripts, talks, podcast notes
   - Screenshots, diagrams
   - Your own existing notes

   **Don't ingest yet.** We'll do the first ingest *together* during
   the workshop — that's when the pattern clicks, and you'll get more
   out of seeing it for the first time with the group.

## What to bring to the workshop

- Your laptop with Obsidian + the agent installed.
- The cloned repo with 2–3 sources in `sources/`.
- An answer to this question, in your head:

  > *Where in your current work do you (or someone on your team)
  > re-derive the same answer repeatedly, or where new sources
  > accumulate but nobody synthesizes them? Bring one example.*

We'll workshop your example live.

## If you get stuck

- Setup question? DM me.
- Curious and want to peek? Run `ingest` yourself and bring questions
  about anything weird you see. (Optional — most people will get more
  out of waiting.)

See you on workshop day.
