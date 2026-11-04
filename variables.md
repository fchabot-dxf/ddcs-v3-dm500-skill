# DDCS V3 / DM500 — variables

> **Grounding:** Reverse-engineered from official Digital Dream firmware `install(2020-03-06-112)` binary `motion.out` (ARMv7-A 32-bit EABI) and factory macro library `slib.nc`.  
> **Applicable Hardware:** DDCS V1.1, V2.1, V3.1, V3.2, and DM500 handheld pendant controller.

---

## 1. Variable Partitioning & Enforced Boundaries

The DDCS V3 variable space is strictly partitioned. Unlike Fanuc Macro B (which supports variables up to `#99999`) or the DDCS Expert (which caps at `#2999`), the DDCS V3 enforces a **hard ceiling at `#999`**.

| Variable Range | Category | Persistence | Storage / Lifetime | Description |
|---|---|---|---|---|
| `#0` – `#49` | Local Variables | Volatile per call | 800-byte dynamic stack frame (`malloc(0x320)`) | Local variables. Automatically allocated upon `M98`/`G65` subprogram call and freed on `M99`. Not preserved across subroutine returns. |
| `#50` – `#499` | Global User Variables | Persistent | RAM array backed by `/mnt/nand1-1/uservar` | General-purpose macro variables. Safe for passing data between subroutines, macro math, and job parameters. Preserved across runs. |
| `#420` – `#499` | Runtime Motion & Macro Args | Volatile / Runtime | Internal interpreter control block | Active G-code state, WCS selector, mirror flags, and canned cycle arguments. |
| `#500` – `#599` | Motion Control Registers | Volatile / Runtime | Motion planner state | Safe Z (`#582`), pause retract (`#589`), return-to-zero feed (`#580`). |
| `#800` – `#835` | WCS Coordinate Table | Persistent | System coordinate store | Datums for G54 through G59 and G59.1. |
| `#864` – `#866` | Live Machine Coordinates | Real-time Read-Only | FPGA position latch | Absolute machine coordinates (X, Y, Z) latched after homing or probing. |
| `#870` | Touch Plate Thickness | Persistent | System parameter store | Floating tool setter block thickness. |
| `#2000` – `#2049`| System Parameter Aliases | Persistent | Direct mirror of `setting` (`Pr0`–`Pr49`) | Configuration parameters accessible via G-code (probe speeds, safe heights, pulser rates). |
| $\ge$ **`#1000`** | **ILLEGAL / OUT OF RANGE** | N/A | **Enforced in `yyparse()` Rule 51/52** | Any variable index $\ge 1000$ immediately throws **Error 36** (`0x24`). Fanuc user alarms (`#3000 = 1`) do not exist and abort execution. |

---

## 2. Runtime Coordination & Active WCS (`#455`)

In runtime G-code execution, coordinate system state is tracked via dedicated system registers:

| Register | Name | Range / Values | Description |
|---|---|---|---|
| **`#455`** | **Active WCS Selector** | `0`–`6` | **`0` = G53** (Machine coordinates)<br/>**`1` = G54**<br/>**`2` = G55**<br/>**`3` = G56**<br/>**`4` = G57**<br/>**`5` = G58**<br/>**`6` = G59** |
| `#450` | Distance Mode Flag | `< 0` / $\ge 0$ | `< 0` = `G91` (Incremental); $\ge 0$ = `G90` (Absolute) |
| `#449` | Canned Cycle Retract Plane | `< 0` / $\ge 0$ | `< 0` = `G98` (Retract to Initial Z level); $\ge 0$ = `G99` (Retract to R plane) |
| `#448` | Coordinate Scale Factor | Float | Scale multiplier applied to axis moves |
| `#420` | X Axis Mirror Multiplier | `1.0` or `-1.0` | Direction sign applied to X moves |
| `#421` | Y Axis Mirror Multiplier | `1.0` or `-1.0` | Direction sign applied to Y moves |
| `#422` | Z Axis Mirror Multiplier | `1.0` or `-1.0` | Direction sign applied to Z moves |
| `#423` | A Axis Mirror Multiplier | `1.0` or `-1.0` | Direction sign applied to A moves |

---

## 3. Position Feedback & WCS Offset Tables

### Real-Time Position Registers
| Register | Axis | Coordinate Frame | Description |
|---|---|---|---|
| `#451` | X | Workpiece (WCS) | Current tool position in active coordinate system |
| `#452` | Y | Workpiece (WCS) | Current tool position in active coordinate system |
| `#453` | Z | Workpiece (WCS) | Current tool position in active coordinate system |
| `#454` | A | Workpiece (WCS) | Current tool position in active coordinate system |
| `#864` | X | Machine (G53) | Latched absolute machine coordinate (updated after `M102`/`G04 P0`) |
| `#865` | Y | Machine (G53) | Latched absolute machine coordinate |
| `#866` | Z | Machine (G53) | Latched absolute machine coordinate |

### WCS Datum Storage Table
The base offset table stores the distance from machine zero to each coordinate system datum:

| WCS Frame | X Register | Y Register | Z Register | A Register |
|---|---|---|---|---|
| **G54** | `#800` | `#801` | `#802` | `#803` |
| **G55** | `#804` | `#805` | `#806` | `#807` |
| **G56** | `#808` | `#809` | `#810` | `#811` |
| **G57** | `#812` | `#813` | `#814` | `#815` |
| **G58** | `#816` | `#817` | `#818` | `#819` |
| **G59** | `#820` | `#821` | `#822` | `#823` |
| **G59.1** | `#824` | `#825` | `#826` | `#827` |

---

## 4. Built-in Cycle & Macro Argument Registers

When built-in cycles (`G81`, `G82`, `G83`, `G73`, `G12`, `G13`, `G102`, `G110`) are executed, the interpreter automatically populates these dedicated registers:

| Register | G-code Letter | Meaning |
|---|---|---|
| `#484` | `P` | Dwell duration (seconds) |
| `#485` | `Q` | Peck depth increment |
| `#488` | `X` | X axis target position or pocket length |
| `#489` | `Y` | Y axis target position or pocket width |
| `#490` | `Z` | Z axis hole bottom or pocket milling depth |
| `#491` | `A` | A axis target position or major axis rotation angle |
| `#492` | `B` | Initial angle in oblique ellipse cycles (`G102`/`G103`) |
| `#493` | `C` | Termination angle in oblique ellipse cycles |
| `#494` | `R` / `I` / `Q` | Pocket radius or peck retract clearance distance |
| `#496` | `K` | Cycle repeat count |
| `#497` | `R` / `D` | R-plane clearance height (drill cycles) or tool radius/diameter (milling) |
| `#498` | `L` | Step angle increment |

---

## 5. Machine & Motion Configuration Registers

| Register | Name | Typical Default | Description |
|---|---|---|---|
| `#516` | Homing Mode Selector | `0`–`7` | Zeroing reference strategy |
| `#571` | Probe Mode | `0` or `1` | `0` = Floating touch plate on part; `1` = Fixed tool setter on bed |
| `#572` | Fixed Probe X | mm | Initial X position of fixed tool setter |
| `#573` | Fixed Probe Y | mm | Initial Y position of fixed tool setter |
| `#574` | Fixed Probe Z | mm | Initial Z position of fixed tool setter |
| `#575` | Probe Retract Distance | mm | Z retract height after probe contact |
| `#578` | Probe Retract Feedrate | mm/min | Feedrate during post-probe retract |
| `#580` | Rapid Feedrate | mm/min | Feedrate used during `O100` return-to-zero moves |
| `#582` | Safe Z Height | mm | Machine or workpiece Z safe travel clearance height |
| `#589` | Pause Retract Distance | mm | Lift height applied to Z when Pause key is pressed |
| `#870` | Tool Plate Thickness | mm | Calibrated touch plate thickness |

---

## 6. System Configuration Parameter Variables (`#2000` – `#2049`)

In `defprobe.nc` and system configuration macros, `#2000`–`#2049` alias parameters `Pr0`–`Pr49` from the `setting` file:

| Register | Name in `defprobe.nc` | Purpose |
|---|---|---|
| `#2000` | Cutter Diameter | Tool diameter for corner/edge probe compensation |
| `#2001` | Tool Plate Thickness X | Touch plate edge offset for X probing |
| `#2002` | Tool Plate Thickness Y | Touch plate edge offset for Y probing |
| `#2003` | Tool Plate Thickness Z | Touch plate top surface thickness |
| `#2004` | X Axis Pre-Shift | Rapid shift distance along X before edge probe |
| `#2005` | Y Axis Pre-Shift | Rapid shift distance along Y before edge probe |
| `#2006` | Z Position Pre-Edge Probe | Z clearance depth for edge detection moves |
| `#2007` | X Axis Back Distance | Retract clearance after touching X edge |
| `#2008` | Y Axis Back Distance | Retract clearance after touching Y edge |
| `#2009` | Z Axis Back Distance | Retract clearance after touching Z plate |
| `#2010` | Tool Plate Center Offset | Distance from plate edge to true part corner |
| `#2011` | Probe Feedrate | Downward probing feedrate (typically 50–150 mm/min) |
| `#2049` | Fixed Probe Safe Z | Z axis safe travel height for fixed tool setter (added build 20260408) |
