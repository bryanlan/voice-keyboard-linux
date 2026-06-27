---
doc_type: running_tests
managed_by: sync-repo-docs
current_through_commit: a717bb9e317f2b6457d87f36873b8d8a0626c072
current_through_date: 2026-06-25T01:50:17-04:00
---

# Running Tests
## Primary Commands
- `cargo test virtual_keyboard::tests` - hermetic unit coverage for transcript update, backspacing, uppercase, digit/key mappings, and voice-enter behavior; passed on 2026-06-14 with 22 tests.
- `cargo build` - compile the debug binary; passed on 2026-06-14.
- `cargo test --no-run` - compile the full test binary without running live STT checks; passed on 2026-06-14.

## Targeted Test Patterns
- Keyboard/keycode/transcript changes: `cargo test virtual_keyboard::tests`.
- Broad compile check without live service execution: `cargo test --no-run`.
- Manual audio check: `./run.sh --test-audio` only when a local microphone/audio session and `/dev/uinput` flow should be exercised.
- Manual STT debug check: `./run.sh --debug-stt` or `./run.sh --test-stt` only when `DEEPGRAM_API_KEY`, audio input, network, and uinput permissions are intentionally in scope.

## Environment and Fixtures
- Install Rust/Cargo plus system audio headers such as `alsa-lib-devel` or `libasound2-dev`.
- Use `sudo -E` for manual binary runs so `DEEPGRAM_API_KEY`, `XDG_RUNTIME_DIR`, `PULSE_RUNTIME_PATH`, `DISPLAY`, and `WAYLAND_DISPLAY` survive privilege escalation.
- `/dev/uinput` and the `uinput` kernel module are required for real keyboard output.
- `CLAUDE.md` is a symlink to `AGENTS.md`; do not replace it during docs or guidance edits.

## Edge Cases
- Treat deploy, restore, migration, promotion, scheduler, and production data commands as operational workflows, not tests.
- Live STT failures may be missing credentials, service auth, network, or Deepgram contract drift rather than Rust regressions.
- Audio failures after `sudo` are often environment-preservation or session-access issues; verify `sudo -E` and PipeWire/PulseAudio first.
- Do not use generated `target/` artifacts as source-of-truth documentation.

## Known Gaps
- Default docs-sync verification does not exercise real audio, real `/dev/uinput` typing, or live Deepgram Flux responses.
