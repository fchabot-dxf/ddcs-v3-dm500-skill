# DDCS V3 / DM500 — syntax

> **Grounding:** Reverse-engineered from official Digital Dream firmware `install(2020-03-06-112)` binary `motion.out` (ARMv7-A 32-bit EABI, Flex DFA 208 states, BYACC 1.9 LALR(1) 90 rules) and factory library `slib.nc`.  
> **Source:** `http://www.ddcnc.com/?m=home&c=View&a=index&aid=135&lang=en`  
> **Applicable Hardware:** DDCS V1.1, V2.1, V3.1, V3.2, and DM500 handheld pendant controller.

---

## Program Structure & Formatting Rules

- **End of program:** End every program with `M30`.
- **Column 0 Alignment:** Write every G-code block, keyword, and line label starting at column 0 (no leading whitespace). Indented block numbers (e.g. ` N10`) trigger a syntax error.
- **Line Ending:** Standard `\r\n` (CRLF) or `\n` (LF) line endings.
- **Distance mode:** `G90` (absolute) or `G91` (incremental). Mode flag is stored in `#450` (`< 0`: G91; $\ge 0$: G90).
- **Dwell:** `G04 P<sec>`. Parameter `P` is in **SECONDS** with decimal fractions (e.g. `G04 P1.5` = 1.5 seconds, `G04 P0.2` = 200 ms).
  > ⚠️ **HAZARD:** Dwell is NOT in milliseconds. Writing `G04 P1000` causes a 1000-second (16.6-minute) freeze!
- **Coordinate Move:** Bare `G00`/`G01` moves are in the active WCS (`G54`–`G59`). Move in machine coordinates via macro `G153 X... Y... Z... A...` or return-to-zero macro `O100`/`O103`.

---

## Case Sensitivity & Keyword Atomicity

- ⛔ **STRICT UPPERCASE ONLY:** The Flex scanner DFA matches keywords solely in uppercase:
  - `IF`, `GOTO`, `WHILE`, `DO`, `END`
  - Lowercase `if`, `goto`, `while`, `do`, `end` reduce to Rule 0 $\to$ **Immediate Syntax Error!**
- **Keyword Atomicity:** Keywords cannot contain whitespace:
  - `GOTO10` or `GOTO 10` is valid. `GO TO 10` is a syntax error.
  - `WHILE#10>0DO1` or `WHILE [#10 > 0] DO1` is valid.
- ⛔ **NO `THEN` KEYWORD:** Fanuc-style `IF [...] THEN ...` is not recognized. `THEN` causes an immediate syntax error. Always use `IF [...] GOTO <label>`.

---

## Control Flow: Jumps and Loops

### Conditional Jump (`IF ... GOTO`)
Conditional jump to a line block label:
```gcode
IF #100 LT #101 GOTO5
GOTO6
N5 (Executed when #100 < #101)
N6 (Continuation)
```

Both word relational operators and symbolic operators are recognized:
```gcode
IF #100 < #101 GOTO5      (Symbolic operator)
IF #100 LT #101 GOTO5     (Fanuc word operator)
IF [#100 <= 0] GOTO10     (Bracketed condition)
```

### Unconditional Jump (`GOTO`)
```gcode
GOTO100
N100
```

### Loop (`WHILE ... DO ... END`)
Loops are natively supported by the BYACC parser (Rule 49 `WHILE`, Rule 50 `DO`, Rule 51 `END`). Loop identification number `1`, `2`, `3`... must match between `DO` and `END`:
```gcode
#100 = 0
WHILE #100 < 10 DO1
  (Body of loop)
  #100 = #100 + 1
END1
```
Nested loops are supported up to arbitrary depth with matching numbers (`DO1`..`END1`, `DO2`..`END2`).

---

## Relational Operators

DDCS V3 supports both Fanuc Macro B two-letter word comparisons and C-style symbols:

| Operator | Word | Symbol | Example |
|---|---|---|---|
| Equal to | `EQ` | `==` | `IF #1 EQ 0 GOTO5` or `IF #1 == 0 GOTO5` |
| Not equal to | `NE` | `!=` | `IF #1 NE 0 GOTO5` or `IF #1 != 0 GOTO5` |
| Greater than | `GT` | `>` | `IF #1 GT 10 GOTO5` or `IF #1 > 10 GOTO5` |
| Less than | `LT` | `<` | `IF #1 LT 10 GOTO5` or `IF #1 < 10 GOTO5` |
| Greater or equal | `GE` | `>=` | `IF #1 GE 5 GOTO5` or `IF #1 >= 5 GOTO5` |
| Less or equal | `LE` | `<=` | `IF #1 LE 5 GOTO5` or `IF #1 <= 5 GOTO5` |

---

## Mathematical Expressions & Functions

### Arithmetic Operators
Algebraic precedence is enforced:
1. Brackets `[...]`
2. Functions (`SIN`, `COS`, `SQRT`...)
3. Multiplication `*` and Division `/`
4. Addition `+` and Subtraction `-`

```gcode
#100 = [#101 + #102 * 2.5] / 3.0
```

### Supported Functions `[CONFIRMED-BY-CODE]`
All functions require square brackets enclosing their operand and must be in uppercase:

| Function | Syntax | Description |
|---|---|---|
| `SIN` | `#1 = SIN[#2]` | Sine (argument in degrees) |
| `COS` | `#1 = COS[#2]` | Cosine (argument in degrees) |
| `TAN` | `#1 = TAN[#2]` | Tangent (argument in degrees) |
| `ATAN` | `#1 = ATAN[#2]/[#3]` | Arc tangent (2-argument supported via Rule 73) |
| `SQRT` | `#1 = SQRT[#2]` | Square root (operand must be $\ge 0$) |
| `ABS` | `#1 = ABS[#2]` | Absolute value (clears IEEE-754 sign bit) |
| `FUP` | `#1 = FUP[#2]` | Ceiling: rounds upward to the nearest integer |
| `FIX` | `#1 = FIX[#2]` | Truncate / Floor: discards fractional part |

⛔ **UNSUPPORTED / ILLEGAL FUNCTIONS:**
- `ROUND` $\to$ Syntax error! Use `FIX[#1 + 0.5]` instead.
- `MOD` $\to$ Syntax error! Calculate modulo via `#rem = #val - FIX[#val / #base] * #base`.
- `LN`, `EXP` $\to$ Not supported.
- `AND`, `OR`, `XOR` $\to$ Logical / bitwise expression operators are NOT in the grammar. Multi-condition tests must be expressed as sequential jumps.

---

## Subprogram Calling & Macro Nesting (`M98`, `M99`, `G65`)

- **Subprogram Call:** `M98 P<prog>` or `G65 P<prog>`
- **Subprogram Return:** `M99`
- **Subroutine Definition:** Begins with `O<number>` (e.g. `O100`, `O9081`) and terminates with `M99`.
- **Unlimited Nesting Depth:** Firmware allocates an 800-byte frame (`malloc(0x320)`) dynamically for every call, enabling unbounded nesting depth.
- ⚠️ **Pass-By-Value Local Variable Isolation:**
  - Variables `#0`–`#49` are local to the subroutine frame.
  - When `M99` executes, the frame is freed, discarding all writes to `#0`–`#49`.
  - **Subprograms cannot return data to the caller via `#1`–`#49`. Return values MUST be placed in global user variables (`#50`–`#499`).**

---

## Hardware Special Macro Directives

DDCS V3 features unique built-in commands for trajectory capture, surface probing, and coordinate math:

| Command | Grammar Rule | Purpose |
|---|---|---|
| `WRRECODE[#1]` | Rule 36 | Writes current axis coordinates into recorded trajectory slot `#1`. |
| `RDRECODE[#1]` | Rule 37 | Reads recorded trajectory point `#1` into `#104` (X), `#105` (Y), `#106` (Z), `#107` (A). |
| `RecordCoords` | Rule 38 | Records current multi-axis touch position for probing. |
| `ClearCoords` | Rule 39 | Clears recorded coordinate buffer. |
| `GetCenterPos` | Rule 40 | Solves circle or rectangular center position from recorded touch points. |

---

## Hardware Probing Architecture

Probing on the DDCS V3 does not use `G31`. It uses high-speed hardware latch M-codes:
```gcode
G04 P0              (Flush queue)
M5                  (Spindle stop)
M101                (Enable hardware probe trigger latch on digital input)
G91 G01 Z-100 F100  (Probe descent move)
M102                (Disable hardware probe latch)
G04 P0              (Flush motion queue: latches trigger position into #864-#866)
G90 G92 Z#2003      (Set workpiece coordinate via touch plate thickness #2003)
G91 G00 Z5.0        (Retract)
```

---

## Comments

- **Parenthetical Comments:** `( text )`. Terminated by the first `)`.
  > ⚠️ **HAZARD:** Do not nest parentheses `( Outer (Inner) Text )`. The lexer exits comment mode on the first `)`, causing `Inner) Text )` to be executed as G-code!
- **Line Comments:** `; text` comments out everything until the end of the line.
