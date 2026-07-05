# CLAUDE Second-Brain Schema

## Purpose
This file defines the second-brain system, folder structure, and the rules the LLM should follow when ingesting sources, updating the wiki, and maintaining the index and log.

## Folder structure
- `raw/` — immutable source material.
- `wiki/` — generated knowledge pages maintained by the LLM.
- `index.md` — a living catalog of wiki pages and source summaries.
- `log.md` — chronological audit trail of ingest actions and major updates.
- `claude.md` — system schema, rules, and workflow guidelines.

## General rules
1. Always keep `index.md` in sync with new wiki pages and important source summaries.
2. Append every ingest, synthesis, or wiki maintenance action to `log.md`.
3. Never edit files in `raw/` except to add new source content. Raw documents are source truth.
4. Write wiki output only inside `wiki/` or root files meant for system metadata (`index.md`, `log.md`, `claude.md`).
5. Use markdown headings, links, and concise summaries.

## Page types
- `Source`: raw input files in `raw/`.
- `Summary`: concise condensation of a source or topic in `wiki/`.
- `Topic`: concept pages, entity pages, or project pages in `wiki/`.
- `Synthesis`: analysis, comparison, or notes generated from multiple sources.
- `Task`: action items or follow-up research notes.

## Metadata conventions
Use YAML frontmatter when a page needs metadata:
```yaml
---
title: "Page title"
date: 2026-07-05
tags: [second-brain, ingest, topic]
sources:
  - raw/first-source.md
---
```

## Ingest workflow
1. Add a new file to `raw/`.
2. Read the raw source fully.
3. Create or update one or more pages in `wiki/`.
4. Update `index.md` with the new wiki pages and source summary.
5. Append a structured entry to `log.md`.

## Query workflow
1. Read `index.md` first to identify relevant pages.
2. Use links and summaries to choose best wiki pages.
3. Answer the question using the wiki content.
4. If the answer is a lasting insight, create a `wiki/` page and update the index and log.

## Index format
`index.md` should include:
- category sections
- page links
- short one-line summaries
- metadata such as tags or source count when useful

## Log format
`log.md` should include:
- date-stamped headings
- action type (ingest, synthesis, review)
- source references
- brief summary of changes

## First ingest example
A first ingest should demonstrate the full cycle:
- Add `raw/first-source.md`
- Create `wiki/first-source-summary.md`
- Add both entries to `index.md`
- Append one log entry to `log.md`

## Rules for every response
1. Use the schema: Purpose, Folder structure, Workflow, Example.
2. Be concise but complete.
3. Reference actual files in the workspace.
4. Keep each response aligned with the second-brain system.
