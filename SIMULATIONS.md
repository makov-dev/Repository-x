# OPENINGS — Simulation Notes (draft)

Headless bot sims for `index.html` (rig: `/tmp/sim.js`, output: `/tmp/sim_out.txt`).
Bots play the real game loop through the debug handle: real rooms, real enemies,
real economy math. No game code was changed for this run — numbers first, tuning later.

## 1) Combat balance — 800 fights per cell (win / flee / death, avg rounds)

| build | tier 1 | tier 2 | tier 3 |
|---|---|---|---|
| fresh (str3, knife, rags) | 95 / 5 / 0 % (r2.8) | 6 / 94 / 0 % (r3.6) | 20 / 80 / 0 % (r3.1) |
| geared (str6, shard, mail) | 100 / 0 / 0 % (r1.0) | 87 / 13 / 0 % (r2.9) | 14 / 86 / 0 % (r4.6) |
| veteran (str10, saw, plate) | 100 / 0 / 0 % (r0.4) | 100 / 0 / 0 % (r1.4) | 52 / 48 / 0 % (r3.7) |

Findings:
- **Flee never fails** (0 deaths in 7,200 fights). The 5th option is a free exit:
  nothing in Vharn can kill you if you are willing to run. All lethal risk lives
  *outside* combat (obstacles, hunger, bleed).
- Tier 2 is where gear matters (6% → 87%). Tier 3 gates hard: even veterans only win ~half.

## 2) Delve sessions — 400 dives per Opening (fresh str3 adventurer)

| Opening | death | walk-out | timeout | depth p50 | net loot/dive (mean/p50/p90) | hp lost p50 |
|---|---|---|---|---|---|---|
| Cessgate (T1) | 47.3% | 51.8% | 1.0% | 2 | 55g / 47g / 169g | 18 |
| Ossuary (T2) | 56.0% | 43.8% | 0.3% | 1 | 10g / −10g / 77g | 19 |
| Cathedral (T3) | 58.0% | 41.5% | 0.5% | 1 | 19g / 0g / 88g | 19 |

Findings:
- **Every dive is a coin flip** (~50% death) at *any* tier, even for a careful
  pathfinding bot that leaves at 40% HP. T1 is not a safe starter zone, because
  `tierNow = opening.tier + depth − 1` pushes depth-2 Cessgate to tier-2 enemies.
- T2/T3 pays *worse* per unit risk than T1 for a fresh character (higher tier
  kills you before the richer loot accumulates).
- HP cost per dive (~18), not gold, is the limiting resource.

## 3) Courtship race — 400 runs per row; can she wed someone else first?

Tempos: visit every X weeks. Talk +4 affection/visit; rose (+18) cuts time 4×.

| tempo | race lost (talk) | race lost (rose) | median weeks to win (talk / rose) |
|---|---|---|---|
| 4w | 22% | 5% | 52w / 12w |
| 8w | 43% | 8% | 104w / 24w |
| 16w | 68% | 22% | 144w / 48w |
| 32w | 90% | 43% | 128w / 96w |

Originally the game exempted your active `p.partner` from the marry-off hazard,
making courtship 100% safe. **Race now applied** (tuning item 3 accepted): while
you dawdle above ground, a courting partner can wed a roper, the ferryman, or
the one holding her debt-notes — the news reaches you late, the way news does.
The rose (60g) buys 4× the speed; tempo is nearly everything.

## 4) Dynasties — 20 runs × 5 generations, real delves

| variant | heir takeovers | lines died out | died before marrying | gens reached |
|---|---|---|---|---|
| as coded (fixed 3 NPCs) | 10 | 20/20 | 13/20 | 10% g2, 0% g3+ |
| + fresh faces each gen (proposed) | 35 | 16/20 | 16/20 | up to g5 |

Findings:
- **The founder funnel is the bottleneck**: estate (house 250 + ring 60 + nursery 260
  = 570g) needs ~12–15 median T1 dives, each ~50% lethal. A surviving founder takes
  ~71 dives; 65–80% of founders die single at age 17–33 and the line ends pre-marriage.
- Once a line IS started, the generational loop works and is stable: heirs take over
  at 15y with inherited house/gear and sustain ~71 dives/generation, dying delving
  at ~31–33y.
- With the fixed NPC pool, heirs almost always find **nobody left to marry** —
  after 15 domestic years at 0.5%/week marry-off, every other NPC is wed or dead.
  Generational turnover (new young faces drifting into Vharn) fixes succession.

## Candidate tuning → applied & still open

### Applied since first run

3. ✅ **Courtship stakes** — the `p.partner` exemption was dropped; the race is
   real (see updated section 3).
4. ✅ **Fresh faces** — arrivals are wired into `simulateNPCs`: roughly once a
   year a new 18–25y NPC drifts into Vharn (fixed arrival name pool, realia
   descs: debt, flight, the hunted, the unexplained), announced by news, roster
   capped at 12. Dynasty experiment re-run against the real code (20 lines):

   | metric | before (fixed pool) | with arrivals wired in |
   |---|---|---|
   | heir takeovers | 10 | 37 |
   | lines with NO spouse available when needed | 7/20 | **0/20** |
   | generations reached | g1:10 g2:10, never g3 | up to g5 (g1:8 g2:3 g3:2 g4:1 g5:6) |

   Lines still die out more often than they flourish — the founder funnel
   (section 2: 570g of estate through ~50%-lethal dives) remains the bottleneck,
   not the marriage market.
6. ✅ **NPC realia pass** — the courtable pool grew from 3 generic figures to 6
   people with reasons to be in Vharn: guild debt (Mira), a discharge from the
   bread riot (Osk), an inherited debt settled in bodies (Yeva), a cutpurse
   hunted by the Kindly Fellows (Corsvine), plague-ash exile (Liss), and a widow
   whose husband's door has been a grave for eleven months (Hesta Wren).
   Poverty, strife, existentialism and futility are the local weather — each
   tells her own lines when you talk. Arrivals share that weather (toll-house
   fires, burden-lash sentences taken for another's theft, boats that do not
   come back).

### Still open (NOT applied — decisions wanted)

1. **Flee should be fallible** (e.g. 65–80% success, agi-weighted) so combat
   has teeth.
2. **Depth-tier curve**: `tierNow = tier` on depth 1–2, +1 each 2 depths after,
   so T1 is a real starter zone and dynasty founders survive to marry
   (~30–50% of lines should survive, not ~20%).
7. Optional: household income (cellar salvage) is 4–11g/week — a liveable
   floor rates.
