---
doc_type: architecture
managed_by: sync-repo-docs
current_through_commit: 8049d4516ff9a953d9690e6feb87d342d90db1d5
current_through_date: 2026-05-24T19:02:39-07:00
---

# Architecture
## System Overview
voice-keyboard-linux is documented from the current repository tree. Voice Keyboard; Voice keyboard is a demo application showcasing Deepgram's new turn-taking speech-to-text API: Flux.; A voice-controlled Linux virtual keyboard that converts speech to text and types it into any application.; As a result of directly targeting Linux as a driver, this works with all Linux applications.
The primary human overview is `README.md`; this managed doc is a navigation and architecture companion for agents.

## First-Class Runtime Surfaces
- Rust crate configured by Cargo.toml.
- Runtime or support surface under src/.

## Main Components
- `docs/` is a top-level area present in the live tree; see `docs/fileindex.md` for navigation details.
- `src/` is a top-level area present in the live tree; see `docs/fileindex.md` for navigation details.

## Data Flow
Start from the runtime surfaces above, then follow imports into the component directories. Treat generated outputs, caches, and reports as derived artifacts unless a repo-specific README says otherwise.

## External Integrations
No external integration can be asserted from filenames alone; verify provider clients in source before changing integration behavior.

## Key Decisions
- Managed docs are synchronized against the live tree and finalized to the current git `HEAD`; commit dossier files are context, not source of truth.
- Future agents should verify ownership in source files before preserving older compatibility paths or workaround behavior.

## Operational Notes
Use `docs/agent_docs/running_tests.md` for safe verification commands. Do not infer deploy, restore, or production-mutating workflows from test documentation.
