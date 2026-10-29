# DDCS V3 / DM500 — variables

> Nothing in this file is bench-measured.

Source: **the DM500's own eng file (install dump 2026-04-08, ddcsv3.3)**, a variable table for the DDCS V3 / DM500. The descriptions are the table's own words.

> These are the DM500 setting/parameter numbers with their English labels, as the eng file numbers them; that they read as the same macro addresses is not confirmed.

⛔ Never write a variable you have not looked up here. A value of 0 does not mean unused.

## System

| variables | description | note |
|---|---|---|
| #1 | language setting — Eng; ç®ä½ä¸­æ; Ð ÑÑÑÐºÐ¸Ð¹; ç¹é«ä¸­æ; Custom |  |
| #2 | Display response during working |  |
| #3 | Function of tool path key — tool path; Try cutting; M8/M10 |  |
| #5 | minimum log radius of 4axis machining — unit |  |
| #6 | A axis rotate reference axis — X axis; Y axis; Z axis; not rotate |  |
| #15 | Speed Selection — 0:set by G code,1:use default speed \| G code; default |  |
| #16 | current coordinate system — 0:G53,1:G54,2:G55,3:G56,4:G57,5:G58,6:G59,7:MACH \| G53; G54; G55; G56; G57; G58; G59; MACH |  |
| #18 | Artboard width |  |
| #19 | Artboard height |  |
| #20 | Sketchpad thickness |  |
| #33 | Motor start speed — unit/min |  |
| #34 | X axis pulse equivalency — P/unit |  |
| #35 | Y axis pulse equivalency — P/unit |  |
| #36 | Z axis pulse equivalency — P/unit |  |
| #38 | A axis pulse equivalency |  |
| #39 | A axis pulse unit — pulse/deg; pulse/circle |  |
| #40 | AB axis Selection — A axis; B axis |  |
| #41 | X Axis max speed in M_Ctrl mode — unit/min |  |
| #42 | Y Axis max speed in M_Ctrl mode — unit/min |  |
| #43 | Z Axis max speed in M_Ctrl mode — unit/min |  |
| #44 | A Axis max speed in M_Ctrl mode — deg/min |  |
| #45 | X axis start Acc in M_Ctrl mode — unit/s2 |  |
| #46 | Y axis start Acc in M_Ctrl mode — unit/s2 |  |
| #47 | Z axis start Acc in M_Ctrl mode — unit/s2 |  |
| #48 | A axis start Acc in M_Ctrl mode — deg/s2 |  |
| #49 | HOME times |  |
| #50 | HOME signal Effective when HOME — Ignore HOME; Continue HOME |  |
| #51 | Enable limit signal when HOME — 0:Disable,1:Enable \| Disable; Enable |  |
| #52 | Enable X Home Searching function — 0:not enable,1:enable \| Disable; Enable |  |
| #53 | Enable Y Home Searching function — 0:not enable,1:enable \| Disable; Enable |  |
| #54 | Enable Z Home Searching function — 0:not enable,1:enable \| Disable; Enable |  |
| #55 | Enable A Home Searching function — 0:not enable,1:enable \| Disable; Enable |  |
| #56 | X axis Home speed — unit/min |  |
| #57 | Y axis Home speed — unit/min |  |
| #58 | Z axis Home speed — unit/min |  |
| #59 | A axis Home speed — deg/min |  |
| #60 | X axis Home signal Electric Level — Low; High |  |
| #61 | Y axis Home signal Electric Level — Low; High |  |
| #62 | Z axis Home signal Electric Level — Low; High |  |
| #63 | A axis Home signal Electric Level — Low; High |  |
| #64 | X axis Home direction — Negative; Positive |  |
| #65 | Y axis Home direction — Negative; Positive |  |
| #66 | Z axis Home direction — Negative; Positive |  |
| #67 | A axis Home direction — Negative; Positive |  |
| #68 | Tool Setting function mode — Disable; mode 1; mode 2; mode 3 |  |
| #69 | Thickness of tool sensor — unit |  |
| #70 | Probe signal electric level — Low; High |  |
| #71 | Initial tool's position — current position; fixed position |  |
| #72 | Initial probe position on X axis — unit |  |
| #73 | Initial probe position on Y axis — unit |  |
| #74 | Initial probe position on Z axis — unit |  |
| #75 | back distance after probe — unit |  |
| #76 | default operation speed — unit/min |  |
| #77 | maximum speed — unit/min |  |
| #78 | Z axis lifting protection speed — unit/min |  |
| #79 | Z axis dropping protection speed — unit/min |  |
| #80 | G0 Speed — unit/min |  |
| #82 | Z axis safe height — unit |  |
| #83 | X axis back distance after Home — unit |  |
| #84 | Y axis back distance after Home — unit |  |
| #85 | Z axis back distance after Home — unit |  |
| #86 | A axis back distance after Home — deg |  |
| #88 | enable of beep — Disable; Enable |  |
| #89 | Z axis back distance when pause — unit |  |
| #98 | maximum spindle speed — rpm |  |
| #99 | operation acceleration — unit/s2 |  |
| #100 | X axis manual control speed — unit/min |  |
| #101 | Y axis manual control speed — unit/min |  |
| #102 | Z axis manual control speed — unit/min |  |
| #103 | A axis manual control speed — deg/min |  |
| #104 | A axis optimal path when G0 run — No; Yes |  |
| #113 | Arc interpolation algorithm selection — Hard algorithm; Soft algorithm |  |
| #114 | Jog support in continuous mode — Mode 1; Disable; Mode 2 |  |
| #115 | unit selection — mm; in |  |
| #116 | X-axis zero signal source select — HOME signal; X++limit signal; X--limit signal |  |
| #117 | Y-axis zero signal source select — HOME signal; Y++limit signal; Y--limit signal |  |
| #118 | Z-axis zero signal source select — HOME signal; Z++limit signal; Z--limit signal |  |
| #119 | A-axis zero signal source select — HOME signal; A++limit signal; A--limit signal |  |
| #120 | U disk partition type — With partition; No partition |  |
| #124 | Circular centrifugal acceleration — unit/s2 |  |
| #125 | Circular speed adjustment factor |  |
| #126 | Home reminder after booting — No; Yes |  |
| #192 | Artboard zero point X position |  |
| #193 | Artboard zero point Y position |  |
| #194 | Artboard zero point Z position |  |
| #218 | Cycle Start key mode selection — Pause start; Restart |  |
| #219 | If Stop Spindle when pause — No; Yes |  |
| #220 | Spindle speed selection — 0:set by G code,1:default speed of spindle \| G code; default |  |
| #221 | default spindle speed — rpm |  |
| #222 | Spindle commands M3/M5 response — 0:not response,1:response \| No; Yes |  |
| #223 | response of M code(M8/M9,M10/M11) — 0:not response,1:response \| No; Yes |  |
| #224 | Spindle M3/M4/M5 response Duration — s |  |
| #225 | delay time of M8/M9 — s |  |
| #226 | delay time of M10/M11 — s |  |
| #227 | Spindle switch Active level — Low; High |  |
| #228 | M8/M9 active electric level — Low; High |  |
| #229 | M10/M11 active electric level — Low; High |  |
| #234 | X-axis DIR in A/B phase mode — +; - |  |
| #235 | Y-axis DIR in A/B phase mode — +; - |  |
| #236 | Z-axis DIR in A/B phase mode — +; - |  |
| #237 | A-axis DIR in A/B phase mode — +; - |  |
| #238 | IO input filter time width — ms |  |
| #250 | real track paint enable — disable; enable |  |
| #253 | track paint mode — Statue Mode; Line Mode |  |
| #263 | X axis stop Acc in M_Ctrl mode — unit/s2 |  |
| #264 | Y axis stop Acc in M_Ctrl mode — unit/s2 |  |
| #265 | Z axis stop Acc in M_Ctrl mode — unit/s2 |  |
| #266 | A axis stop Acc in M_Ctrl mode — deg/s2 |  |
| #267 | H00 tool offset — unit |  |
| #268 | H01 tool offset — unit |  |
| #269 | H02 tool offset — unit |  |
| #270 | H03 tool offset — unit |  |
| #271 | H04 tool offset — unit |  |
| #272 | H05 tool offset — unit |  |
| #273 | H06 tool offset — unit |  |
| #274 | H07 tool offset — unit |  |
| #275 | H08 tool offset — unit |  |
| #276 | H09 tool offset — unit |  |
| #277 | H10 tool offset — unit |  |
| #278 | H11 tool offset — unit |  |
| #279 | H12 tool offset — unit |  |
| #280 | H13 tool offset — unit |  |
| #281 | H14 tool offset — unit |  |
| #282 | H15 tool offset — unit |  |
| #283 | D00 tool offset — unit |  |
| #284 | D01 tool offset — unit |  |
| #285 | D02 tool offset — unit |  |
| #286 | D03 tool offset — unit |  |
| #287 | D04 tool offset — unit |  |
| #288 | D05 tool offset — unit |  |
| #289 | D06 tool offset — unit |  |
| #290 | D07 tool offset — unit |  |
| #291 | D08 tool offset — unit |  |
| #292 | D09 tool offset — unit |  |
| #293 | D10 tool offset — unit |  |
| #294 | D11 tool offset — unit |  |
| #295 | D12 tool offset — unit |  |
| #296 | D13 tool offset — unit |  |
| #297 | D14 tool offset — unit |  |
| #298 | D15 tool offset — unit |  |
| #374 | Enable software limit — 0:not enable,1:enable \| Disable; Enable |  |
| #375 | Soft-limited postion value of X-- — unit |  |
| #376 | Soft-limited postion value of Y-- — unit |  |
| #377 | Soft-limited postion value of Z-- — unit |  |
| #378 | Soft-limited postion value of A-- — deg |  |
| #379 | Soft-limited postion value of X++ — unit |  |
| #380 | Soft-limited postion value of Y++ — unit |  |
| #381 | Soft-limited postion value of Z++ — unit |  |
| #382 | Soft-limited postion value of A++ — deg |  |
| #390 | X axis DIR signal Electric Level — Low; High |  |
| #391 | Y axis DIR signal Electric Level — Low; High |  |
| #392 | Z axis DIR signal Electric Level — Low; High |  |
| #393 | A axis DIR signal Electric Level — Low; High |  |
| #400 | Limited siganl active of X-- — 0:not enable,1:enable \| Disable; Enable |  |
| #401 | Limited siganl active of Y-- — 0:not enable,1:enable \| Disable; Enable |  |
| #402 | Limited siganl active of Z-- — 0:not enable,1:enable \| Disable; Enable |  |
| #403 | Limited siganl active of A-- — 0:not enable,1:enable \| Disable; Enable |  |
| #404 | Limited siganl active of X++ — 0:not enable,1:enable \| Disable; Enable |  |
| #405 | Limited siganl active of Y++ — 0:not enable,1:enable \| Disable; Enable |  |
| #406 | Limited siganl active of Z++ — 0:not enable,1:enable \| Disable; Enable |  |
| #407 | Limited siganl active of A++ — 0:not enable,1:enable \| Disable; Enable |  |
| #408 | Effective electric level of X-- — Low; High |  |
| #409 | Effective electric level of Y-- — Low; High |  |
| #410 | Effective electric level of Z-- — Low; High |  |
| #411 | Effective electric level of A-- — Low; High |  |
| #412 | Effective electric level of X++ — Low; High |  |
| #413 | Effective electric level of Y++ — Low; High |  |
| #414 | Effective electric level of Z++ — Low; High |  |
| #415 | Effective electric level of A++ — Low; High |  |
| #416 | Time interval between DIR & pulse — ns |  |
| #418 | X axis Pulse signal Electric Level — Low; High |  |
| #419 | Y axis Pulse signal Electric Level — Low; High |  |
| #420 | Z axis Pulse signal Electric Level — Low; High |  |
| #421 | A axis Pulse signal Electric Level — Low; High |  |
| #422 | definition of PWM Electric level — Low; High |  |
| #423 | Enable signal of extended ESTOP — 0:not enable,1:enable \| Disable; Enable |  |
| #424 | Electric level of ext-ESTOP signal — Low; High |  |
| #425 | Enable extended START&PAUSE — 0:not enable,1:enable \| Disable; Enable |  |
| #426 | Electric level of Ext-key1 — Low; High |  |
| #427 | Electric level of Ext-key2 — Low; High |  |
| #428 | Enable the ESTOP signal on MPG — 0:not enable,1:enable \| Disable; Enable |  |
| #429 | Electric level of ESTOP on MPG — Low; High |  |
| #430 | MPG interface type — 0-serial port MPGï¼1-standard MPG \| Serial; Standard |  |
| #431 | MPG Precision |  |
| #432 | Standard MPG signal Electric Level — Low; High |  |
| #433 | PWM rising rate from 0V to 10V |  |
| #434 | A/B phase signal level on MPG — Low; High |  |
| #435 | X axis protection speed — unit/min |  |
| #436 | Y axis protection speed — unit/min |  |
| #437 | enable of X axis backlash — 0:not enable,1:enable \| Disable; Enable |  |
| #438 | enable of Y axis backlash — 0:not enable,1:enable \| Disable; Enable |  |
| #439 | enable of Z axis backlash — 0:not enable,1:enable \| Disable; Enable |  |
| #440 | enable of A axis backlash — 0:not enable,1:enable \| Disable; Enable |  |
| #441 | X axis backlash distance — unit |  |
| #442 | Y axis backlash distance — unit |  |
| #443 | Z axis backlash distance — unit |  |
| #444 | A axis backlash distance — deg |  |
| #446 | Define the Function of Ext-key1 — START; Find Middle; extkey1.nc; JOG-D; Disable; S/P ctrl |  |
| #447 | Define the Function of Ext-key2 — PAUSE; ZERO; extkey2.nc; S/P ctrl; Disable |  |
| #448 | MPG control mode — Open; Close |  |
| #449 | ZERO |  |
| #450 | GOTOZ |  |
| #451 | HOME |  |
| #452 | PROBE |  |
| #453 | AUTO |  |
| #454 | SIMULATE |  |
| #455 | CONT |  |
| #456 | STEP |  |
| #457 | MPG |  |
| #458 | RESET |  |
| #459 | READY |  |
| #460 | BUSY |  |
| #461 | FRO: |  |
| #462 | SRO: |  |
| #463 | SJR: |  |
| #464 | Start Line: |  |
| #465 | 2ND |  |
| #466 | syntax check |  |
| #467 | Main Page |  |
| #468 | File Page |  |
| #469 | Param Page |  |
| #470 | Normal Mod |  |
| #471 | X-:gotoz Y-:zero Z-:home |  |
| #472 | A-:probe Start:goto break |  |
| #473 | X-:all gotoz X+:x gotoz |  |
| #474 | Y+:y gotoz Z+:z gotoz |  |
| #475 | A-:go probe   X+:x 1/2 |  |
| #476 | X+:left X-:right Y+:up Y-:down |  |
| #477 | Z-:enter Z+:cancel |  |
| #478 | Y-:all zero X+:x zero |  |
| #479 | Y+:y zero Z+:z zero |  |
| #480 | Z-:all home X+:x home |  |
| #481 | Y+:y home Z+:z home |  |
| #486 | Y+:y 1/2       Z+:mode 3 |  |
| #487 | Start:Specifies line |  |
| #488 | Pause:Search line by position |  |
| #489 | Advanced |  |
| #495 | interpolation period — s |  |
| #499 | user access key |  |
| #2000 | Cutter diameter — unit |  |
| #2001 | Tool plate thick for X — unit |  |
| #2002 | Tool plate thick for Y — unit |  |
| #2003 | Tool plate thick for Z — unit |  |
| #2004 | shift of X axis before probed — unit |  |
| #2005 | shift of Y axis before probed — unit |  |
| #2006 | Z position before X(Y)-axis probed — unit |  |
| #2007 | Back distance when the tool touches the X-axis edge — unit |  |
| #2008 | Back distance when the tool touches the Y-axis edge — unit |  |
| #2009 | Back distance when the tool touches the Z-axis edge — unit |  |
| #2010 | center of tool plate — unit |  |
| #2011 | Probe feedrate — unit/min |  |
| #2020 | Jog-1 moving distance — unit |  |
| #2021 | Jog-2 moving distance — unit |  |
| #2022 | Jog-3 moving distance — unit |  |
| #2023 | Jog-4 moving distance — unit |  |
| #2024 | X axis Home offset — unit |  |
| #2025 | Y axis Home offset — unit |  |
| #2026 | Z axis Home offset — unit |  |
| #2027 | A axis Home offset — deg |  |
| #2028 | the % of the change in value per X Pulses of the Handwheel |  |
| #2029 | color reversal — No; Yes |  |
| #2030 | Soft-limited stop mode — deceleration; emergency |  |
| #2031 | Uniaxial acc is limited by M_Ctrl start acc — No; Yes |  |
| #2032 | Soft-limited pre-judgment coefficient of X |  |
| #2033 | Soft-limited pre-judgment coefficient of Y |  |
| #2034 | Soft-limited pre-judgment coefficient of Z |  |
| #2035 | Soft-limited pre-judgment coefficient of A |  |
| #2036 | Automatically adjust the artboard — Yes; No |  |
| #2037 | Is FRO valid for G0? — No; Yes |  |
| #2038 | Enable software limit alarm in the idle state — Yes; No |  |
| #2039 | Specified line scan increment |  |
| #2041 | G53/G153 function interchange — No; Yes |  |
| #2043 | subroutine programming mode — Disable; Enable |  |
| #2044 | The initial state of manual mode is MPG — No; Yes |  |
| #2045 | M4 prot definition — No; M10 |  |
| #2046 | extended function key filter coefficient |  |
| #2047 | IO output M command execution strategy — software proc.; FPGA |  |
| #2048 | Home sequence — ZXYA; SYNC; ZYXA; YXZA; XYZA |  |
| #2049 | Z axis safe height of probe — unit |  |
