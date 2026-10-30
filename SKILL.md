---
name: ddcs-v3-dm500
description: Rules for writing G-code and macros for the DDCS V3 / DM500 CNC controller (not the DDCS V4.1 or the Expert / M350, not Fanuc Macro B): syntax, variables and hazards (declared forms, not bench-measured yet).
---

# DDCS V3 / DM500 — Rulebook

> For the DDCS V4.1, use the `ddcs-v41` skill; for the DDCS Expert / M350, use the `ddcs-expert` skill instead: this one is for the DDCS V3 / DM500 only.

> Nothing in this file is bench-measured.


> ⚠ **Nothing in this pack has been measured on a DDCS V3 / DM500.** Its rules are declared forms, not bench tests. Treat every rule as unverified on the machine.

## The rules

0. This controller is NOT Fanuc Macro B and NOT another DDCS model: do not carry habits from either.
1. Jump with `IF #190LT#191 GOTO5` and a label line `N5`.
2. Never write an inline `IF .. THEN` assignment: write a negated jump, the assignment, then the label (syntax.md).
3. Start every line at column 0.
4. A comment is `( text )` with no `(` or `)` inside it; a nested `)` can refuse the whole file.
5. An operator prompt is the instruction in a comment followed by `M0`: `( Check the clamp )` then `M0`.
6. `vendor/` holds the vendor's own macro and dialog files ONLY when the person has put them there (a fresh copy of this repository has none; `vendor/README.md` says which files and where from). If `vendor/macros/<file>` or `vendor/dialogs/<file>` exists, read it FIRST and say which firmware version it came from (`vendor/VERSION.txt` if they wrote one, else ask: it may be newer than the firmware this pack was measured on); if it does not exist, use the lines this pack quotes from it (`vendor/README.md`, `evidence.md`). Neither is a bench test, and other people's community notes are not re-published here: where either disagrees with this pack's measured files, the measured files win (`community/conflicts.md` lists each known disagreement).

## Read next

- `differences.md`
- `syntax.md`
- `variables.md`
- `dialogs.md`
- `hazards.md`
- `operation.md`
- `ui.md`
- `integration.md`
- `evidence.md`
- `examples/`
- `community/`
- `vendor/`
