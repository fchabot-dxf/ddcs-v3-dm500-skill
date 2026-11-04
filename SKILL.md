---
name: ddcs-v3-dm500
description: Rules for writing G-code and macros for the DDCS V1.1 / V2.1 / V3.1 / V3.2 and DM500 CNC controllers (not the DDCS V4.1, not the DDCS Expert / M350, not Fanuc Macro B): syntax, variable boundaries (#0-#999), WCS selector #455, dwell in seconds, hazards, and vendor stock macros; reverse-engineered from firmware build 2020-03-06-112.
---

# DDCS V3 / DM500 — Rulebook

> For the DDCS V4.1, use the `ddcs-v41` skill; for the DDCS Expert / M350, use the `ddcs-expert` skill instead: this one is for the DDCS V1.1, V2.1, V3.1, V3.2, and DM500 handheld pendant controller only.
> 
> **Grounding:** Fully reverse-engineered from official Digital Dream firmware `install(2020-03-06-112)` binary `motion.out` (ARMv7-A 32-bit little-endian ELF, Flex DFA lexer at `0x000330a8`, BYACC 1.9 LALR(1) parser at `0x00051d0c`) and factory macro library `slib.nc`. Target hardware signatures: `DDCSV1.1@DDREAM`, `DDCSV2.1@DOSEDONE`, `DDCSV3.1@DDREAM`, `DDCSV3.2@DDREAM`, `Ver:2020-03-06-112`.

## The rules

0. This controller is NOT Fanuc Macro B, NOT the DDCS Expert, and NOT the DDCS V4.1: do not carry habits from either (`differences.md`).
1. **Jump Syntax:** Jump with `IF #190 LT #191 GOTO5` (or `IF #190 < #191 GOTO5`) and a destination label line `N5` (`syntax.md`).
2. **No Inline `THEN`:** Never write an inline `IF .. THEN` assignment. `THEN` does not exist in the lexer/grammar. Write a negated condition jump, the assignment, and a destination label.
3. **Start Every Line at Column 0:** Any leading whitespace before block numbers (e.g. ` N10`) or keywords causes a syntax error.
4. **Strict Uppercase Only:** All keywords (`IF`, `GOTO`, `WHILE`, `DO`, `END`) and math functions (`SIN`, `COS`, `SQRT`, `ABS`, `ROUND`) must be strictly uppercase. Lowercase drops to DFA Rule 0 (Syntax Error).
5. **Hard Variable Ceiling at `#999` (Error 36):** User and system variables span `#0` through `#999`. Any reference to `#1000` or higher triggers parser Error 36 (`0x24`). Fanuc `#3000` alarm variables do not exist (`variables.md`).
6. **Dwell `G04 P` is in SECONDS:** Parameter `P` takes decimal fractions in seconds (`G04 P1.5` = 1.5 s, `G04 P0.2` = 200 ms). Writing milliseconds (`G04 P1000`) hangs execution for 16.6 minutes (`hazards.md`).
7. **Active WCS Selector is `#455`:** In G-code, `#455` reflects and selects the active coordinate system (`0`=G53, `1`=G54, `2`=G55, `3`=G56, `4`=G57, `5`=G58, `6`=G59). WCS offsets are stored at `#800`–`#803` (G54) through `#820`–`#823` (G59). Live machine coordinates are read from `#864`–`#866`.
8. **Subroutine Local Variable Isolation:** Local variables `#0`–`#49` are pass-by-value inside dynamically allocated stack frames (`malloc(0x320)`). When `M99` returns, the frame is freed, discarding local writes. Subroutines returning values must write to global user variables (`#50`–`#499`).
9. **Comments Cannot Nest:** Comments are delimited by `( text )`. The scanner regex is `\([^)]*\)`. Any closing paren `)` immediately terminates the comment; remaining text on the line is evaluated as live G-code.
10. **Hardware Probing Sequence:** Macro probing uses `M101` (probe arm), `G01` probe move, `M102` (probe disarm), followed by `G04 P0` to flush the motion planner queue before reading latched machine coordinates from `#864`–`#866`.
11. `vendor/` holds stock macros (`slib.nc`, `probe.nc`, `defprobe.nc`, `safez.nc`, `gotoz.nc`, `pause.nc`). If `vendor/macros/<file>` exists, check its version against `evidence.md`.

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
