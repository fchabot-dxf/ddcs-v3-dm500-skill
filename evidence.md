# DDCS V3 / DM500 — evidence

> **Source Material:** Official firmware binary `motion.out` (build `Ver:2020-03-06-112`) from vendor release archive `install(2020-03-06-112).zip` (`http://www.ddcnc.com/?m=home&c=View&a=index&aid=135&lang=en`), and factory macros `slib.nc`, `probe.nc`, `defprobe.nc`.

---

## Reverse Engineering Evidence Table

| Fact / Rule | Evidence Source | Address / Offset | Verification Detail |
|---|---|---|---|
| **Binary Identity & Architecture** | `motion.out` ELF Header | File offset `0x00`–`0x40` | ELF 32-bit LSB ARM EABI5, entry `0x000080d0`. Sections: `.text` (`0x80d0`), `.rodata` (`0xd2920`), `.data` (`0xe51c0`). |
| **Shared V1/V2/V3/DM500 Codebase** | `motion.out` `.rodata` | `0x000d55dc`–`0x000d5620` | Signatures: `DDCSV1.1@DDREAM`, `DDCSV2.1@DOSEDONE`, `DDCSV3.1@DDREAM`, `DDCSV3.2@DDREAM`, `Ver:2020-03-06-112`. |
| **Flex Scanner Engine** | `motion.out` `.text` / `.rodata` | `0x000330a8`–`0x000367f8` | `yylex()` implementation. `yy_ec` (71 classes) at `0xd3770`, `yy_accept` at `0xd35d0`, `yy_base` at `0xd3c8c`, `yy_def` at `0xd3e3c`, `yy_nxt` at `0xd3fec`, `yy_chk` at `0xd4444`. Jump table at `0x33414` (91 rules). |
| **BYACC Parser Engine** | `motion.out` `.text` / `.data` | `0x00051d0c`–`0x00057dd8` | `yyparse()` implementation. `yylhs` at `0xe53c8`, `yylen` at `0xe547c` (90 reduction rules). `yydefred` at `0xe552c` (220 states). Reduction switch at `0x52240` with 85 cases. |
| **Unlimited Nesting Stack Allocator** | `motion.out` `.text` | `0x0001ed94`–`0x0001ee68` | Calls `malloc(0x320)` (800 bytes). Zeroes frame with `memset(0x320)`. Links into doubly-linked list (`0x173f98` stack top). Increments nesting depth counter at `0x173fa4`. |
| **Stack Frame Destruction on M99** | `motion.out` `.text` | `0x0001f1c8`–`0x0001f298` | Decrements counter at `0x173fa4`, unlinks current frame, frees 800-byte block via `free()`. Restores caller local variables. |
| **Hard Variable Limit at `#999`** | `motion.out` `.text` | `0x00055c4c`, `0x00055d28` | BYACC reduction rules 51 and 52: `cmp r2, #999` (literal `0x3e7` at `0x54df4`). Throws Error 36 (`0x24`) via `0x5a888` if index $\ge 1000$. |
| **Local Variable Scope `#0`–`#49`** | `motion.out` `.text` | `0x00055dbc` | Checks `cmp r3, #0x31` (49). Variables $\le 49$ routed to current subroutine heap frame. |
| **Active WCS Register `#455`** | `slib.nc` (O9028, O9153) | Lines 220–235, 275–290 | Switch block: `IF#455EQ0GOTO3` (G53 `#[800]`), `IF#455EQ1GOTO4` (G54 `#[800]+#[804]`), `IF#455EQ2GOTO5` (G55 `#[800]+#[808]`). |
| **WCS Offset Table Addresses** | `slib.nc` (O9028, O9153) | Lines 225–250 | Base register `#800` (G54 X), `#804` (G55 X), `#808` (G56 X), `#812` (G57 X), `#816` (G58 X), `#820` (G59 X), `#824` (G59.1 X). |
| **Live Machine Coordinates `#864`–`#866`** | `probe.nc`, `slib.nc` (O103) | `probe.nc` lines 4–6, `O103` line 2 | `#20=#864`, `#21=#865`, `#22=#866` read current tool machine coordinates. |
| **Uppercase-Only Keyword Enforcement** | Flex DFA Simulation | BFS search of DFA graph | `IF` matches Rule 48 (state 99); lowercase `if` drops to Rule 0 (state 207). `GOTO` matches Rule 47 (state 156); lowercase `goto` drops to Rule 0. |
| **Unsupported Functions (`MOD`, `ROUND`)** | Flex DFA Simulation | BFS search of DFA graph | `MOD`, `ROUND`, `THEN`, `AND`, `OR`, `XOR`, `LN`, `EXP` all drop to Rule 0. |
| **Supported Math Functions** | Flex DFA Simulation | Rules 28–35 | `SIN` (28), `COS` (29), `TAN` (30), `SQRT` (31), `ATAN` (32), `ABS` (33), `FUP` (34), `FIX` (35). `ABS` implementation at `0x56c1c` uses `bic r2, r3, #0x80000000`. |
| **Trajectory Recording Directives** | Flex DFA Simulation | Rules 36–40 | `WRRECODE` (36), `RDRECODE` (37), `RecordCoords` (38), `ClearCoords` (39), `GetCenterPos` (40). Tested in `slib.nc` macro `O102`. |
| **Hardware Probe Latching** | `probe.nc`, `defprobe.nc` | Lines 16–20 | `M101` enables hardware latch, `G91 G01 Z-100 F100` probes, `M102` disables latch, `G04 P0` flushes position to `#864`–`#866`. |
| **Dwell Parameter Seconds** | `defprobe.nc`, `probe.nc` | `G04 P0` | Parameter `P` evaluated as floating-point seconds across all motion blocks. |
