---
doc_type: fileindex
managed_by: sync-repo-docs
current_through_commit: df9c977eec4e0d4706f7bf6640ccf0ed1ad2faf6
current_through_date: 2026-06-28T00:49:06-04:00
---

# File Index
## Top-Level Layout
- `README.md` - install, run, Deepgram Flux, troubleshooting, and architecture notes.
- `AGENTS.md` - repo operating rules. `CLAUDE.md` is a symlink to this file.
- `Cargo.toml` and `Cargo.lock` - Rust crate manifest and resolved dependencies.
- `run.sh` - local runner that builds and invokes the binary with `sudo -E`.
- `src/` - Rust source for keyboard, audio, STT, and input-event logic.
- `target/` - generated Cargo build/test output.
- `docs/` - managed repo-doc sync files.

## Key Directories
- `src/` - runtime source modules.
- `docs/agent_docs/` - doc-sync status, commit dossier, and testing guidance.
- `target/debug/` and `target/release/` - generated binaries and test artifacts.

## Key Files
- `src/main.rs` - CLI parsing, privilege capture/drop, mode dispatch, audio/STT loop, and keyboard wiring.
- `src/virtual_keyboard.rs` - uinput keyboard hardware implementation, transcript delta/backspace logic, uppercase mode, voice-enter handling, and unit tests.
- `src/input_event.rs` - Linux input structs, uinput constants, keycode constants, and `char_to_keycode` mapping.
- `src/audio_input.rs` - default input-device selection and typed sample conversion for `cpal`.
- `src/stt_client.rs` - Deepgram Flux WebSocket client, auth header, server-message parsing, `CloseStream`, and audio buffering.
- `run.sh` - recommended wrapper for build plus `sudo -E`.
- `Cargo.toml` - dependencies including `cpal`, `tokio-tungstenite`, `nix`, `clap`, `serde`, `regex`, and tracing.

Test and verification anchors:
- `virtual_keyboard::tests` in `src/virtual_keyboard.rs` - local hermetic transcript/key behavior coverage.
- `cargo test --no-run` - compiles the full test binary without executing live STT paths.

## Change Hotspots
- Keyboard text behavior changes should review `virtual_keyboard.rs`, `input_event.rs`, and `cargo test virtual_keyboard::tests` together.
- Privilege or runner changes should review `main.rs`, `run.sh`, README troubleshooting, and audio environment preservation together.
- STT protocol changes should review `stt_client.rs`, README Flux contract notes, and live credential expectations together.
- Audio capture changes should review `audio_input.rs`, privilege-drop sequencing, and PipeWire/PulseAudio troubleshooting.

## Deferred or Unclear Areas
- Full live STT execution is intentionally not a default docs-sync check because it needs service credentials, audio devices, and `/dev/uinput` access.
- README Flux details can drift with the external service; verify provider docs or live behavior before changing protocol assumptions.
