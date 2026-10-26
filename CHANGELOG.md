# Changelog

All notable changes to this module are documented in this file. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/). The module version follows the Figram
release whose libraries it bundles.

## [0.2.0] - 2026-10-26

Bundles the libraries of
[Figram v0.2.0](https://github.com/Ruddy35/figram-fsm-library/releases/tag/v0.2.0), which speak
protocol version 2. Not compatible with Figram v0.1.0: use ffl-go v0.1.0 with it.

### Added

- `Bot.StartWith(seq, event, guards)`: start a new user with the update's event (a command, a
  button of an old message). The event is matched as if the user stood on the initial message;
  the result may have `Need`, as for `Step`, and `Decide` works with it. A timer event is a
  `bad_request`.
- `Snapshot.Back` (JSON `back`): the message "back" arrows lead to, which the engine writes after
  a move across a section boundary. Store it with the rest of the snapshot.
- `ProtocolVersion` (2); `Load` rejects a library that speaks another protocol version (possible
  with the build tag `figram_system`).
- Tests: the golden scenarios of v0.2.0 (`start` with an event, "back"), `StartWith` with `Need`
  and `Decide`, a recovered step that processes its event, and six more wrapper mutants (16).

### Changed

- The libraries, the header and the test data of Figram v0.2.0 (`BundledVersion` 0.2.0).
- A reply to `start` may be `need`; `Hello.Protocol` is 2.
- `Result.Recovered`: the event of a recovered step is processed now (the old timers are
  cancelled and the remembered places kept); do not deliver it again.

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
