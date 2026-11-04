# DDCS V3 / DM500 — hazards

> **Grounding:** Reverse-engineered from official Digital Dream firmware `install(2020-03-06-112)` binary `motion.out` (ARMv7-A 32-bit EABI) and factory macro library `slib.nc`.  
> **Applicable Hardware:** DDCS V1.1, V2.1, V3.1, V3.2, and DM500 handheld pendant controller.

---

## 1. ⛔ Hard Boundary at `#999` (Fanuc `#3000` User Alarm Crash)
- **The Defect:** In `yyparse()` Rule 51 (`0x00055c4c`) and Rule 52 (`0x00055d28`), any variable index $\ge 1000$ triggers **Error 36** (`0x24`).
- **The Trap:** Common Fanuc Macro B programming patterns like `#3000 = 1 (ALARM)` or `#1000`–`#1035` hardware signal variables **do not exist** on the DDCS V3.
- **The Hazard:** Running a program containing `#3000` or `#1000` immediately halts execution with an unrecoverable syntax/variable error.
- **The Fix:** Variable addressing must remain strictly within `#1`–`#999`.

---

## 2. ⛔ Dwell Time Units (Seconds vs Milliseconds Hang)
- **The Defect:** The interpreter evaluates `G04 P<val>` strictly in **seconds** (with floating-point precision).
- **The Trap:** On the DDCS Expert M350, `G04 P` is strictly in **milliseconds** (`G04 P1000` = 1.0 s). Programmers porting macros from the Expert or Fanuc often write `G04 P1000` expecting a 1-second pause.
- **The Hazard:** On the DDCS V3, `G04 P1000` pauses the machine for **1,000 seconds (16 minutes and 40 seconds)**! Spindle and axes freeze while the operator thinks the controller has crashed.
- **The Fix:** Always specify dwell in decimal seconds: `G04 P0.5` (500 ms) or `G04 P1.0` (1 s).

---

## 3. ⛔ Subroutine Local Variable Stack Frame Isolation (`#0`–`#49` Pass-by-Value)
- **The Defect:** At `0x0001ed94`, calling a subroutine (`M98` / `G65`) dynamically allocates an 800-byte frame (`malloc(0x320)`). Variables `#0`–`#49` reside exclusively inside this frame.
- **The Trap:** On `M99` (`0x0001f1c8`), the frame is freed (`free()`), restoring the caller's previous local variable values.
- **The Hazard:** A subprogram **cannot return calculation results to the caller via `#1`–`#49`**. Any values calculated in `#1`–`#49` disappear upon `M99`.
- **The Fix:** Any values intended to be returned from a subroutine to its caller MUST be stored in global user variables (`#50`–`#499`).

---

## 4. ⛔ Strict Uppercase Case Sensitivity
- **The Defect:** The Flex DFA scanner compiles regexes strictly for uppercase keywords (`IF`, `GOTO`, `WHILE`, `DO`, `END`, `SIN`, `COS`, `SQRT`...).
- **The Hazard:** Lowercase keywords like `if`, `goto`, `while`, `sin`, `cos` do not match any token rule and drop to DFA Rule 0 $\to$ **Syntax Error!**
- **The Fix:** All G-code keywords, control flow directives, and math functions must be strictly uppercase.

---

## 5. ⛔ Unsupported Keywords: `THEN`, `MOD`, `ROUND`, `AND`, `OR`
- **The Defect:** The BYACC parser grammar does not contain reduction rules for `THEN`, `MOD`, `ROUND`, `LN`, `EXP`, `AND`, `OR`, or `XOR`.
- **The Hazard:**
  - `IF [#1 GT 0] THEN #2=1` $\to$ **Syntax Error** on `THEN`. Use `IF [#1 LE 0] GOTO10; #2=1; N10`.
  - `#3 = #1 MOD #2` $\to$ **Syntax Error** on `MOD`. Use `#3 = #1 - FIX[#1 / #2] * #2`.
  - `#2 = ROUND[#1]` $\to$ **Syntax Error** on `ROUND`. Use `#2 = FIX[#1 + 0.5]`.
  - Multi-condition boolean logic `IF [#1 EQ 1 AND #2 EQ 2]` $\to$ **Syntax Error** on `AND`. Must be broken into sequential `IF` tests.

---

## 6. ⛔ Premature Comment Termination on Nested Parentheses
- **The Defect:** Flex start condition 1 (comment mode) exits on the first `)` character encountered.
- **The Hazard:** Writing `( Outer (Inner) Text )` causes the lexer to close comment mode on `Inner)`. The remaining characters `Text )` are scanned as active G-code, causing a syntax error or unexpected motion commands!
- **The Fix:** Never nest parentheses in comments. If nested clarification is needed, use brackets or semicolons: `( Outer [Inner] Text )` or `; Outer (Inner) Text`.

---

## 7. ⛔ Missing `G04 P0` Flush After Hardware Probing
- **The Defect:** Probing relies on high-speed hardware latching via `M101` and `M102`.
- **The Hazard:** If `G04 P0` is omitted after `M102`, reading machine coordinates `#864`–`#866` reads stale coordinates from before the probe triggered, because the motion planner queue has not finished decelerating and synchronizing with the CPU register mirror!
- **The Fix:** Always insert `G04 P0` immediately after `M102` before reading `#864`–`#866` or executing `G92`:
  ```gcode
  M101
  G91 G01 Z-100 F100
  M102
  G04 P0
  G90 G92 Z#2003
  ```

---

## 8. ⛔ Indentation / Leading Whitespace
- **The Defect:** The parser expects line labels `N<num>` and command blocks to begin at column 0.
- **The Hazard:** Indenting lines for readability (e.g. `  #100 = 1` or `  N10 G01 X10`) causes parsing failures or orphaned token syntax errors.
- **The Fix:** Write every line flush against column 0.
