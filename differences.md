# DDCS V3 / DM500 — differences

> **Grounding:** Reverse-engineered from official Digital Dream firmware `install(2020-03-06-112)` binary `motion.out` (ARMv7-A 32-bit EABI) and factory macro library `slib.nc`.  
> **Applicable Hardware:** DDCS V1.1, V2.1, V3.1, V3.2, and DM500 handheld pendant controller.

---

## Architectural Comparison Table

| Feature / Trait | DDCS V3 / DM500 | DDCS Expert (M350) | DDCS V4.1 | Fanuc Macro B |
|---|---|---|---|---|
| **Maximum Variable Number** | **`#999`** (Error 36 for $\ge 1000$) | **`#2999`** (Error 9 for $\ge 3000$) | **`#499`** user variables | **`#99999`** |
| **User Alarm Register** | ⛔ None (`#3000` is illegal) | `#1505 = 3 (msg)` (Modal prompt) / `#1503` (Strip) | `#1505` (Modal prompt) | `#3000 = 1 (ALARM MSG)` |
| **Subroutine Nesting Depth** | **Unlimited** (dynamic heap `malloc(0x320)`) | 8 levels | 8 levels | 4 levels |
| **Local Variables** | `#0`–`#49` (pass-by-value stack frame) | `#0`–`#49` (pass-by-value frame `malloc(0x4e0)`) | `#0`–`#49` (pass-by-value) | `#1`–`#33` |
| **Global User Variables** | `#50`–`#499` | `#50`–`#499` | `#50`–`#499` | `#100`–`#199`, `#500`–`#999` |
| **Active WCS Register** | **`#455`** (`0`=G53, `1`=G54... `6`=G59) | **`#578`** (`1`=G54... `6`=G59) | `#16` (setting) / `#500` | `#4014` (modal group 14) |
| **Live Machine Coords** | `#864` (X), `#865` (Y), `#866` (Z) | `#790` (X), `#791` (Y), `#792` (Z) | `#864` (X), `#865` (Y), `#866` (Z) | `#5021`–`#5026` |
| **WCS Offset Tables** | `#800`–`#803` (G54)... `#820`–`#823` (G59) | `#805`–`#808` (G54)... `#830`–`#833` (G59) | Internal `coord1` file | `#5221`–`#5226` (G54)... |
| **Dwell Parameter Units** | **SECONDS** (`G04 P1.5` = 1.5 s) | **MILLISECONDS** (`G04 P1500` = 1.5 s) | **MILLISECONDS** (`G04 P1500`) | Seconds or Milliseconds |
| **Probing Instruction** | `M101` $\to$ `G01` $\to$ `M102` $\to$ `G04 P0` | `G31` probe move | `G31` probe move | `G31` probe move |
| **Datum Setting Method** | `G90 G92 <ax><val>` | Direct write to `#805`+ or G10 | `G92` or G10 | `G10 L2 P<n>` or `#5221`= |
| **Supported Math Functions** | `SIN`, `COS`, `TAN`, `ATAN`, `SQRT`, `ABS`, `FIX`, `FUP` | `SIN`, `COS`, `TAN`, `ATAN`, `SQRT`, `ABS`, `ROUND`, `FIX`, `FUP` | `SIN`, `COS`, `TAN`, `ATAN`, `SQRT`, `ABS`, `ROUND`, `FIX`, `FUP` | Full set + `LN`, `EXP` |
| **`ROUND` Function** | ⛔ Not in grammar (Syntax Error) | ✅ Supported | ✅ Supported | ✅ Supported |
| **`MOD` Operator** | ⛔ Not in grammar (Syntax Error) | ⛔ Not in grammar (Syntax Error) | ⛔ Not in grammar | ✅ Supported |
| **`IF ... THEN` Syntax** | ⛔ Not in grammar (Syntax Error) | ⛔ Not in grammar (Syntax Error) | ⛔ Not in grammar | ✅ Supported |
| **Relational Operators** | Words (`EQ`, `NE`...) AND Symbols (`==`, `!=`...) | Words (`EQ`, `NE`...) AND Symbols (`==`, `!=`...) | Words AND Symbols | Words only (`EQ`, `NE`...) |
| **Trajectory Recording** | `WRRECODE`, `RDRECODE`, `RecordCoords` | `RecordData`, `ExportRtcpData` | None | None |
| **Communication / Network** | None (USB flash drive only; no Ethernet) | Ethernet (SMB share, HTTP, Modbus RTU / Slave P279) | Ethernet (FTP, HTTP) | RS-232, Ethernet |

---

## Porting Gotchas: Moving Macros Between DDCS Controllers

1. **Dwell Units Mismatch (Expert $\to$ V3):**
   - Expert: `G04 P2000` pauses for 2 seconds.
   - V3: `G04 P2000` pauses for **2000 seconds (33 minutes)**! Must divide by 1000: `G04 P2.0`.

2. **Active WCS Inspection:**
   - Expert: `IF #578 EQ 1 GOTO10` checks for G54.
   - V3: `IF #455 EQ 1 GOTO10` checks for G54 (`#578` on V3 is probe retract feedrate!).

3. **User Alarms and Dialogs:**
   - Expert: `#1505 = 3 (PRESS ENTER)` opens a modal prompt.
   - V3: Variable `#1505` is completely out of range ($> 999$). The V3 has no programmatic G-code modal prompt mechanism.

4. **Coordinate Setting:**
   - Expert: Programs directly update `#805`–`#808` to set G54 offsets without moving axes.
   - V3: Setting coordinate zero in macros is performed via `G90 G92 <axis><val>`.
