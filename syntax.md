# DDCS V3 / DM500 — syntax

> Nothing in this file is bench-measured.


> ⚠ **Nothing in this pack has been measured on a DDCS V3 / DM500.** Its rules are declared forms, not bench tests. Treat every rule as unverified on the machine.

## Program structure

End a program with: `M30`

Write every line from column 0 (no indentation): a label with a leading space is a syntax error on the DDCS family.

Distance mode: `G90` absolute, `G91` incremental.

Dwell 1.5 s: `G04 P1.5` (P is in s).

Move in machine coordinates (Z to the value in #190):
```gcode
G53 Z#190
```

## Jumps and loops

Conditional jump:
```gcode
IF #190LT#191 GOTO5
GOTO6
N5
N6
```

A clamp ("if #190 > 3 then #190 = 3") — never an inline IF .. THEN on this controller:
```gcode
IF #190LE3 GOTO4
#190=3
N4
```

Expression functions (declared; the measured ones are in the Math section): FIX: unattested; FUP: unattested.

## Comments

- `( text )` — one comment, never nested: a `(` or `)` inside the text ends or breaks it. Keep both out of comment text.
