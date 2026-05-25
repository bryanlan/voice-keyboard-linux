---
doc_type: fileindex
managed_by: sync-repo-docs
current_through_commit: 8049d4516ff9a953d9690e6feb87d342d90db1d5
current_through_date: 2026-05-24T19:02:39-07:00
---

# File Index
## Top-Level Layout
- `docs/` - top-level directory in the current tree.
- `src/` - top-level directory in the current tree.

## Key Directories
- `docs/` - contains `docs/agent_docs/agents_md_status.json`, `docs/agent_docs/commit_dossier.json`, `docs/agent_docs/commit_dossier.md`, `docs/agent_docs/doc_status.json`, `docs/agent_docs/running_tests.md`.
- `src/` - contains `src/audio_input.rs`, `src/input_event.rs`, `src/main.rs`, `src/stt_client.rs`, `src/virtual_keyboard.rs`.

## Key Files
- `README.md` - repository configuration, entrypoint, or operator documentation.
- `AGENTS.md` - repository configuration, entrypoint, or operator documentation.
- `CLAUDE.md` - repository configuration, entrypoint, or operator documentation.
- `Cargo.toml` - repository configuration, entrypoint, or operator documentation.
- Test anchors: `docs/agent_docs/running_tests.md`.

## Change Hotspots
- `src/` changes should be reviewed with adjacent tests and docs.
- `docs/` changes should be reviewed with adjacent tests and docs.
- When touching manifests or runtime entrypoints, update this file and `docs/architecture.md` in the same change.

## Deferred or Unclear Areas
- This automated rollout used live manifests, README content, and tracked file layout; deeper domain semantics should be confirmed in representative source before large behavior changes.
