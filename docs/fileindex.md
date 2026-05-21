---
doc_type: fileindex
managed_by: sync-repo-docs
current_through_commit: 8bc06f6ecb3449077ac3d9da76fa066f9b8fc749
current_through_date: 2026-05-13T02:11:30-07:00
---

# File Index

## Top-Level Layout

- `src/`: main Rust source for CLI orchestration, audio capture, Flux WebSocket handling, and virtual keyboard output.
- `docs/`: managed repo docs and sync metadata.
- `Cargo.toml` / `Cargo.lock`: crate manifest and locked dependency graph.
- `run.sh`: local runner that builds and invokes the binary with `sudo -E`.
- `flake.nix` / `shell.nix`: Nix development shell definitions.
- `target/`: generated Rust build/test artifacts when present.

## Key Directories

- `src/`: authored runtime and unit-test code.
- `docs/agent_docs/`: managed sync state, commit dossier, and current verification notes.

## Key Files

- `src/main.rs`: entrypoint, clap flags, privilege-drop flow, audio/STT loop, and event-to-keyboard dispatch.
- `src/virtual_keyboard.rs`: uinput keyboard implementation, transcript diffing, voice-enter command handling, uppercase mode, and mock-backed unit tests.
- `src/stt_client.rs`: Deepgram Flux client, `DEEPGRAM_API_KEY` auth header, server message schema, audio channel/CloseStream handling, and live STT tests.
- `src/audio_input.rs`: CPAL input-device setup and sample conversion.
- `src/input_event.rs`: Linux input constants, key maps, and uinput structs.
- `README.md`: operator-facing setup and Flux contract notes; verify CLI/options against `src/main.rs` when behavior changes.
- `AGENTS.md`: agent-facing repo guardrails and verification commands.
- `run.sh`: recommended local execution helper from the repo root.

## Change Hotspots

- `src/main.rs`, `src/stt_client.rs`, and `README.md` move together when Flux endpoint/auth/protocol assumptions change.
- `src/main.rs`, `src/audio_input.rs`, and `run.sh` move together when privilege dropping or audio-session setup changes.
- `src/virtual_keyboard.rs` is the main hotspot for typing behavior, transcript correction, voice-enter command detection, and uppercase mode.
- `Cargo.toml` / `Cargo.lock` change with CPAL, Tokio, tungstenite, nix, clap, serde, or tracing dependency updates.
- `docs/` should move when command-line modes, service assumptions, or test expectations change.

## Deferred or Unclear Areas

- Live-service authentication materially affects the STT tests in `src/stt_client.rs`.
- `run.sh` prevents direct root execution, but manual runs can still bypass that guard; preserve the documented privilege model.
- The README's option list can drift from `src/main.rs`; `--voice-enter` and `--uppercase` are source-of-truth flags in the current code.
