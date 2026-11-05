# Changelog

All notable changes to this module are documented in this file. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/). The module version follows the Figram
release whose libraries it bundles.

## [0.3.0] - 2026-11-04

Bundles the libraries of
[Figram v0.3.0](https://github.com/Ruddy35/figram-fsm-library/releases/tag/v0.3.0), which speak
protocol version 3. Not compatible with Figram v0.2.0 or v0.1.0: use ffl-go v0.2.0 or v0.1.0
with them.

### Added

- `Bot.Outcome(snapshot, action, outcome, token, guards)`: deliver the outcome of a call of an
  action with outcomes (the event `call:<action>:<outcome>` with the call's token). The engine
  accepts it once, while the snapshot waits for that call, and handles it by the arrows of the
  message the user is on; a stale or repeated outcome changes nothing. `Need` and `Decide` work
  as for `Step`. A malformed outcome (empty, or with `:`) is refused before it is sent.
- `Snapshot.Pending` (JSON `pending`, a list of `Wait{Call, Token}`, newest first): the calls
  whose outcome is awaited. Store it with the rest of the snapshot; `Restart` clears it.
- `Action.Outcomes` and `Action.Token` of a `Call` (the action has outcomes: deliver one with
  that token) and `Action.Detached` (a background action: run it later and in any order).
- `CodeSnapshotTooLarge` (`snapshot_too_large`): under the request size limit the engine refuses
  a step whose new snapshot would take more than 15/16 of the limit; the stored snapshot still
  works for its outcomes and `Restart`.
- Tests: the golden scenarios of v0.3.0 (outcomes with their tokens, background actions,
  arrows without a target), `Outcome` with a stale, an accepted and a repeated token and with
  `Decide` and with an action name containing `:`, `Timer` with `Need`, the snapshot size limit
  (also with names that JSON encoders escape), invalid UTF-8, the protocol 3 rows and every
  field check of the reply validator, an example, and 21 more wrapper mutants (37).

### Changed

- The libraries, the header and the test data of Figram v0.3.0 (`BundledVersion` 0.3.0). The
  runtime of v0.3.0 reserves a 64 MiB arena when the address space allows it (about 64 MiB more
  address space, no more memory in use).
- `ProtocolVersion` is 3; `Load` rejects a library of another protocol version. The reply
  validator accepts `pending` in snapshots and `token` and `detached` in `call` actions.

### Fixed

- Requests are encoded as the engine encodes JSON: `&`, `<`, `>` without HTML escapes, U+2028 and
  U+2029 as UTF-8. With the escapes of `encoding/json`, a snapshot the engine kept within 15/16 of
  the request limit could exceed the limit when sent back, and its outcomes and `Restart` failed
  with `too_large` (found by an audit of this release).
- A request whose strings are not valid UTF-8 fails with `CodeParseError` before it is sent; before,
  `encoding/json` replaced the invalid bytes with U+FFFD and the engine got another event or
  snapshot.
- The reply validator rejects duplicate entries in `need` and any text after the reply (before, a
  `}` or `]` after it passed).
- `Result.MarshalJSON` writes the fields in the engine's order (`snapshot`, `actions`,
  `recovered`).
- Documentation: a stale or repeated outcome or timer changes nothing unless the snapshot's
  message is no longer in the document (`Recovered`); `Restart` clears the waits and `back`;
  `CodeSnapshotTooLarge` also comes from timers and remembered places.

### Known issues

- The maps `Snapshot.Resume` and `Snapshot.Timers` do not keep the order of the engine's keys. The
  engine cancels the timers of a `Restart` or a recovery in the order of the snapshot's keys, so
  the `cancel_timer` actions may come in another order than for the engine's own JSON; for a
  scheduler that follows the host contract the order does not matter. To reproduce the engine's
  replies byte for byte, store the raw reply lines of `Bot.Request`.

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
