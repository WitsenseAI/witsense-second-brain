---
description: Incrementally sync the code wiki after a code change
model: claude-sonnet-5
---

Run the code sync workflow defined in `claude.md` under "Code sync workflow — incremental".

1. Diff the working tree against each wiki page's `last_synced_commit`.
2. If `wiki/modules/` is empty, stop and suggest `/bootstrap` instead.
3. For each changed file, find every wiki page citing it.
4. Re-read only the changed regions; update only the affected claims/citations — don't rewrite whole pages.
5. Update `wiki/architecture.md`/`index.md` only if modules were added or removed.
6. Bump `last_synced_commit` on every page touched.
7. Append one `log.md` entry: files changed, pages updated, commit hash.

Report back: pages updated, and any changed files with no citing page yet (a sync gap — flag it, don't silently expand scope).

**Why this runs on a cheaper model**: routine sync is narrow and mechanical — find the pages that cite a changed file, update the affected lines. It doesn't require re-deriving architecture, so it doesn't need a planning-tier model. If a change is large enough that sync would need to rethink module boundaries (not just update citations), stop and suggest re-running with a stronger model instead of pushing through on this one.
