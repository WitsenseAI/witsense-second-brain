# Witsense Second Brain

A lightweight personal knowledge base system that turns raw sources — or an existing codebase — into an evolving wiki. See `llm-wiki.md` for the full idea.

## Credit

The core idea — an LLM that *compiles* a persistent, cross-referenced wiki from your sources instead of re-deriving understanding from scratch on every query — is Andrej Karpathy's "LLM wiki" concept. `llm-wiki.md` in this repo is that idea, written up as a portable schema doc you can hand to any coding agent. All credit for the pattern itself goes to him; this repo is one concrete instantiation of it. (Link his original post here if you're citing a specific source.)

## What this repo adds on top of the original idea

Karpathy's original write-up (`llm-wiki.md`) is intentionally abstract and document-focused — notes, papers, journal entries, a personal research wiki. This repo turns it into something you can drop into a **code repository** and run today:

- **A code-specific workflow, not just a doc-ingest one.** `claude.md` adds `Module`/`Architecture` page types and three concrete workflows — bootstrap, incremental sync, lint — for source code specifically, alongside the original document-ingest flow.
- **A citation convention that makes staleness detectable.** Every claim on a code page cites `path:line` and a `last_synced_commit`. That's what lets `/lint` mechanically flag "this page describes code that has since changed" instead of silently drifting out of date.
- **Runnable Claude Code commands, not just instructions.** `.claude/commands/{bootstrap,sync,lint}.md` turn the workflows described in `claude.md` into actual slash commands, so "ask your agent to ingest this" becomes `/bootstrap` and `/sync`.
- **Model-tiering built into the tooling.** Each command pins its own model tier in frontmatter — a planning-tier model (Opus) for the one-time `/bootstrap` compile, a cheaper model (Sonnet) for routine `/sync` and `/lint`. The original idea doesn't address cost at all; this is this repo's own addition, based on the observation that bootstrap needs holistic judgment but incremental maintenance is mechanical. See "Model strategy" below.

## What this repo contains

- `raw/` — immutable source documents (doc-ingest use case)
- `wiki/` — generated markdown knowledge pages (module pages + `architecture.md` for code)
- `claude.md` — system rules and workflows for the LLM agent (doc ingest, code bootstrap/sync/lint, query)
- `.claude/commands/{bootstrap,sync,lint}.md` — Claude Code slash commands that run the code workflows, each pinned to the model tier it needs (see below)
- `index.md` — catalog of wiki pages and summaries
- `log.md` — chronological history of ingests and updates

## Quickstart: using this as a coding-repo template

1. Use this repo as a template (or copy `claude.md`, `.claude/commands/`, `index.md`, `log.md`, `wiki/` into an existing project).
2. In a fresh Claude Code session in your repo, run `/bootstrap`. This does the one expensive pass: reading the whole codebase and compiling it into `wiki/modules/*.md` + `wiki/architecture.md`, every claim cited `path:line`.
3. From then on, after each code change, run `/sync` — it only re-reads the diff and updates the pages that cite the changed files.
4. Run `/lint` periodically to catch stale citations or orphan pages.
5. Point everyday coding sessions (bug fixes, features) at `index.md` → `wiki/architecture.md` → the relevant module page first — that's what makes the wiki pay for itself instead of re-scanning the repo from zero every session.

## Model strategy: planner model once, cheap model always

The commands in `.claude/commands/` each pin a model tier in their frontmatter, so you don't have to remember to switch by hand:

| Step | Model tier | Why |
|---|---|---|
| `/bootstrap` | Planning-tier (e.g. Opus) | One-time (or rare) holistic pass across the whole codebase — deciding what the wiki should say requires real judgment. Worth paying for because it happens once. |
| `/sync` | Small/cheap (e.g. Sonnet, Haiku, or a local coding model) | Mechanical: find pages citing a changed file, update the affected lines. No architecture-level judgment needed for a normal diff. |
| `/lint` | Small/cheap | Pure staleness check against `last_synced_commit` — no judgment call. |
| Everyday bug fixes / features | Small/cheap | The wiki already compiled the hard part; day-to-day edits should only need `index.md` → `wiki/architecture.md` → one module page, not a fresh re-derivation of the codebase. |

Only escalate a `/sync` back to the planning-tier model if the underlying change is itself architectural (new module boundaries, files moving between layers) — the command's own instructions tell it to flag that case rather than push through.

## Doc-ingest workflow (unchanged from the original template)

1. Put a new source document in `raw/`.
2. Ask your LLM agent to ingest it.
3. The agent should create or update a page in `wiki/`.
4. The agent should update `index.md` and append an entry to `log.md`.

## Recommended workflow

- Keep raw documents as the source of truth.
- Let the agent generate summaries, topic pages, and syntheses in `wiki/`.
- Use `index.md` to navigate the knowledge base.
- Use `log.md` to track important changes over time.

## Example flow

- Add a file such as `raw/first-source.md`
- Generate a summary page like `wiki/first-source-summary.md`
- Update the index and log

## Notes

This repo is intentionally simple and easy to extend. You can add more folders, page types, or automation later as your second brain grows.
