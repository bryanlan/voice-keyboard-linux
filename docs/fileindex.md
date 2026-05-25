---
doc_type: fileindex
managed_by: sync-repo-docs
current_through_commit: 90c6ef13223d0b20945727cb5018f9479e2822ab
current_through_date: 2026-05-25T02:03:12-07:00
---

# File Index
## Top-Level Layout
- `docs/` - repository documentation and managed doc-sync metadata.
- `src/` - primary source code.

## Key Directories
- `docs/` - repository documentation and managed doc-sync metadata.
- `src/` - primary source code.

## Key Files
- `README.md` - key tracked file or entrypoint for this repo.
- `AGENTS.md` - key tracked file or entrypoint for this repo.
- `CLAUDE.md` - key tracked file or entrypoint for this repo.

Test and verification anchors:
- No tracked test files were identified.

## Change Hotspots
- Runtime entrypoint changes should be reviewed with adjacent service, route, CLI, or frontend modules and the tests that exercise them.
- Manifest or dependency changes should be reviewed with setup docs and `docs/agent_docs/running_tests.md`.
- Documentation-only changes should stay scoped to managed docs unless source-of-truth operator docs are stale.
- When recent commits rename, split, or demote modules, verify whether the old file still owns behavior or only delegates to newer modules.

## Deferred or Unclear Areas
- This rollout used live manifests, README content, tracked file layout, and representative source paths. Confirm deeper domain semantics in source before large behavior changes.
