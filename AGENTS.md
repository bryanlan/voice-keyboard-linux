# AGENTS.md

This repo is a Rust voice keyboard for Linux that streams audio to Deepgram Flux and types transcript updates through a virtual keyboard device.

## Quick Rules
- Respect the privilege model: create the virtual keyboard with elevated access, then drop privileges for audio/session access.
- Use `sudo -E` when running the binary manually so the audio/session environment survives privilege escalation.
- Some STT tests hit live service/auth paths; a red test can be a credentials problem rather than a Rust regression.
- Keep transcript-update behavior conservative; real-time backspacing logic is part of the product contract.

## Build / Test / Verify
- Install: `cargo build`
- Dev: `./run.sh`
- Test: `cargo test virtual_keyboard::tests`
- Verify: `cargo test virtual_keyboard::tests`

## Repo Map
- `src/` — STT client, transcript handling, and virtual keyboard logic.
- `docs/` — managed architecture, file index, and test guidance.
- `run.sh` — recommended local runner.
- `Cargo.toml` — crate manifest and dependency contract.

## Repo-Specific Guardrails
- Keyboard correctness and Linux input-device permissions matter as much as STT correctness.
- The live Flux tests are not hermetic; keep local-unit and service-auth failures mentally separate.
- README assumptions are service-specific and can drift quickly if provider/auth behavior changes.
- Full `cargo test` currently includes live STT-client checks that can fail on missing service credentials.

## Additional References
- `docs/architecture.md` — runtime surfaces and privilege/audio flow.
- `docs/fileindex.md` — key files and change hotspots.
- `docs/agent_docs/running_tests.md` — current `cargo test` expectations and caveats.
- `README.md` — install, run, and Deepgram Flux contract notes.
