# Witsense Second Brain

A lightweight personal knowledge base system that turns raw sources into an evolving wiki.

## What this repo contains

- `raw/` — immutable source documents
- `wiki/` — generated markdown knowledge pages
- `claude.md` — system rules for the LLM agent
- `index.md` — catalog of wiki pages and summaries
- `log.md` — chronological history of ingests and updates

## How to use it

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
