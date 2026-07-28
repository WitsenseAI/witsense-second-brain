---
description: Bootstrap the code wiki from scratch (first run on a codebase)
model: claude-opus-4-8
---

Run the code ingest workflow defined in `claude.md` under "Code ingest workflow — bootstrap".

Before starting, check `wiki/modules/` — if it already has pages, stop and ask whether to rebootstrap fully or run `/sync` instead.

1. Read the project's dependency/entry-point files first (e.g. `package.json`, `pyproject.toml`, `main.py`, `src/index.ts`) to establish the module map.
2. Walk the source tree module by module, not file by file. Group tightly-coupled files into one pass.
3. For each module write `wiki/modules/<module-name>.md`: purpose, public interface, key dependencies, non-obvious design decisions — every claim cited with `path:line`. Do not reproduce code, describe it.
4. Write `wiki/architecture.md`: how modules connect, request/data flow, entry points.
5. Set `last_synced_commit` in each new page's frontmatter to current HEAD.
6. Update `index.md` (Architecture + Modules sections).
7. Append one `log.md` entry: module count, commit hash, anything deliberately left uningested.

Report back: modules written, commit hash, anything skipped.

**Why this runs on a planning-tier model**: bootstrap requires holistic judgment across the whole codebase at once — deciding what the 20% of structure is that explains 80% of the system. That's the one step worth paying for. Once the wiki exists, `/sync` and everyday coding can run on a much cheaper model.
