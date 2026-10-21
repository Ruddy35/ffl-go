# Changelog

All notable changes to this module are documented in this file. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/). The module version follows the Figram
release whose libraries it bundles.

## [0.1.0] - 2026-10-21

First release, bundling the libraries of
[Figram v0.1.0](https://github.com/Ruddy35/figram-fsm-library/releases/tag/v0.1.0).

### Added

- Package `figram`: `Init`, `SetMaxRequest`, `Check`, `Fmt`, `Load`; `Bot` with `Start`,
  `Restart`, `Step`, `Timer`, `Hello`, the raw `Request` and `Close`; `Decide` for conditions.
- Typed protocol values `Snapshot`, `Action`, `Result`, `Hello` with their JSON forms, and the
  errors `DocumentError`, `ProtocolError` (with the protocol's codes) and `ErrClosed`. Every
  reply of the engine is checked strictly.
- The static libraries of Figram v0.1.0 for linux/amd64 in `lib/linux_amd64` (vendoring
  supported), and the build tag `figram_system` for a library found with pkg-config.
- Tests: the golden scenarios of the release, concurrency and `Close` under load, protocol
  errors, process tests (lazy start, signal dispositions, RLIMIT_AS), runnable examples, a
  consumer-module build test and wrapper mutants; `scripts/update-lib.sh` to update the bundled
  release and check it.
