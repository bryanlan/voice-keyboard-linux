---
doc_type: running_tests
managed_by: sync-repo-docs
current_through_commit: 35e4b5f281765a144c32c411ee415f34766f9930
current_through_date: 2026-05-08T02:22:43-07:00
---

# Running Tests

## Primary Commands

- `cargo test virtual_keyboard::tests`
  - Primary hermetic check for the local product contract: incremental typing, smart backspacing, voice-enter command detection, voice-enter disabled behavior, and uppercase mode.
- `cargo test`
  - Full Rust suite. This also runs live Deepgram Flux tests from `src/stt_client.rs`, so it is not a clean offline gate unless valid credentials/network are available.
  - Current result on this sync pass: 23 local tests passed and 2 live STT tests failed with `401 Unauthorized` / `INVALID_AUTH`.

## Targeted Test Patterns

- `cargo build`
  - Compile-only check for the binary and dependency graph.
- `cargo test virtual_keyboard::tests::<name>`
  - Focus one transcript/keyboard behavior case.
- `cargo test stt_client::tests::<name>`
  - Rerun only a live Flux case while debugging credentials, endpoint behavior, or message schema changes.
- `./run.sh --debug-stt`
  - Manual end-to-end STT smoke without typing into other applications.
- `./run.sh --test-stt --voice-enter --uppercase`
  - Manual smoke for the current optional typing modes.

## Environment and Fixtures

- Rust tooling is enough for build/unit tests; runtime use also needs Linux `/dev/uinput` access and a working CPAL default input device.
- `DEEPGRAM_API_KEY` must be set for the live STT tests and normal Flux usage. The client sends `Authorization: Token <DEEPGRAM_API_KEY>`.
- `STT_TEST_URL` can override the default endpoint for the live STT tests.
- Use `sudo -E` for manual binary runs so `DEEPGRAM_API_KEY`, `XDG_RUNTIME_DIR`, `PULSE_RUNTIME_PATH`, display variables, and other session settings survive elevation.

## Edge Cases

- Full-suite failures in `stt_client::tests` can be auth/network/provider failures rather than Rust regressions.
- `--voice-enter` is opt-in in `src/main.rs`; without it, an end-of-turn clears internal tracking and does not press Enter.
- `--uppercase` transforms the transcript before diffing, so tests for casing should inspect both `current_text` and emitted mock hardware characters.
- Unit tests do not exercise the real uinput device, sudo flow, CPAL device availability, or desktop focus behavior.

## Known Gaps

- No automated test currently covers real `/dev/uinput` event injection or desktop application focus.
- No automated test covers `run.sh` itself.
- The external-service tests are intentionally not hermetic because they hit live Flux auth and WebSocket behavior.
