---
description: Check the code wiki for staleness, orphan pages, and missing citations
model: claude-sonnet-5
---

Run the code lint workflow defined in `claude.md` under "Code lint workflow".

1. For every Module/Architecture page, compare `last_synced_commit` against current HEAD for its cited files.
2. Flag (don't fix) pages whose cited lines changed since last sync.
3. Flag orphan pages (module deleted from the codebase) and missing pages (module with no wiki page).
4. Flag any claim missing a `path:line` citation.
5. Append one `log.md` entry summarizing findings. Do not edit wiki pages during lint.
