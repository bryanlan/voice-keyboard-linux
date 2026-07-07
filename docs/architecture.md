---
doc_type: architecture
managed_by: sync-repo-docs
current_through_commit: 7fdaf8bdf2523d710854ccba0530870c9cc624db
current_through_date: 2026-07-04T01:18:25-04:00
---

# Architecture
## System Overview
`voice-keyboard-linux` is a Rust Linux voice keyboard. It creates a virtual keyboard through `/dev/uinput`, drops from root back to the invoking user for desktop audio access, streams microphone audio to Deepgram Flux over WebSocket, and types transcript deltas into the active application.

First-class runtime surfaces:
- Binary entrypoint and privilege model: `src/main.rs`.
- Virtual keyboard/uinput layer and transcript diffing: `src/virtual_keyboard.rs` and `src/input_event.rs`.
- Audio capture: `src/audio_input.rs`.
- Deepgram Flux WebSocket client and audio chunking: `src/stt_client.rs`.
- Runner script: `run.sh`.
- Cargo manifest: `Cargo.toml`.

## Main Components
- `src/main.rs` parses `--test-audio`, `--test-stt`, `--debug-stt`, `--stt-url`, `--voice-enter`, and `--uppercase`; creates the real keyboard hardware while privileged; drops group/user privileges; then runs audio, debug STT, or typing STT mode.
- `OriginalUser` in `main.rs` captures `SUDO_UID`, `SUDO_GID`, `HOME`, `USER`, `PULSE_RUNTIME_PATH`, `XDG_RUNTIME_DIR`, `DISPLAY`, and `WAYLAND_DISPLAY` so audio/session access survives `sudo -E` and privilege dropping.
- `src/virtual_keyboard.rs` owns the `KeyboardHardware` abstraction, `RealKeyboardHardware` uinput implementation, transcript update logic, uppercase mode, and optional voice-enter command handling.
- `src/input_event.rs` defines Linux input event structs/constants and character-to-keycode mappings. The current branch includes explicit digit keycode mappings.
- `src/audio_input.rs` uses `cpal` to locate the default input device, normalize `f32`/`i16`/`u16` samples, and stream audio callbacks.
- `src/stt_client.rs` connects to `wss://api.deepgram.com/v2/listen` by default, adds `Authorization: Token <DEEPGRAM_API_KEY>` when present, parses Flux `Connected`, `TurnInfo`, `Error`, and `Configuration` messages, and sends `CloseStream` after audio input closes.
- `run.sh` builds the crate and runs `sudo -E ./target/debug/voice-keyboard`.
- `CLAUDE.md` is a symlink to `AGENTS.md`; do not replace it with a regular file.

## Data Flow
The app starts as root or via `sudo -E`, creates a uinput device, enables the required key codes, writes the uinput device descriptor, and then drops to the original user. After the drop, audio capture uses the user's PipeWire/PulseAudio session through `cpal`.

In STT mode, `AudioInput` sends sample buffers through an `AudioBuffer` to `SttClient`. The client sends linear16 audio frames over WebSocket to Deepgram Flux and receives `TurnInfo` updates. `VirtualKeyboard::update_transcript` compares each new transcript to the currently typed text, backspaces only the changed suffix, and types the new suffix. At end-of-turn it finalizes tracking; when `--voice-enter` is enabled, an end-of-turn transcript ending in an enter command can press Enter.

## External Integrations
- Linux `/dev/uinput` is required for real keyboard output and requires elevated access.
- PipeWire/PulseAudio or another `cpal`-supported input backend is required for microphone capture after privilege drop.
- Deepgram Flux is the default STT service. `DEEPGRAM_API_KEY` is optional at process startup but required for authenticated live service use.
- `--stt-url` can point to a custom WebSocket-compatible STT endpoint for debugging or proxy use.

## Key Decisions
- Keep the privilege window minimal: create keyboard hardware first, then drop privileges before initializing audio.
- Use `sudo -E` for manual runs so audio/session and STT environment variables survive privilege escalation.
- Keep transcript-update behavior conservative; the product contract is minimal backspacing, stable digit/key mappings, optional uppercase conversion, and optional voice-enter behavior.
- Treat live STT checks separately from local Rust tests because failures can be credentials, network, or service-contract issues.
- Managed docs are synchronized against the live tree and finalized to the current git `HEAD`; commit dossier files are navigation context, not source of truth.

## Operational Notes
Use `docs/agent_docs/running_tests.md` for safe verification commands. `target/` is generated Cargo output and is not part of the authored runtime docs.
