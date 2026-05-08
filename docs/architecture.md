---
doc_type: architecture
managed_by: sync-repo-docs
current_through_commit: eea2db2e94bce962ae3e970d248288f7c387f054
current_through_date: 2026-05-05T07:24:31-07:00
---

# Architecture

## System Overview

`voice-keyboard-linux` is a Rust application that turns speech into typed Linux keyboard input. It captures microphone audio, streams PCM16 chunks to Deepgram Flux over WebSocket, and applies transcript updates through a Linux uinput virtual keyboard.

The runtime is built around a privilege split: the process starts with enough permission to create `/dev/uinput`, then drops back to the original sudo user before opening the desktop audio session. That is why `run.sh` and manual usage rely on `sudo -E`.

## Main Components

- `src/main.rs`: CLI parsing, original-user capture, root-to-user privilege drop, audio/STT orchestration, and transcript event dispatch.
- `src/virtual_keyboard.rs`: uinput hardware implementation plus the business logic for incremental transcript edits, optional voice-enter handling, and optional uppercase conversion.
- `src/stt_client.rs`: Deepgram Flux WebSocket client, auth header handling from `DEEPGRAM_API_KEY`, server-message parsing, close-stream behavior, and live-service tests.
- `src/audio_input.rs`: CPAL microphone discovery and sample-format normalization into `f32`.
- `src/input_event.rs`: Linux input/uinput structs and keycode mappings used by the keyboard hardware layer.
- `run.sh`: builds the crate and runs the debug binary with `sudo -E`.

## Data Flow

1. `main.rs` captures `SUDO_UID`, `SUDO_GID`, audio/session environment variables, and CLI flags before doing privileged work.
2. `RealKeyboardHardware::new()` opens `/dev/uinput`, enables the required keycodes, writes the uinput device descriptor, and creates the "Voice Keyboard" device.
3. The process drops group and user privileges back to the original sudo user and restores audio/session environment variables.
4. `AudioInput` opens the default CPAL input device. Audio callbacks average stereo input to mono, buffer it into 16-bit PCM chunks, and send chunks to `SttClient`.
5. `SttClient` connects to `wss://api.deepgram.com/v2/listen` by default, adding `Authorization: Token <DEEPGRAM_API_KEY>` when the environment variable is present.
6. Flux `TurnInfo` events are converted into `TranscriptionResult` callbacks. Non-`Update` events are always logged; plain update logs are rate-limited.
7. For transcript updates, `VirtualKeyboard` finds the common prefix with the currently typed text, backspaces only the changed suffix, and types the new suffix.
8. On `EndOfTurn`, the keyboard finalizes the transcript. If `--voice-enter` was passed and the text ends with the standalone word `enter` plus optional punctuation/whitespace, the matched command is removed and an Enter keypress is emitted. Without `--voice-enter`, no Enter keypress is injected.

## Runtime Modes

- `--test-audio`: lists input devices and logs audio levels for five seconds after the keyboard device is created and privileges are dropped.
- `--test-stt`: streams microphone audio to Flux and types transcript updates through the virtual keyboard.
- `--debug-stt`: streams audio and logs transcripts without typing.
- `--stt-url <URL>`: overrides the Flux WebSocket endpoint.
- `--voice-enter`: enables spoken "enter" command handling at end-of-turn.
- `--uppercase`: converts all typed transcript text to uppercase before diffing and typing.

## Operational Notes

- `cargo test virtual_keyboard::tests` is the best hermetic local regression check for transcript diffing, uppercase mode, and voice-enter behavior.
- Full `cargo test` includes live Flux tests in `src/stt_client.rs`; those require valid service credentials and network access.
- Running the binary requires Linux with `/dev/uinput`, a working default CPAL input device, and preserved desktop audio environment variables.
- The repo may have a generated `target/` directory after local builds/tests; it is not authored source.
