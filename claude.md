# CLAUDE Second-Brain Schema

## Purpose

This file defines the second-brain system, folder structure, and the rules the LLM should follow when ingesting sources, updating the wiki, and maintaining the index and log.

This template supports two kinds of source material:

- **Document sources** — notes, specs, papers, decisions. Immutable, live in `raw/`.
- **Code sources** — the project's own source files. Live, tracked by git, never copied into `raw/`. The wiki compiles *understanding* of the code, not a copy of it.

Use whichever workflow fits the source. Most projects use both.

## Folder structure

- `raw/` — immutable document sources (specs, notes, decisions, external references).
- `wiki/` — generated knowledge pages maintained by the LLM. For code projects, includes an `architecture.md` and one page per module.
- `index.md` — a living catalog of wiki pages and source summaries.
- `log.md` — chronological audit trail of ingest actions and major updates.
- `claude.md` — system schema, rules, and workflow guidelines.

## General rules

1. Always keep `index.md` in sync with new wiki pages and important source summaries.
2. Append every ingest, sync, or wiki maintenance action to `log.md`.
3. Never edit files in `raw/` except to add new source content. Raw documents are source truth.
4. Never copy source code into `raw/` or `wiki/`. Wiki pages describe and cite code; they don't reproduce it.
5. Write wiki output only inside `wiki/` or root files meant for system metadata (`index.md`, `log.md`, `claude.md`).
6. Use markdown headings, links, and concise summaries.

## Page types

- `Source`: raw input files in `raw/`.
- `Summary`: concise condensation of a source or topic in `wiki/`.
- `Topic`: concept pages, entity pages, or project pages in `wiki/`.
- `Synthesis`: analysis, comparison, or notes generated from multiple sources.
- `Task`: action items or follow-up research notes.
- `Module`: one page per code module/package in `wiki/modules/`. Describes purpose, public interface, and key dependencies, grounded in citations (see below).
- `Architecture`: `wiki/architecture.md` — the top-level map of how modules fit together. Read this first when answering any code question.

## Metadata conventions

Use YAML frontmatter when a page needs metadata:

```
---
title: "Page title"
date: 2026-07-19
tags: [second-brain, ingest, topic]
sources:
  - raw/first-source.md
---
```

For `Module` and `Architecture` pages, also include:

```
---
title: "auth module"
date: 2026-07-19
tags: [code, module]
citations:
  - src/auth/session.py:1-40
  - src/auth/middleware.py:12-58
last_synced_commit: a1b2c3d
---
```

## Citation convention for code pages (required)

Every factual claim on a `Module` or `Architecture` page must be grounded in a `path:line` or `path:start-end` citation, e.g.:

> The session token is refreshed on every request (`src/auth/middleware.py:34-41`).

This is what makes a later lint pass able to detect staleness: if the cited lines have changed since `last_synced_commit`, the claim is flagged for review rather than trusted blindly. Don't paraphrase code into a claim without a citation attached.

## Document ingest workflow (unchanged)

1. Add a new file to `raw/`.
2. Read the raw source fully.
3. Create or update one or more pages in `wiki/`.
4. Update `index.md` with the new wiki pages and source summary.
5. Append a structured entry to `log.md`.

## Code ingest workflow — bootstrap (first run on a codebase)

Run this once when the wiki has no `wiki/modules/` pages yet, or when asked to rebuild it from scratch.

1. Read the project's dependency/entry-point files first (e.g. `package.json`, `pyproject.toml`, `main.py`, `src/index.ts`) to establish the module map before reading implementation details.
2. Walk the source tree module by module — not file by file. Group tightly-coupled files (e.g. a class and its tests) into one pass.
3. For each module, write `wiki/modules/<module-name>.md`: purpose, public interface, key dependencies, non-obvious design decisions — every claim cited per the convention above.
4. Write `wiki/architecture.md`: how modules connect, data flow, entry points. This is the page every future query reads first.
5. Update `index.md` and append one `log.md` entry summarizing the bootstrap (module count, commit hash bootstrapped from).
6. Do not attempt full coverage in one pass on a large codebase — extract the 20% of structure (schemas, models, entry points, public interfaces) that explains 80% of how the system works, and note in `log.md` what was deliberately left uningested.

## Code sync workflow — incremental (every commit or on request)

Run this instead of bootstrap once `wiki/modules/` exists.

1. Diff files changed since the commit recorded in each page's `last_synced_commit`.
2. For each changed file, find every `wiki/` page that cites it (via `citations` frontmatter or in-text citations).
3. Re-read only the changed regions and update just the affected claims and citations — don't rewrite the whole page.
4. If a change adds a new module or removes one, update `wiki/architecture.md` and `index.md` accordingly.
5. Bump `last_synced_commit` on every page touched.
6. Append a `log.md` entry: files changed, pages updated, commit hash.

## Code lint workflow (periodic)

1. For every `Module`/`Architecture` page, check whether `last_synced_commit` is behind the current HEAD for its cited files.
2. Flag (don't silently fix) claims whose cited lines have changed since last sync — these need a human or a sync pass, not an automatic rewrite.
3. Flag orphan pages (module deleted from the codebase) and missing pages (module with no wiki page).
4. Report findings in `log.md` rather than editing pages directly during lint.

## Query workflow

1. Read `index.md` first to identify relevant pages. For code questions, read `wiki/architecture.md` before any module page.
2. Use links and summaries to choose the best wiki pages.
3. Answer using wiki content and its citations. Only open the actual source file if a citation needs verifying or the wiki doesn't yet cover the question.
4. If the answer required reading raw source because the wiki didn't cover it, treat that as a sync gap — create or update the relevant `wiki/` page afterward and log it.
5. If the answer is a lasting insight not yet in the wiki, create a page and update the index and log.

## Index format

`index.md` should include:

- category sections (Documents, Modules, Architecture)
- page links
- short one-line summaries
- metadata such as tags, source count, or last-synced commit when useful

## Log format

`log.md` should include:

- date-stamped headings
- action type (ingest, sync, lint, synthesis, review)
- source or commit references
- brief summary of changes

## First ingest example — code project

- Bootstrap reads `src/`, writes `wiki/modules/auth.md`, `wiki/modules/api.md`, `wiki/architecture.md`
- `index.md` gets an Architecture section and a Modules section
- `log.md` gets one entry: "Bootstrap: 6 modules, 2 flagged as partially covered, commit a1b2c3d"

## Rules for every response

1. Use the schema: Purpose, Folder structure, Workflow, Example.
2. Be concise but complete.
3. Reference actual files in the workspace, with citations for code claims.
4. Keep each response aligned with the second-brain system.
