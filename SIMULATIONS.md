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
  nothing in Jericho can kill you if you are willing to run. All lethal risk lives
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
  Generational turnover (new young faces drifting into Jericho) fixes succession.

## Candidate tuning → applied & still open

### Applied since first run

3. ✅ **Courtship stakes** — the `p.partner` exemption was dropped; the race is
   real (see updated section 3).
4. ✅ **Fresh faces** — arrivals are wired into `simulateNPCs`: roughly once a
   year a new 18–25y NPC drifts into Jericho (fixed arrival name pool, realia
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
   people with reasons to be in Jericho: guild debt (Mira), a discharge from the
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

## 12) Two ways to live — persona runs (rig: `/tmp/personas.js`, 50 runs each)

Bots play the *real* game loop (real rooms, real stairs, real shop ladder,
real 55% yard drills, real dice with skill-based "caught sixes" emulated:
chaser .8, mender .4). No game code was changed for this run.

**The Door-Chasers** (story chasing players: train, gear, descend, chase truths):

| metric | result |
|---|---|
| died in the dark | 41/50 |
| reached the Gate | 0/50 |
| deepest depth | median 6, max 9 (ninth depth witnessed) |
| lore fragments | median 2, max 6/9 |
| great horrors | 25/50 killed ≥1, 4/50 killed all three, 50/50 died to one |
| average death age | ~18 (nobody retires from this) |
| marriage | 13/50 — always instrumental ("Needed, not wanted: the Gate counts children") |

Findings: the chase is nearly a suicide pact — the five Gate conditions
outlive 18-year-old bodies, which is exactly the intended cosmology for the
endgame's rarity. The graveyard curve: most chasers die at depth 3–9 to the
very horrors they study; killing all three apexes and still being alive is a
roughly 1-in-12 talent event. The ninth depth is reachable with a Bone Saw,
Yes-Plate and STR 8; without the training arc it is a death sentence.

**The Roof-Menders** (sidequest players: cautious dives, court, build, tithe):

| metric | result |
|---|---|
| died | 13/50 (the rest ended their errand list, no beggar's exit) |
| deepest depth | median 0 — they never go below one |
| married | 25/50 — the race bites: suitors (ropers, ferrymen, debt-holders) outrun half of them |
| houses owned | 29/50  upgrades median 5  (leak → door → hearth → cellar) |
| Bones Game wins | median 3 |
| born children | 22 in 50 lives |

Findings: the sidequest life is survivable *if you buy iron first* (13 died vs
41 chasers) but wealth still comes only from the openings — the couple that
stays home stays poor. The courtship race produced genuine near-runs:
three roses, then "Mira married a smuggler off the Tar Docks" while the
player was under the city.

**Most interesting Door-Chaser runs (from the rig's own chronicles):**

- **#10** (age 18, d9, 6 truths, 2/3 horrors): Host killed at 17, yards her way
  to STR 8 on a door-chaser's arithmetic, kills the Wound-Warden at 18 with 3hp
  left, marries Mira "for the child the Gate counts", reads gild/notheaven/
  keeper/mother at the ninth depth — then dies at 18 to a second
  Hundred-Handed Host. Six truths and no successor.
- **#43** (age 22, d9, 6 truths, 2/3): the diligent version — eleven weeks at
  the yard (six bought nothing), Kill Host at 17y9, walks out with 1hp twice,
  marries Sergeant at 18y7, kills Wound-Warden at 18, Sixtongue by title,
  needs one more funeral and a grown child: died at 22, two documents short.
- **#5** (age 17, d9, 6 truths, 2/3): the prodigy — all four depth-fragments at
  17y3, Host killed at 17y3, Sixtongue at 17y8 after killing the Wound-Warden,
  dead at 17y9 to The Long Mother, one season after Sixtongue. The city
  never heard the fragment it won from her.

**Most interesting Roof-Mender runs:**

- **#43** (age 26): buys a house with winnings from a Bones Game, courts Mira
  with roses across four years while the city weds everyone else around her
  (Yeva to her debt-holder, Liss to a tallow merchant, Hesta to Brother Ansel);
  the run ends mid-courthouse, still un-married, the roses falling behind her body.
- **#14** (age 26): the good one — bought the house, met Mira at the Lamp,
  rose after rose ("weeks are the clock"), won the Bones Game twice,
  cellar upgrade by 20y10, and the bellhymn fragment at the thirteenth candle.
  The exception the city allows.

## 13. Re-run after the inheritance round (build with pregnancy heirs,
kingdom rumors, work shifts and facility keepers)

Same rig, same 50/50 split, new build - a full-loop regression pass and a
check that the new systems didn't distort the economy the personas play:

- Door-Chasers: died 42/50 (was 41/50), reached the Gate 0/50, deepest depth
  median 7 max 9, truths median 3 max 7, great horrors >=1 in 31 runs,
  all-three 6, heirs continued 3/50.
- Roof-Menders: died 8/50 (was 13/50), married 28/50, housed 39/50, upgrades
  median 5, bones wins median 3, born children 28 - mildly kinder spread,
  consistent with dice weather rather than drift.
- Conclusion: the new layers ride alongside; the Gate remains unreached in
  100 lives, and work shifts do not yet appear in persona playbooks (they
  are below the survival bar the rig is tuned for - as designed).

## 14. Re-run after the rooms ladder (census walkers, loan sharks, stamina
floor -25, per-floor landlords, house map, room costs 500 + 500 each)

Run: the `personas` rig, 50 lives each, split across five workers with seeded
stress (fixed per-index RNG state), against the `daf2f29` engine plus two
correctness fixes the runs themselves flushed out (see below).

### Rig changes this pass

- the delve loop now runs until the character is back in base or dead; it
  resolves fights and event menus inside the loop instead of abandoning them,
  and stays null-safe when a z-press leaves the dungeon mid-iteration;
- revised survival policy: camp below 75% HP, retreat below 35% HP or
  stamina -14, never descend below 50% HP, flee below 30%; die-catching runs
  both dice with persona skill (z for the first, x for the second);
- the poor recover now: at-home sleep (bed), the pantry shelf, and hot meals
  (6g) for the houseless;
- cheap first-dive gear allowed (the Door-Chaser buys a blade at 22g), and a
  render-skip in the harness so 50 lives fit in wall-clock.

### Engine bugs the sims caught (fixed in `index.html` this round)

1. `winCombat` was re-entrant. With a full pack, the loot drop menu defers the
   win callback while the fight still reads as a live player turn, so every
   next strike re-awarded the kill: kills into the tens of thousands and gold
   into the billions inside nine dives. Now a kill settles once.
2. `clog()` crashed when the deferred pack-full callback resolved after the
   combat had already been torn down. It now just exits.
3. Cosmetic: death reports read "Torn apart in the The Weeping Cessgate";
   the doubled article is gone.

### Results (50 lives each)

| metric | Door-Chasers | Roof-Menders |
|---|---|---|
| died in the dark | 20/50 | 39/50 |
| survived to the 40-year cap | 30/50 | 11/50 |
| became Gatekeeper / ever gate-ready | 0/50 | 0/50 |
| great horrors killed (>=1 / all three) | 38 / 18 | 4 / 0 |
| deepest depth (median / max) | 2 / 4 | 2 / 3 |
| lore truths (median / max) | 3 / 5 | 1 / 3 |
| kingdom rumors (median) | 7/25 | 4/25 |
| dives (median) | ~1,100 | 140 |
| kills (median) | 36 | 75 |
| married / child | 0/50 | 0/50 |
| housed / rooms built | 0/50 | 0/50 |
| death age of the dead (median) | 17 | 18 |
| purse (end / peak, median g) | 5 / 282 | 87 / 352 |

Death causes across complete lives: ~74% are stamina exhaustion ("your body
quit in the dark/water/deep"), ~21% combat, the rest finished by the room's
own traps - the poverty spiral is the leading killer now, not the horrors.

### What the run says

- The dive loop itself is healthy: crash-free 100 lives, no runaway economy,
  apex horrors fall to good dice (18 chasers killed all three).
- **The 500g house is now a marriage gate.** `proposeCourt` requires `house`,
  and no persona life in 100 ever reached a peak purse of 500 (medians: 282g
  chasers, 352g menders). Marriage 0/100, children 0/100, rooms 0/100 - so
  the heir line and every house-anchored story is closed behind one price
  point. This is the sharpest tuning signal of the round.
- Exhaustion dominates death. Sleep is house-gated, and the house is
  unaffordable, so the cautious Roof-Menders (who plan to sleep) die at 18
  in the field: the *intent* of the room ladder - a safe rest - is priced out
  of the lives that want it most.
- Depth courage vs. survival is a hand you set: with a cautious descent
  policy (this run) the chasers settle at depth 2-4; the older, braver
  policy in section 13 reached 6-9 and died for it 41/50. There is no
  observed policy in the current economy that both stays alive and reaches
  the ninth depth - which keeps the Gate as an event, not a routine, but the
  house-gate above is what locks the arc, not the depth.
  Probe (8 lives each, small sample): loosening the descend-gate to 35% HP
  gave depth median 3 max 5 with 4/8 dead; an eager 25% gate gave median 2
  max 3 with 4/8 dead - the menders died out entirely both times. Neither
  regained the old 6-9 depths: descent courage alone no longer buys depth,
  the retreat/do-not-descend supply rules spend it instead. The knob stays
  in the rig (`GATE_C`) as a dial worth pulling again after any economy fix.
- Loan sharks never fire for these personas (they never carry coin into town
  after a delve payday), and courtship "bites" never register because nobody
  holds a partner long enough to wed - the race is won by the deadline of
  death rather than by other suitors, in this sample.
## 15. Two hundred lives, four ways to live — smart-player audit (this round)

Run: the `personas` rig on the post-`8aae4a2` build **plus** the flesh-setter's
`Traveller's Bloodrest` (35g, full HP, drinkable mid-fight) — 50 lives each of
four personas = 200 lives, one process per life (the sandbox OOM-kills wider
parallelism; with heap caps and 3 lanes every life completes clean):

| persona | style |
|---|---|
| Door-Chaser (skill .8) | dives at 22g gear, no house, shallow courage |
| Roof-Mender (skill .4) | cautious, wants house+family, works shifts |
| Deep-Sage (skill .85) | trains, buys the house, marries, pays funerals |
| Depth-Pilgrim (skill .85) | 9 rations + 3 Bloodrests, depthCap 12, wants the deep |

### Ladder audit (new this round, `/tmp/ladder.js` pattern)

A competent wanderer who paths straight to `>` each floor was walked down the
Cathedral twelve times: **each floor costs ~3–6 stamina** (occasionally ~25 when
a wounded fight drags), full descent to depth 12 fits inside one stamina bar
plus a handful of rations (~40–90 stam total, 1–2 rations of HP). Descent is
*not* stamina-prohibitive — the sim personas' 260-step-per-dive cap was what
kept re-launching them at floor 1, so their medians (below) understate what a
map-reading human reaches trivially.

### What 200 lives proved illogical (and the engine fixes this round)

1. **Depth paid almost nothing.** Loot value capped at ×1.22 (tier factor only);
   kill-drops below depth 5 even got a secret ×1.5 the floor lists never saw.
   Result: every persona farmed depth 1–3 forever (sage: 1,178 dives, deepest 3).
   **Fix:** `rollLoot` now scales value by true floor depth (+15%/floor below
   the first — ~2.2× at depth 9, ~2.6× combined with tier), so *the deep is the
   money*, and the kill-drop's ad-hoc bonus rides the same honest rule.
2. **"Great horrors of the deep" didn't live in the deep.** The three gate
   apexes (Hundred-Handed Host, Wound-Warden, Long Mother) *were* the tier-3
   pool, so the Cathedral fought the Long Mother on floor 1 — 108 of the first
   200 lives killed all three while never passing depth 5, making the gate
   condition "3 great horrors of the deep" a shallow formality. **Fix:** the
   apexes now live only below the fourth depth (weight rises 8%/floor); tier 3
   instead fields three new beast-tier critters (Palanquin of Gristle, The
   Tithe-Slug, Host of the Bell Throat) with their own bestiary entries.
3. **Deeper floors held no more loot than shallow ones.** **Fix:** one extra
   loot pile per three floors of depth (depth 9≈10 piles).

### Lives on the fixed engine (200 fresh, same rig)

- 0 rig crashes in all 200 lives; fuzz still 0 errors (42000 frames).
- **Apex placement now behaves**: only Depth-Pilgrims — the ones that actually
  push past depth 4–5 — meet apexes (25/50 met ≥1, and every
  pilgrim who met one met all three — meaning every one of them crossed below
  the fifth), and the chasers/sages/menders meet none at their depths: the deep
  keeps its own monsters now.
- Door-Chaser/Roof-Mender economics unchanged at shallow depth (0.95×–1.25×
  loot): still die ~17–18 of exhaustion between the house they can't reach and
  the work they can't skip — that is the poverty spiral, working as designed.
- Sage line (the "smart player" archetype): 45/50 survive the 40-year cap, 30
  married, 49 housed, funerals paid, apex-horror lore now comes from *going
  deep* with the truths instead of one Cathedral floor — truths median 2 max 3
  for sages (down from 4), because the apex truths left the shallows with the
  apexes. That is the intended cost of "lore hidden, kill them to learn them".
- Purse peaks rose where depth was reached (pilgrim peak median 914g vs 825g).
- Still no Gatekeeper in any sim life — with descent ladder cost audited above,
  the remaining blocker is the persona rigs' own courage, not the engine; a
  human pathing floor-to-floor has every condition reachable.

### Suggested human play pattern this unlock invites
Drill Strength/Agility to hold the tier-3 pool, buy 8–10 rations + a Bloodrest,
take the Cathedral, path to `>` deliberately (map first), camp between floors,
and only loot the floor you intend to leave. The deep pays for the house, the
nursery, the funerals — and below the fourth depth, the horrors are waiting.

## 16. Three thousand lives, eight ways to live (this round)

Run: the `personas` rig extended to **8 archetypes**, **exactly 3,000 completed
lives** (one process per life, flock'd queue, 5-7 workers; 48 OOM-prone lives
re-run serially at 2.5 GB) - **0 rig crashes**. Baseline build `38a5ab7`
(depth-pays economy, apex horrors gated below the fourth depth).

### The archetypes

| persona | n | who they are |
|---|---|---|
| Door-Chaser | 700 | fights most things at 80% skill, no house, drills STR |
| Roof-Mender | 600 | cautious (40%), saves toward a house, upgrades it |
| Clan-Builder | 600 | 60% skill, house at 560, builds rooms, nurseries a family line |
| Six-Chaser (gambler) | 400 | 25% skill - pure dice addiction, courts the Ossuary tier |
| Ledger-Keeper | 300 | ultra-cautious thresholds (out at -6 stam), devout, works hard |
| Scar-Penitent | 250 | willpower first, tier-1 openings only, ever penitent |
| Deep-Sage | 75 | the "smart player": strongest stat plan, descends with gear |
| Depth-Pilgrim | 75 | 9 rations, 3 Bloodrests, depth 12 or nothing |

### Results (all 3,000)

| persona (n) | dead | capped40 | deepest med/max | wed | child | housed | death age med |
|---|---|---|---|---|---|---|---|
| CHASER (700) | 579 | 121 | 2/4 | 0 | 0 | 7 | 17 |
| MENDER (600) | 523 | 77 | 2/4 | 0 (39 bites) | 0 | 1 | 18 |
| CLAN (600) | 508 | 92 | 3/4 | 0 (50 bites) | 0 | 29 | 18 |
| GAMBLER (400) | 257 | 143 | 2/4 | 0 | 0 | 0 | 17 |
| LEDGER (300) | 114 | 186 | 2/4 | 0 (26 bites) | 0 | 10 | 18 (dives med **1176**) |
| PENITENT (250) | 235 | 15 | 2/4 | 0 | 0 | 0 | 21 (kills med 338, gold peak med 768) |
| SAGE (75) | 46 | 23 | 3/5 | 22 | 6 | 43 | 18 |
| PILGRIM (75) | 74 | 1 | 2/4 | 0 | 0 | 0 | 18 |

Truths median 1-2 everywhere (max 3); apex horrors met by almost no one
(chaser 2, sage 1, clan 4, rest 0); **zero gate-ready lives in 3,000**;
loans taken 0/3,000 (personas pay before they buy - the shark feeds on the
reckless, and the rigs are not reckless); funerals ~0 everywhere.

### What 3,000 lives said, and what was done

1. **The year-one wall.** ~80% of all lives die at age 17-18 (the first year
   of play), dominated by exhaustion - sleep was a 520g purchase, and a
   beginner is not rich. **Fix: the Salt Bunks.** A new city building on the
   carver-to-pawn stretch, keeper Deck-hand Sal ("No names asked at the Salt
   Bunks. That is the amenity."): **chair by the stove, 5g, +40 stamina**;
   **salt-crusted bunk, 12g, stamina full and +6 HP**. Broke and buying? The
   Kindly Fellows find you there too.

   **Before/after on the doss build** (150 fresh poor-archetype lives, same
   seeds' rig; "first-year" = died at 17-18):

   | persona | baseline first-year (of dead) | doss build | death age med |
   |---|---|---|---|
   | Scar-Penitent | 20% | **0%** | 21 -> **25** |
   | Roof-Mender | 92% | **67%** | 18 -> 18 |
   | Door-Chaser | 97% | 92% | 17 -> 18 |

   The Penitent (who paces himself) and the Mender (who saves) now survive
   their first winter; the Chaser still dies young because he *chooses* to -
   he dives until his body quits at the -25 floor. That is the archetype, not
   a bug: caution is now survivable, recklessness still isn't.

2. **Truth starvation explained - the deep arc is real.** A new **Pathfinder**
   archetype (descend-greedy: shortest road to `>`, descend whenever hp/stam
   allow, 9 rations, 3 Bloodrests) ran 50 lives on the depth-pays build:

   - deepest **median 7, max 13** (past the ladder audit's ceiling);
   - truths **median 3, max 5** (vs 1-2 for every shallow archetype);
   - apex horrors met in **26/50 lives**, one life killed all three;
   - 9 lives survived to the 40-year cap, most at depth 8-12.

   So the Gate's conditions sit exactly where the game says they do: the
   church misread its fragments, and the truths are *down there*. Door-chasers
   never see one. **Humanly reachable, deliberately expensive** - no engine
   change made; the remaining 0/3,250 gatekeepers are rig courage, not a wall.

3. **Left alone on purpose (flagged for the player):**
   - **Weddings 0 / children 6 in 3,000** - the courtship race bites hard
     (partners wed others mid-delve ~50 bites) and the nursery gate stands.
     The clan/sage lines are the only ones that ever had a family. Raising
     base courtship odds would make the Gate's child condition trivial;
     awaiting direction.
   - **Funerals ~0** - personas only pay when *unburied* accrues; the 5-grave
     gate condition wants a deliberate mourner archetype. A human role-plays
     this; a rig does not. Awaiting direction.
   - **Loans 0/3,000** - working as designed: the shark feeds on the broke
     and sudden, and these personas buy everything cash.
   - **Pilgrim dives-stall** - the pilgrim explores exhaustively before
     descending; only the Pathfinder's beeline shows depth 12+. Exploration
     costs steps, steps cost stamina; true on purpose.

Verification this round: dosstest 17/17 (placement/building exclusivity, bunk
and chair arithmetic, shark interception for the broke, Sal's greeting), full
suite on the patched engine - deeptest 12/0, bosstest 52/0, roundtest 24/0,
exhausttest 31/0, housetest 72/0, sharktest 22/0, fuzz 30 sessions 0 errors -
plus 150 verification lives and 50 Pathfinder lives, 0 rig crashes.

## 17. Levers and second tries — traps stop evaporating (this round)

A player report, of the good kind: *"so traps such as levers you can actually
go back and bypass the trap."* Audit of `obstacleEvent` confirmed the engine
disagreed with itself: **every** choice — including "Back off", and including
a **failed** roll — set `ev.done=true`, and events only fire once. So a trap
could be disarmed by retreating and stepping on it again (the silent vanish),
and a botched crossing bought the crossing anyway, paid in blood that didn't
matter next time. Single-use traps, one roll per trap, no return trips.

**Fix — traps are persistent until genuinely crossed:**

- **Retreat no longer disarms.** "Back off" / "Leave it" leave the obstacle
  armed; step off, step back on, and the same trap wakes again.
- **Failure no longer disarms.** A botched Agility/Will/Wits roll leaves you
  bleeding *and* leaves the trap live: go back, get gear or courage, face it
  again. The trap is a toll you pay until the passage is actually opened.
- **New: the lever hunt.** Spikes and plates are mechanisms, and mechanisms
  keep maintenance: a new **"Hunt for the release lever (Wits)"** option.
  Success (Wits 9) disarms that crossing **permanently** — the sabotage lever
  thrown by "some earlier debtor of the dark"; the teeth sink and stay sunk,
  the plate becomes furniture, and re-crossing it later is silent. Failure
  costs a dart (plate) or just wasted light (spikes), and the trap stays live.
- **Unchanged permanent opens**: clean stat roll past rubble/spikes/gas/plate,
  spent Rope & Hook, spent Antitoxin.
- **Renderer**: a defused mechanism stops drawing its glyph (sunk teeth, plain
  stone); the chasm keeps its black tile — a hole is terrain, not a gadget.
- No soft-lock is possible: chokepoint traps can always be crossed by eating
  the failure toll (you are on the tile after the roll resolves), so retreat
  is tactics, never a wall.

Verification (fresh rigs; /tmp was wiped since last round, so traptest and
fuzz were rebuilt): **traptest 26/26** — back-off re-arms spikes/plate/gas/
rubble, failed rolls leave them armed and drawing blood again on re-entry,
lever hunt defuses spikes and plates permanently with silent re-crossings,
antitoxin spends gas, strength clears rubble, renderer clean; **fuzz 20
sessions / 40,000 frames / 0 errors** (random keys across two delves with
GEAR at hand). Syntax check passed, inject verified by re-extraction.

## 18. The reckoning — endings for keepers (this round)

Player request: *endings for players that become gatekeepers at the end,
depending on whether any of their stats has reached 100* — the full mtier:
nothing / overwhelmed / mercy / mutation / combinations, with exactly ONE true
ending.

### The growth engine that makes 100 reachable

An audit showed training has no hidden cap (`train`/`homeTrain` grow +1 per
success, unbounded; church-grief and whimper mercy WLL still respect the
mortal cap of 10). Probe result: **Strength 9 -> 100 in 171 yard drills**
(~6.6 game-years), Will 9 -> 100 inside 3,000 drills. So the keeper's long
peace *can* grow all four measures — and now there is a dedicated, faster,
zero-gold path: **"Keep the watch"** (new, at the Gate of Openings, keepers
only): 13 weeks and 45 stamina buys one measure +1, guaranteed. The moment a
measure crosses 100, a banner tells you the Gate has noticed.

### The reckoning: "Descend — let the Gate look at what you have become"

New keeper option at the Gate of Openings. The ending is composed from the
keeper's state:

| state at the reckoning | outcome |
|---|---|
| **all four at 100+** | **THE GATE IS CLOSED.** You are not overwhelmed, you do not go mad, you resist the call and close the Gate. The city goes silent; the horrid creatures thin and die out within the year; you remain as an immortal hermit, having paid what humans interpret as soul/being/existence — "there is no human word for it." **The only time the game truly ends** (run ends to title). The cosmology persists in localStorage (`openings_cosmos.closed`) and every later life begins in a quieter city — the Gate's intro says so. |
| **Wits 100 + Will 100** | **The Sentinel.** Mutated, but a person. Earned title; the name persists (`openings_cosmos.sentinels`, last 3). In every later life a figure stands at (49,17) by the Gate of Openings — stone-silver glow, does not age, wander, or die — and **a new player can talk to them**: the warning ("I will not want that for you") and, once per life, **a truth given freely**. |
| **Wits 100 alone** | Understood the alien logic; **mutates and submits** — joins the keepers of the gate, a feature of the doorway. Heir continues; the submitted keeper joins the prior-keeper line (their bossized form clamps each stat at 12 so the dream loop stays a fight, not arithmetic). |
| **Strength 100 alone** | Body keeps its shape; eyes/nails mutate — a **recognizable mutant** ("the Marked"). Keeps playing, keeps the city, keeps the watch. |
| **Agility 100 alone** | Outline changes into something else — no footprints, lagged reflection, arrived shadow ("the Unfooted"). Keeps playing. |
| **no measure at 100** | **Nothing.** Overwhelmed, cannot comprehend, body fails the forces at hand — mutation into **one of the creatures of the forgotten city** (named from the real horror pools; no article stutter). The heir wakes; the child's inheritance line says what the city has not. |

- Heirs of grown keepers start **human-sized** (stat halves now clamp at 12).
- Endings that pass the lamp use the same ledger the death-screen uses
  (deadRows pages; the closed run's book is titled "THE GATE IS CLOSED").

### Verification (regress rig, rebuilt after the second /tmp wipe)

**regress 23/23**: trap persistence re-verified (back-off keeps armed, re-entry
re-fires, lever defuses spikes and plates permanently with silent
re-crossings); the full ending matrix (all six outcomes, states legacy/base/
dead as designed); the Sentinel's cross-save existence, fixed post at (49,17),
dialogue, once-given word, one granted truth; heir clamp (100-stat parent ->
child at stats <= 12); cosmology persistence into fresh worlds; fuzz 10
sessions x 1,500 frames, 0 errors. reach100 probe: growth unbounded as
intended. Syntax check passed; inject verified by re-extraction.

## 19 — Controls round: hold-to-walk and finger steering

**Complaint:** controls disliked. Request: arrows/WASD as the main scheme on
computer; on phone, move in the direction where the finger is held.

**Audit of the old scheme.** Keyboard arrows/WASD were tap-per-step only
(dirTap consumed a fresh pressed edge; OS key-repeat was swallowed), so
walking a corridor meant drumming a key. Touch had no steering at all:
tap-a-tile BFS auto-walk only. Gamepad already held-walked (%8 frame repeat)
while keyboard didn't.

**Changes (patch `controls`, 8 asserted anchors, all hit):**

1. tickHeld() in the frame loop: for held arrows/WASD, first press fires
   immediately (unchanged) and the key re-fires every 7 frames while held —
   hold-to-walk everywhere movement is read (base, dungeon, home, cellar).
   Menus consume the same cadence, so held arrows scroll option lists; the
   wrap was verified live (5-option pack menu returns to sel 0 after exactly
   5 held advances). OS autorepeats are ignored (e.repeat doesn't reset the
   cadence); keyup zeroes the counter so each press starts clean.
2. Touch steering: pointerdown anywhere on a walkable scene (base / dungeon /
   home / cellar, no open UI, map closed) sets a steer anchor; a pointermove
   listener tracks the finger in canvas units; steerHold() returns the
   dominant-axis direction from the hero's screen position to the finger,
   beyond a 10-unit dead zone, on a 6-frame cadence — and dirTap consults
   it. Because the camera keeps the hero centered, "hold right of him"
   means keep walking east; drag over him and he stops (finger-on-hero is
   the dead zone). A gold ring + thread is drawn at the finger while
   steering (and only then). pointerup/pointercancel end steering; steering
   auto-cancels any tap-to-walk in progress.
3. Canvas CSS touch-action: none so drags can't scroll the page on phones;
   the title screen now says "arrows or WASD move, Z confirms / on touch:
   hold a finger - you walk toward it".
4. Tap-to-walk, right-click cancel, chips, dice taps, and gamepad are
   untouched; a Z/X press or any movement key still cancels an auto-walk.

**Verification (rig `conctl`): 20/20** — held arrows and each of WASD walk
repeatedly (6 steps in 40 held frames; cadence exact), a single tap still
moves exactly one step with no drift, held ArrowDown scrolls a menu (sel 3
after 20 frames; wraps to 0 after 5 advances), steerHold cadence is exactly
fire-then-five-quiet-then-fire ("1000001000001"), finger-on-hero is a dead
zone (12 frames, zero steps), steering walks east/west/north from held-finger
positions, steering works inside a dungeon (steps taken until an encounter
interrupts), brief poke steps at most once, and fuzz (3 sessions x 1500
frames with random key down/up, presses, steering set/clear, teleports)
runs with 0 errors. Previous suites re-run on the patched engine:
regress 23/23, endtest 37/37, reach100 probe unchanged (STR 9->100 in 169
drills).

## 20 — Touch-activation round: doors and interactables answer the tap, not just the Z

"You need to also have doors and interactable things to activate on touch."
Audit of every tappable thing after the steering round found most taps already
worked on arrival (base buildings walk to the door and enter; home furniture
walk and interact; cellar barrels and stairs; the stranger walks to you or
meets you close-up; dungeon loot/puzzle/corpse/shrine/bait/whimper/secret fire
on the step). Four real gaps remained, and one latent engine bug fell out of
fixing them:

- **Dungeon stairs answered only the Z key.** Tapping `<`/`>` walked you
  there and then made you find the key. A `useStair()` was extracted from
  `updateDungeon` (same walk for both inputs) and the tap handler now attaches
  it as the walk's callback: tap the stairs and the hero walks there and
  climbs/descends — from depth 3 a tapped `<` rises a level, at the first
  floor a tapped `<` climbs out, a tapped `>` still weighs the Gate first.
- **Latent bug: the dungeon walk-loop picked up a walk's completion callback
  and never called it.** `const cb=walkCb; walkCb=null;` and then silence —
  harmless before, because nothing tapped in a dungeon set a callback. Now
  the callback fires when the path empties (bypassed while a dialog is open).
- **Townfolk talked from across town.** Tapping a Jericho face anywhere in a
  crowd opened their chat from wherever you stood — teleport-gossip. Tap now
  behaves like the stranger does: within two tiles you talk in place, beyond
  that the hero paths to them and the conversation waits until he arrives.
- **Quick taps fought the new steering.** Pressing far away started a walk
  *and* read as a steering hold a few frames later, aborting the journey
  partway. A ~200 ms grace tick is stamped on every touch (pointerdown);
  `steerHold` refuses to steer until it expires, so a tap is decided as a tap
  and a hold as a steering hold — hold east and he still keeps going east.
- **Bonus find: bleeding building tap-regions.** Every building hit-rect was
  clamped with `Math.max(0, screenX)` while keeping its full width, so the
  visible street beside any building poking off the left/top screen edge
  belonged to the building — taps meant for the open lane "entered" the
  neighbour. Rects are now registered at their true (possibly negative) screen
  position; the pointer test is containment, so nothing else changes.

Verification (rigs rebuilt after the wipe): 16/16 — fresh-game arrows/WASD
steps, hold repeats (2+ tiles/18 f), steering moves ≥2 tiles in 9 frames,
quick-tap journeys run to completion (arrival within 3 of the requested
tile), tapped `>` descends from depth 1→2, Z-on-`>` still descends, tapped
`<` ascends, tapped townfolk walks over and talks (dialog mode opens),
3×1500-frame fuzz 0 crashes.

## 21 — Kingdom round: a day over the city, lantern light in the deep

"Let's go more like Kingdom." The reference is the side-scrolling Kingdom
(Noio-Licorice lineage) — near-monochrome pixel art that lives by its
**ambience**: a real time-of-day tint, pools of warm light fighting a cold
blue-black night, and the edges of the frame falling dark. The game's drawing
is itself; the delivery is a post-process grading layer between the world
draw and the HUD, `drawAmbience()` each frame:

- **The day passes over Jericho.** Ninety seconds of play per day, a keyframed
  tint curve cross-fading multiply-tint layers: pale washed noon (alpha .14)
  → warm amber dusk (alpha .30) → cold blue-black night
  (rgb 84,104,158 @ .58) → grey-pink dawn, and out. Night is treated as the
  honest resting state of this city; noon is merely a reprieve.
- **Lamps and cooking fires cut back through the tint.** After the multiply,
  the lamp posts (LAMPS) and fire spots re-emit warm lighter-blended glows
  whose radius and strength scale with darkness — at full night the streets
  become the Kingdom constellation of warm pools in the cold.
- **A hero lantern.** A small flickering warm glow (radius breathing with a
  slow sine) around the hero's screen position whenever light drops — in the
  city at night, always in the home and cellar, and in the delvings (a fixed
  cold-evening lift plus the lantern is the whole grade down there; the
  depth-based dimming already in the floor renderer does the falloff).
- **Cloud shadows by day.** Three soft multiply ellipses drift across the
  city while there is daylight, fading to nothing at night.
- **The frame edges.** A cached radial-gradient vignette (transparent centre
  → 40% dark corners) plus a faint film-grain tile wobbling every few frames
  — Kingdom's finish, kept gentle enough that the HUD chips, menu text and
  dice stay crisp above it (the grading sits under renderUI/combat menus by
  design; combat, death and legacy screens get vignette+grain only).

Verification: a **real-pixel rig** (node-canvas as the actual backing store,
not draw-call stubs) — title, town at day/dusk/night/dawn, home interior at
night, dungeon with lantern all rendered and screen-inspected: night reads
cold blue with lamp pools punching through, dusk reads ochre, day readable
and balanced, interiors dim with warm glows; 2×1200-frame fuzz **with real
rendering** 0 crashes (it exercises the multiply composite path under cairo);
the full logic rig runs 12/12 green runs on top of the grading.

Correction note: the Kingdom build itself was first pushed as `c8a7910`
wearing a duplicate of the *repair* commit's message — the shipping command
had been rejected wholesale (a sandbox rule denies fetching the Pages URL
with curl, and the rejection cancelled the whole script, including the title
file write), so the follow-up commit ran with a stale title file. This
commit restores the true story above; remote state is verified with
`gh api` blob-sha comparison, never a curl of the Pages site.

## 22 — No event ever tests the same stat twice

User law, added as a standing rule: "there should never be tests for the same
stat in one event." A full audit of every event menu (75 `showMenu` blocks
scanned across town, delve, home, church, dock, doss and the loan's
arithmetic) found exactly two violators, both born of round 17's
persistence feature, both fixed by re-routing the second option to a
different measure of the keeper:

- **SUSPICIOUS FLOOR** offered "Read the floor (Wits)" and "Hunt for the
  mechanism's lever (Wits)" — two Wits tests in one sitting. The lever is
  now an act of hands: **"Hunt the lever and pry it over (Strength)"** —
  the wrench marks were left by an arm, not a mind ("you lean your weight
  into it"). Same DC, same permanent defusal, same dart on failure.
- **CHASM** offered "Jump (Agility 11)" and "Drop down into it (Agility
  10)". The controlled descent is now **"Lower yourself down and take the
  drop (Strength)"** — owning the landing is an act of the body's core.
  Same DC, same costs, same one-floor descent path for straight-pathing
  audits.

The clean events stand as built: SPIKES pairs "Cross carefully (Agility)"
with "Hunt for a release lever (Wits)" — genuinely different measures; the
mugger offers Steel-or-Will against Run (Agility); duels and tolls carry a
single Wits option each; the drill system's repeated rolls are training, not
an event, and are deliberately excluded from the law.

**Standing verification added — `eventaudit.py`:** every `showMenu` block is
scanned for `check("stat",…)` calls; any menu that tests the same stat twice
is a build failure. Result this build: **75 menus scanned, 0 duplicate-stat
events.** Logic rig re-run on the changed events: 9/9 (including failure-toll
walks and fuzz 3×1500 frames 0 crashes).

## 23 — The Kingdom round, second pass: a gate with stones in it, a night with no lying shadows

The first Kingdom pass graded the light but left the buildings as they
were, and the user was right to call it out: "you did nothing with the
graphics ... there cannot be any lamp in shadow that's just nonsense ...
can we make the gate to the city look like a gate and not a portal in
startrek?" Three fixes, all delivered:

- **The GATE OF OPENINGS is drawn as a real fortified gatehouse**
  (`drawGatehouse`, a full replacement of the gate branch of
  `drawBuilding`): twin battlemented towers with merlons and arrow slits,
  ashlar bond-lines every few courses, a carved round arch with a stone
  ring, closed oaken doors in two greens of plank with two iron bands and
  rivets, portcullis teeth hanging raised in the arch's dark, a torch
  sconce at each jamb (the only light the gate owns, flickering),
  a **static gold sigil plaque — deliberately no glow, it is carved stone,
  not a portal** — and a red banner hanging on the north tower.
- **The cloud-shadow pass is deleted outright.** A lamp may never sit in
  a travelling dark rectangle; the user's physics objection was adopted as
  a permanent design law.
- **Night means night, and the town answers it**: deeper dusk/night/dawn
  keyframes (dusk amber 0.34, night 0.66, dawn violet 0.32, dark divisor
  widened); a new `dayDark()` (0 at noon to 1 in deep night) is the single
  source of darkness for the windows, the lamps and the interiors; house
  windows are lit with probability `0.30 + 0.52*dayDark()` at map
  generation, and lit windows burn warm yellow with a soft glow at night;
  lamp pools widen with the dark (`34*sqrt(dark)+10` radius); roads and
  gravel get hashed pixel-noise cobbling.

**Verification:** real-pixel rig (node-canvas) shot title, town at day /
dusk / night / dawn, home at night, and the gatehouse at midday and night
close up; zoomed crops confirmed towers, battlements, arch, doors, portcullis
teeth, sconces, plaque and banner all read, lit windows glow, no dark
patches sit over any light. Fuzz 2×1200 frames with full rendering: 0
crashes. Logic rig on the changed engine: 9/9. `eventaudit`: 75 menus,
0 duplicate-stat events.

## 24 — The dice answer every hand: space, and a tap anywhere

The user picked up a real gap: the dopamine dice were stopped by Z / X only
on keyboard and only by tapping the exact 26-pixel die faces on touch —
"we didn't implement also touch action. we have spacebar that can be used
but the issue is you do not stop dice rolling with a space either." Confirmed
by rig against the shipped build: tapping empty screen while the dice rolled
changed nothing; space did nothing. The hint said "Z / X / tap" all the same.

Fixed in all three dice contexts, verified by a dedicated dice rig:

- **Space (and Enter) now catch a die** wherever Z does — combat attack and
  defense dice, the Last Stand's slow crawl, and the tavern Bones Game.
- **A tap anywhere on the screen catches the next die in combat** (the
  small die faces keep their own direct regions; the corner chips keep
  priority over the fallback so nothing else in the HUD can be mis-caught).
- **The Bones Game answers a tap of the whole table**, not just the die
  faces.
- Hint lines rewritten to match reality: "SPACE OR TAP TO STOP" in combat,
  "catch both sixes - space or tap" at the table.

**Verification:** dice rig — real combat through `launchDelve`, attack arms
the dice; tap-empty catches die 1; direct die tap still stops; SPACE stops
die 2; combat then resolves without hanging. Logic rig 9/9; eventaudit
75 menus, 0 duplicate-stat events. Injected through the structurally
asserted wrapper (DOCTYPE + canvas + `</html>`), round-trip diff clean.
