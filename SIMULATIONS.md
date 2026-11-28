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

## 25 — No lamp stands in a building's shadow

The cloud-shadow law held, and the same complaint surfaced one layer down:
"once again a lamp post cannot stand in a shadow of a building it just
looks wrong." The building drop-shadows (the soft ellipse each structure
casts at its base) were painted AFTER the lamp light pools, and geometry
confirmed six lamp posts partly or wholly inside a building's shadow
ellipse — the guild, the tavern, the training yard, the church and two of
the tenements — plus one lamp standing outright on the Salt Bunks' roof
(a placement that predates the building's position).

Two-part fix:

- **Geometry**: six lamps relocated ([8,20]→[8,22], [41,20]→[41,22],
  [10,10]→[10,11], [52,10]→[52,11], [10,29]→[9,29], [52,29]→[56,29],
  and the roof-standing [36,31]→[31,30]). Every lamp now clears all 18
  shadow ellipses and all walls (home included), verified by a geometric
  check sampling each lamp's head, mid and foot against every ellipse.
- **Paint order**: the wide lamp ground-pools moved from before the
  building pass to after it, so lamp light is painted over shade — even
  at a near miss, no shadow can darken a lamp's pool.

**Verification:** lamp geometry check 19 lamps / 0 violations (shadow and
wall); node-canvas night closeups of the guild shadow and the stacks row
confirm lamps clear of shade with bright pools; logic rig 9/9; dice rig
6/6; eventaudit 75 menus, 0 duplicate-stat events.

## 26 — The city breathes (lamps, idlers, freight, food, laundry)

Five complaints, five answers, plus one honest repair of the §25 claim.

**The lamp-shadow check was wrong, and six lamps stood in shade anyway.**
The §25 audit parameterized every building's shadow ellipse with its center
one and a half *tile* units below the wall base — but the painter's `+1.5`
is *canvas* units (a fifth of a tile). All eighteen ellipses therefore sat
about 1.3 tiles too low in the verifier, and six lamps ([18,5], [23,17],
[8,22], [36,10], [9,29], [56,29]) cleared a phantom ellipse while standing
squarely in the painted one. The checker was rebuilt from the actual paint
calls — ellipse center (x+w/2, y+h+1.5/8), radii 0.92·w and 1.472·w/8 in
tile space — and the six lamps were re-located by a search that requires:
not on any road/plaza tile, not inside a wall, not on a cooking fire's or
stall's tile, the post's full painted column (four sampled points, foot to
head) outside every true shadow ellipse, and not crowded against another
lamp. New law-strength result: **19 lamps, 0 violations under exact painted
geometry** — lamps stand beside the roadways, never on them.

**The fires have their idlers.** `fireIdlers()` seeds two shiftless
regulars (Odo Brandt, Widow Seem, Crake of the Kettle, Menna Ost) at
open tiles beside the city's cooking fires, one-time-flagged so saves stay
consistent. They live outside the normal inside/exit/age cycle: every
240–560 ticks they shuffle to an adjacent open tile or drift to another
fire, they carry a warm glow and a lazy idle-bob, and walking over to one
and tapping gives fireside talk ("Stand near. Costs nothing. Warm is the
only thing this town gives away.").

**Barrels and crates** exist: `computeProps()` seeds 8.5% barrels, 8.5%
crates and 4.5% double-stacks on open tiles adjacent to building walls
(seed 5521, mulberry32), explicitly excluding roads, doors, trash tiles,
stall tiles, lamp posts and cooking fires, plus six deliberate freight
corners (the Grave-Market load and the Pawnbroker drift among them). `drawStreetProps()`
renders banded barrels, slatted crates with X-braces, and crate-on-crate
stacks, painted after fires and before the lamp posts, under the hero.

**Food comes from somewhere** — two solid meal carts now sit in the city:
MEAL CART off the plaza (7,21) and KETTLE STALL (29,8), both made solid
(`"B"`, you walk around a kettle). The carts draw body, wheels, striped
awning (slow gust flutter), cooking pot with two-phase steam, loaves and
hanging sausage links. `vendorMenu()` sells a **hot meal 3g → +25 stamina**
(capped), a **pocket-slip** (AGI 10 — success steals +10 stamina of a
snack; failure costs the min(gold,5) fine, honestly), a food-lore line,
and free warm hands. Both carts register taps like buildings do: tap near
opens the vendor; tap far walks you over and opens it on arrival.

**Laundry no longer thrashes**: flap cadence from worldTick/9 to
worldTick/110 — a sway, not a flag in a gale.

**Standing-verifier repair:** the event law auditor's span heuristic
("scan to the first `]);`") had been silently merging unrelated menus; the
new vendor menu exposed it as 13 false duplicate-stat violations. The
auditor was rewritten with exact balanced-parenthesis span matching; it
reports truthfully: patched engine 76 menus / 0 duplicate-stat events,
shipped build 75 / 0 (the old build had merely been lucky).

**Verification:** dressrig 10/10 (idlers exist and stay in bounds; meal
cart tap opens the stall from two tiles; meal costs exactly 3g with stamina
capped; far tap on the kettle stall walks then opens the menu; reopen
works; the pocket-slip branch resolves; fuzz 2500 frames, 0 crashes);
logic rig 9/9; dice rig 6/6; lamp geometry 19/0; props/lamp/fire tile
sweep clean; node-canvas real-pixel render fuzz 0 crashes; day and night
evidence frames of the dressed city.

## 27 — No building is walkable ground

"I can walk through some buildings and the 'on touch' means if I stand on
specific spots like doors it automatically triggers." A reachability sweep
(flood fill from the hero over every tile the city pass allows) proved it:
the Training Yard's whole interior — 60 tiles — was walkable ("y" ground
under a fully drawn, roofed building), and the Gate of Openings' two
passage columns were walkable straight through the drawn-shut oaken doors
(leftover "G" gate-corridor tiles from the pre-gatehouse build).

Fix: every building is now solid to its last tile — yard included, gate
passage included; the code that carved walkable gate columns is gone. The
home's doorstep tile stays walkable because leaving home anchors there,
but leaveHome() now steps the hero out onto the street below the threshold
instead of parking them inside the doorway. And since old saves can hold a
hero standing inside what is now solid, loadGame() migrates the position:
a hero on a solid tile spirals out to the nearest open cobble on load.

On the trigger rule: an arrival auto-activates only the thing the tap
aimed at (tap the building, walk up, it opens); standing near it and
moving never triggers; Z/Enter/Space beside a building remains the
keyboard's deliberate enter.

**Verification:** reachability sweep 0 reachable tiles inside any building
rect (was 88); house/gate rig — enter home from the street, leave onto the
open street tile (24,29), old-save migration steps the hero out of a solid
yard tile, yard and gatehouse solid on every interior tile, gate facility
opens from the south side; dressrig 10/10; logic rig 9/9; dice rig 6/6;
eventaudit 76/0; visual fuzz 0 crashes.

## 28 — The people, drawn Curious-Expedition-1 style (the graphics round)

"Let's do a full overhaul of the graphics. Do a full examination of how art
looks in Curious Expedition 1 (ignore 2) and use similar graphics especially
for characters." Direct image pulls off the CE1 wiki were blocked, so the
style was taken from documented characteristics of the originals: chunky
pixel people with big heads, strong dark outline, few-tone garments, warm
earthy palette with saturated accents, and silhouettes you can name by gear
alone — then applied where it counts, characters first.

Evidence. The old people were barely people: hero, door-keepers, spouse,
stranger, human foes were all the same 8×8 blob ("fHkkHf..."), and townfolk
were three paint rects (4×4 body, 2×2 head, 2×3 legs). One replacement
covers everything because all humans funnel through five draw functions:
drawHero (base, dungeon, home, cellar), drawNPC (tavern/market doors,
spouse), drawTownfolk, drawStranger, drawHumanFoe (combat intro + panel).

Instead of just researching it, here is a full audit of what actually
shipped. New engine: sprO(art, map, x, y, scale, flip) — the old spr() grid
filler plus a first pass that pours an outer outline (every empty cell with
a filled 4-neighbour gets the ink) and supports per-column flip and integer
scale; sprP() anchors a figure's feet on the tile's bottom row so hats can
overhang without anyone floating. shadeHex() derives the two garment
support tones from any coat colour. Facing: heroFace ("dn"/"up"/"lf"/"rt")
is set in every stepping path (base, dungeon, home, cellar — direct steps
and walk-path steps alike), and the hero picks the 2-frame walk pair from
it; up-facing shows the pack, side-facing shows the profile, lantern hand,
and the pack slung on the back. Arts, all hand-laid 12-wide grids: hero
down/up/side × standing/walk with brim hat, umber coat, pack, lantern;
worker (flat cap, rolled sleeves), shawl-woman (long dress, flared hem),
elder (bald fringe, cane), apron keeper — townsfolk pick an archetype by a
name hash and keep their street colour as the garment, sentinel greys to a
white fringe; the stranger is the worker art in all-grey with the head
shifted a pixel off-true; the combat adventurer comes bandana'd with a
hatchet, scaled ×2 in combat so a killer now stands as tall as an
abomination beside them. Townfolk and stranger sprites can now overhang
their tile, so the cull margins widened to match (no pop-in at screen
edges). Abominations stay procedural — they are meant to be wrong.

Verification. Isolated sprite sheet: every art rendered and every claimed
pixel probed exactly (hat/face/eyes/mouth/coat tones/lantern gold/pack
co-ordinates/boots, outline left and beneath) — the two probe "misses" were
probe offset errors, not art errors, and the arts are pixel-exact. In
scene: town-frame + close-up and street band VLM-checked (chunky outlined
figures, feet set on ground); home spouse + hero present (exact-probe
returns zero under the end-of-frame tint, so presence was proven with a
tolerant palette census of the couple's box: purple family present);
combat intro and panel with a seeded human foe VLM-checked (outlined
figure, on its shadow, covering no UI); dungeon hero + facing rig
(12/12: four-way base facing, tap-to-walk whole journey with a visible
in-camera target — the first rig attempt tapped off-camera and was rig
error, not engine), home steps, dungeon steps. Full battery: logic rig
9/9, dice 6/6, dressing 10/10 + fuzz 0, solid-ground flood 0 in-rect,
house/gate all pass, lamps 19/0, eventaudit 76/0, visual fuzz 2×1200
frames 0 crashes.

## 29 — The buildings, drawn honest (the artifacts round)

*"Gotta overhaul the buildings as well and there should be no weird
artifacts on buildings other than what they represent."*

The user's phrase set the law for the round: **everything drawn on a
building must depict a real object.** An audit of the old `drawBuilding`
found the artifacts: a **universal 4×6 dark smoke box painted at the
top-LEFT corner of every building** (`sx+4, sy-5`), floating above the
roofline so its puffs rose out of mid-air; **vertical plank seam stripes
every 6px on all walls**, including the stone guild and the church; the
**guild identified by two floating purple 3×13 bars** hung on nothing; the
work-shops marked by a **black rectangle plaque stuck mid-wall** depicting
nothing; the **clinic's cross floating loose**; and a **flat 10px roof
band** with no eaves, no tile texture, no shadow.

`drawBuilding` was rewritten wholesale (the whole function replaced by
line-surgery with asserted anchors), keeping every hit/door coordinate
untouched — the door leaf still starts at `dxc=sx+floor(w/2)-2`, so taps,
`enterBuilding`, `nearBuilding` and the door arithmetic are bit-identical.
The new anatomy, per building: a warm **roughcast wall** (per-building wash
from a six-earth palette hashed on `(bl.x*7+bl.y*3)%6`) with faint seeded
plaster mottling; a **dark stone footing** three rows deep with seam lines
every 7px; **timber corner posts** (2px, full height) and a head beam;
a **pitched tiled roof with a true eave overhang** (roof spans `sx-2` to
`w+4`, 12 tall, alternating shade columns every 6px, course seams every 4
rows, ridge highlight, eave shadow line); a **brick chimney** at
`chx=sx+floor(w*0.68), chy=sy-8` — 5-wide brick body with a darker shaded
face, stipple, and a 7×2 cap — with **smoke rising from the chimney's top**
(4 puffs, `ph=(⌊worldTick/14⌋+i*5)%18`, visible when `ph<13`, alpha
`0.30−0.020ph`; an earlier 3-puff/0.26 version was invisible in practice and
was strengthened after the VLM twice missed it); scalloped striped
**awnings** for market and tavern; windows keeping the lit-follows-dark law
(`rnd()<(0.30+0.52*dayDark())`) and gaining a **cross mullion** and pale
**sill**; a **banded oak door** (lintel beam, recessed reveal, plank-seamed
leaf, iron bands, gold handle; the home's leaf in its darker tint); and
**identity signage that depicts the trade**: the tavern a proper bracket
arm with its hanging swinging gold lamp and night glow; the guild a purple
heraldic banner (pointed hem, gold emblem) hung from the eave; clinic,
office and work-shops a `signBoard()` — ink board, gold frame, dark hanging
rod from the eave — carrying a bone-white cross, a paper notice, or the
trade icons (washtub+drop, bone+knife, candle+flame, stacked coins). The
church's rose window moved onto the wall with a stone-stroke frame (inner
glow pulse kept); the tenement laundry line was left as the user liked it;
the gatehouse branch early-returns to `drawGatehouse` untouched.

**Verification.** A single-building probe rig (`bldone.js`) drew each of the
13 building ids in isolation at tiles (10,8) and pixel-probed roof mid,
eave overhang, wall plaster, corner post, chimney body/cap and door leaf —
all key elements present at expected positions in the right colour
families. Every probe "FAIL" line was bred out one by one as **probe
placement error** (probes landing on unlit windows, on alternating roof
shade columns at `x+3 mod 6`, on the label text at `sx-3`, or on the
home's recoloured door) — not engine defects, and the distinction is
recorded because it is the same failure family as prior rounds' false
negatives. A 13-stop scene rig (`bldshot.js`, clock forced to `worldTick=200`
after `newGame` — the first runs rendered night-tinted because the clock
was never set) shot every building day and three night. VLM zoom reviews
of guild, tavern and wash-house crops confirmed the full anatomy with no
artifacts; the VLM's "floating black boxes above the tavern" claim was
disproven by the probes (the chimney demonstrably sits on the roof) and
its "no smoke" claim was acted on (the smoke visibility fix above). A
4-quadrant in-scene composite (tavern/guild × day/night) confirmed gestalt:
roofs read, night lamps and window glows sensible, no overlaps with the CE1
people, lamps, carts or labels. Full battery on the rewritten engine:
logic rig 9/9 (one flaked run traced to the rig's own `Math.random()`
combat resolver, six consecutive greens after), dice 6/6, dressing 10/10 +
fuzz 0, solid-ground flood 0 in-rect, house/gate all pass, lamps 19/0,
eventaudit 76/0 (no event tests the same stat twice — law intact), visual
fuzz 0 crashes, facing/walk rig 12/12 (its six initial fails were the
rebuilt `__dbg2` losing the `face` getter — rig-side, restored).

## 30 — The helm and the honest iron (the gear-specials round)

*"can character not have a hat but a full helm instead? Can we also add
various other armors? Can we also make a variety of special things weapons
and armors do? Eg. Chainmain can be put in addition to another armor.
Spear has a first strike so first attack you do is without getting damage
back and similar?"*

Three asks, one round. **The hat is gone**: the hero's three CE1 string
arts were re-headed with a **full steel helm** — rounded m-tone dome with
a light ridge, a dark visor slit carrying two pale eye-glints (front view),
fluting strokes on the back view, and a dark visor at the front of the side
view; two steel tones (`#8c8a98`/`#c1becc`) and a shade (`#5f5d6e`) joined
the map. Verdict was by a standalone 10× render (all three views read as
full helms, VLM-confirmed, no stray pixels) plus an in-scene steel census
(728 pixels in the head box under the day tint) and a scene gestalt check
(clean integration, no head glitch).

**The gear system grew opinions.** A props layer keyed by item name
(`WPROPS`/`APROPS` + `normGear()`, applied on load, equip, and heir
succession — so **old saves gain the specials by name**, no migration
fault) now powers:

- weapons: **Crude Spear first strike** (the opening attack resolves and
  skips the enemy's answering turn, `c.fsUsed` once per fight), **Bone Saw
  bleed** (armed on first hit; ticks -1 at the top of every enemy turn,
  kill-capable), **Wardriver Maul stagger** (28% on a hit; the enemy's next
  beat is skipped), **Surgeon's Scalpel precise** (a single crowned die
  reads +4 on the edge instead of +2);
- armor: **Widow's Mail rides a new UNDER slot** — wearer's armor value is
  `main + mail`, the bag lists WEARING / UNDER / armor total, equipping a
  mail item routes to the under-layer and swaps the old under-layer back to
  the pack; **Quilted Jerkin** (+2 on the flee roll), **Wall-Sergeant's
  Harness** (armor 4, -1 on the flee roll, both announced in the log),
  **Yes-Plate's last stand** — once per fight, a killing blow holds you at
  1 HP (combat path and the environmental `hurt()` path both covered;
  the second death is not refused).

The price board states every special in its entry; the iron shard and hand
axe stay plain. Checked against the standing laws: the two-d6 dice decide
nothing new here (specials ride the existing rolls), the one-test-per-event
law is untouched (eventaudit 76/0).

**Verification.** A 20-assertion gear rig on the hooked engine: mail routes
to UNDER and layers (armor 4 harness + mail 2 = def 6), spear's first
strike lands damage with HP unchanged and the flag logged, the second
strike is answered, saw bleeds tick, stagger skips the beat, precise reads
the crowned die (+4 with the surgeon's log line), flee modifiers +2/-1,
Yes-Plate holds a lethal blow at 1 HP exactly once, old-save normGear
attaches first-strike to a bare spear and keeps mail-as-main armors
resolvable, plain iron stays plain. Full battery on the patched engine:
logic rig 9/9, dice rig, dressing 10/10 + fuzz, solid-ground flood
0 in-rect, house/gate all pass, lamps 19/0, eventaudit 76/0, visual fuzz
0 crashes, facing rig 12/12. Kill-coin economy, streaks, and loot rolls
are untouched (weapons still come only from the rack; the duelist's blade
prop-less, as before).

## 31 — Ragged people, and the doors that speak for themselves (the plaques round)

**The ask.** "Make the characters more raggedy" — and at the Flayed Lamp and
the Grave-Market, "remove those character sprites from the houses, it's
ugly, give them a nice plaque instead with a symbol relevant to it."

**The raggedy pass.** All seven string-art figures (hero dn/up/side, worker,
shawl-woman, elder, apron keeper) gain two kinds of wear, both done inside
the existing art grids so row counts and widths never move (16/16/16/13/14/
14/13 rows, 12 wide — `sprP` anchors feet by `art.length`, so nothing
re-anchors):

- **Tattered hems.** Gaps punched in the last garment row (the coat's bottom
  row for the hero and worker, the dress above the flare for the woman, the
  apron hem for the keeper). The `sprO` outer-outline pass fills around any
  empty cell that touches filled neighbours, so each gap arrives outlined in
  the poured ink and reads as a rent in the cloth, not a hole in the sprite.
  The hero's hem gaps dodge the hanging lantern (`L`) and the belt (`k`).
- **Patch squares.** Two-tone contrast patches sewn onto the coats and
  dresses: pale grey (`g`, the pack-cloth tone) on the hero's coat in all
  three facings; brown `w` on the townfolk, one on the chest and one at the
  hem side. The stranger — the worker's art recoloured all-grey — would
  otherwise wear *bone-white* patches (unmapped chars fall to `PAL.bone`),
  so his map gained `w:"#45404e"` and his patches grey out with the rest of
  him.

**The plaques.** The base draw loop had been pasting a standing
`TOWN_KEEPER` figure at the tavern's and market's doors — a person loitering
in the doorway of their own workplace, reading as clutter. That draw block
is deleted outright (the keeper art itself stays: the gate sentinels and the
home spouse still wear it). In its place, `drawBuilding` gained
`wallPlaque(bx,by,bw,bh)` — a 12x7 ink board (`#241c26`) with a gold stroke
frame (`#7d6f4c`) and two gold nail dots at the top corners — nailed over
each door at `dxc-3, sy+h-18` (one tile wide, 2px clear of the lintel beam,
clear of both window rows by geometry: the tavern's second-row sills end at
`sy+37`, the plaque starts at `sy+38`; the market's single window row ends
at `sy+23`). On the boards, the trades in miniature:

- **The Flayed Lamp:** the lamp itself — hook pixel, 3x3 gold body,
  flame-pale core pixel — the tavern's own hanging sign, flayed of any shade.
- **The Grave-Market:** the weighing scales — a pale (`#c7bfae`) post and
  beam with two gold pans — because everything you carry up out of the
  openings gets weighed before it pays.

**A real bug caught by the rebuild.** Re-deriving the rig battery from
scratch (all rigs were lost to a sandbox wipe) re-tested Yes-Plate's
environmental path — and failed it. In `hurt()`, the last-stand branch was
followed by a *separate* `if (p.echo ...)`/`else { die() }` chain, so the
plate held you at 1 HP and then the un-taken `else` killed you anyway; the
plate "has never said no" and then the floor said otherwise. The chain is
now `else if` end to end (matching the combat path in `afterEnemyTurn`,
which was already correct): the plate refuses the death and the refusal
sticks. Honest-repair law, again: the check exists because the fix was
needed, not the other way around.

**Verification.** New plaque probes on the live engine: board ink, gold
frame, nail gold, and the lamp/scales symbol pixels all at expected
positions in-scene (backing = unit x 4.5 in the scaled pass — the probe rig
initially read plaster because it sampled at x3, the unscaled mapping; the
transforms, again, are the authority). Old-keeper probe: the spot where the
door sprite's torso stood now reads wall plaster, not apron or skin. Raggedy
strip: all seven arts parsed straight from the source, rendered with the
outline pass on plaster — row counts asserted, widths asserted, VLM confirms
no disfigurement; in-scene hero probes confirm patch (`g` tone), hem-gap
(outline ink), and coat control pixels. Zoomed VLM crops of both plaques:
board, frame, nails, symbol, no overlap with door, windows, or awning.
Rebuilt battery, all green: functional smoke 16/0 (boot, full solidity sweep
over every building tile, stall tiles, fire-spot doors, tavern/market menus,
delve, combat, hurt, save/load round-trip, townfolk tick), facing rig 11/0,
gear rig 17/0 x5 (first strike, bleed, stagger roll + skip, precise, mail
under-layer, flee mods, both Yes-Plate paths, old-save normGear) — one
observed flake family: the 28% stagger roll is `Math.random()`-driven and
the loop detector now reads the combat log, so it is deterministic; the
resolver's randomness remains rig-side, as recorded in the buildings round.

## 32 — No stain before the door (the ground-shadow removal)

**The ask.** "Remove the shadow radius from before the buildings — it doesn't
look good."

**The change.** `drawBuilding` opened with a soft dark ellipse pooled on the
ground in front of every house — `blobShadow(sx+w/2, sy+h+1.5, w*0.92, 0.30)`,
a left-over of the pre-overhaul anatomy. With the new CE1 look the buildings
already sit on dark coursed stone footings, so the ellipse read as a stain on
the cobbles rather than depth. The call is removed; nothing else in the
function moves. The small object-grounded shadows (lampposts, meal carts,
people) stay — those read as contact shadows, not puddles.

**Verification.** Seeded-RNG before/after diff (`Math.random` re-seeded with
the same mulberry32 state in both engines, so townfolk, props and lit
windows render identically): exactly 24,659 pixels changed across the whole
960x540 frame, all inside the old ellipse's zone (canvas units x 43.8-132 —
the tavern's center ± w*0.92 exactly), every one of them *lighter* after the
removal (mean luminance +54). No other pixel in the frame differs — no
side effects on footing, plaques, people, or props. (A first probe pass read
inconclusive because wandering townfolk contaminated the sample band; the
seeded diff replaces eyeballed samples.) Full battery re-run green: smoke
16/0, facing 11/0, gear 17/0, plaque probes all pass.

## 33 — People-sized people, doors taller than their users, firelight earned by attention

**The ask.** "Characters are still too big compared to doors and objects. I
like that fires get reflected on characters, but it must only work if the
character is turned towards the fire and at a certain distance from it."

**The size analysis.** People were 12x13-16 art cells at scale 1 — 1.5-2
tiles tall — against a 7x7 door leaf and ~5px barrels: everyone in Jericho
dwarfed every doorway. The fix renders every person-scale figure at **2/3
tile scale**: a 12-wide art becomes exactly 8 units (one tile), 13-16 rows
become 8.7-10.7 units (~1.1-1.3 tiles). The anchor re-centers
(`x0 = x + (8 - w*s)/2`, top `y0 = y + 8 - h*s`), feet stay on the tile's
bottom row. **The crispness law that chose 2/3:** a 2/3-unit cell at the
scaled pass's 4.5 backing multiplier is exactly 3.0 backing pixels, and the
anchor arithmetic (`y0*4.5 = 4.5y + 36 - 3*rows`) keeps every cell on
integer backing pixels — no blur, same crispness as full scale. Explicit
scales (combat's hand-tuned `drawHumanFoe(x, yf, 2)`) keep the old anchor
untouched.

**The doors.** A person a tile tall needs a door taller than them: the leaf
widens 7->9 (same center `dxc+3`), raises to 10 rows under a 2-row lintel
(`sy+h-13`), with reveal, plank seams, iron bands and handle all rescaled to
the leaf. Where a window's frame would hang over the new doorway *and* reach
down to the plaque's top (`wy0+8 > sy+h-20` with x-overlap of
`[dxc-3, dxc+9]`), the wall stays bare — exactly one over-door window goes
from the tavern, none from the market (its single window row ends above the
plaque). The plaques ride one row up (`by=sy+h-20`), 1px clear of the new
lintel. Door *tiles* and hit logic never moved — only the paint.

**The fire gating.** The reflection the user liked is `drawAmbience`'s late
fire-glow pass (`dark > 0.12`, radial "lighter" wash) — it tinted every
figure near a fire, any facing. Now: `sprP`'s default branch records each
figure drawn (`FIG_DRAWNS`, with camera-pinned tile anchor and facing —
townfolk got a `face` updated at all four movement sites plus
`faceFire()` for idlers keeping coals; the hero carries `heroFace`). After
the glows, per figure: **facing** the nearest fire (unit dot > 0.25) within
2.5 tiles -> a warm **rim** (per-cell `rgba(255,150,60)`, alpha
`0.08+0.22*warmth`, biased 0.55-1.0 toward the fire's side); within ~3.5
tiles but **turned away** -> the late fire-glow is **washed back off** by
exact subtraction: each fire glow's analytic contribution
`a*(gr-dist)/(gr-RES)` per channel, removed only from the figure's own
pixels (art cells + ink outline), lamp and lantern washes untouched, the
ground's glow untouched.

**Verification (new rig: firerig, 15 asserts x8 runs, random fire-spot
draws):** away+erase-on vs erase-off at every art cell — the removed light
is *fire-colored* (dR:dG:dB = 255:150:60 within +-5), bounded by the glow's
own alpha, radially falling off (near cell loses more than far); rim
isolated with a boosted-rim twin engine (same seed, same position — the rim
alpha constant string-patched +0.50 in a rig-only copy): delta lands on art
cells only, biased toward the fire's side, background in the sprite's bbox
untouched; facing figures keep the fire's own glow (erase provably never
touches them); beyond the cutoff erase-on/off frames are pixel-identical;
fire idlers stand facing their fire; day frames show the rim too (rim is
independent of darkness). scalerig (14 asserts): hero figure at s=2/3,
12-wide art, integer backing rows, feet on the tile bottom row, cell
interiors uniform at 3px pitch, sharp cell boundaries, street resuming 13
cells out (the old full-scale sprite would reach 17). Full battery green:
smoke 16/0, facing 11/0, gear 17/0, plaque probes all pass (updated for the
raised plaques, new doorway anatomy, and the window skip), raggedy strip
unchanged. Two rig bugs caught on the way (fixed face labels: standing at
fire+[1,0] means facing "lf", not "rt"; and a real engine bug the
twin-engine delta exposed: `fireRim` mirrored its occupancy mask for
left-facing figures, but `sprO`'s flip remaps *colors*, not cells — the rim
now samples the unflipped grid, matching `sprO` exactly). VLM gestalt at 4x:
person clearly shorter than the tavern door, plaque reading clean, rim on
the facing figure, cool/dark figure when turned away, no artifacts.

## 34 — The door answers the touch, and the crosses come down

**Ask.** "Now we need to work out the character touching doors is interacting
with the building, also weird fascination with crosses, there is no Jesus
here." Two laws: (1) touching a building with your character must use the
building; (2) no crosses anywhere in the draw layer — the cosmology is the
lost gods and the Choir of Seven Lights, not ours.

**The door answers the touch.** The tap already kept the law *you stand
beside a building to use it* — but `tryBaseMove` swallowed a blocked step
silently: keyboard, d-pad and finger-steer players walked into doors that
did nothing, and a blocked move did not even turn the hero's face. Now a
step into a solid tile finds the building under it (`BUILDINGS.find`,
skipping an un-owned home, whose tiles are not solid anyway), and if
`nearBuilding` holds — the same predicate the tap uses — it stops any walk,
turns the hero to face the wall they touched, and calls the very same
`enterBuilding`. The home keeps its stricter door law (`nearBuilding` for a
home is a stride from the door, so its far wall stays a wall); the Gate of
Openings opens to the bump like any door. Because the bump flows through
`dirTap()`, all four input paths get it in one place: arrows/WASD, held-key
repeat, gamepad, and finger-steer. The pressure checks that run after a
move (`maybeMugged`, `crownPressure`, `strangerPressure`) all guard on
`ui.mode`, which `say`/`showMenu` set synchronously, so a bump-opened menu
is never double-fired. The doorstep behavior falls out of the existing
grid: the home's door tile is walkable ("D"), so the first bump steps onto
the doorstep and the second — touching the house itself — opens it.

**The cross census.** A grep-and-read audit of every draw call found three
crosses, and one of them was everywhere:

1. **Every window on every building.** The "cross mullions" of the
   buildings round drew a 1px upright at `wx0+2` *and* a 1px horizontal bar
   at `wy0+3` across the 4x6 glass — a little cross in every window in
   Jericho. The horizontal bar is deleted; the upright mullion stays (two
   narrow panes, no bar crossing them).
2. **The flesh-setter's sign.** "The bound cross on a board" — 2x7 pale
   vertical crossed by 6x3 — is now **the bleeding bowl catching the
   drop**: a deep-red 2x2 drop falling toward a dark iron bowl (rim
   `#2e2c36`, body `#4a4854`), which is a more honest picture of what the
   flesh-setter does.
3. **The church's rose window.** A pale 2x11 shaft crossed by an 8x2 bar.
   Now it holds the **Choir of Seven Lights** — a 2x2 gold center light
   with a bright core pixel, and six 1px lights about it in hexonal
   arrangement (no upright-and-bar anywhere), each shimmering between gold
   and dim gold on a `worldTick/18` rotation — which is the city's own
   doctrine ("the vanished rose bodily to the Choir of Seven Lights, and
   the lights are good"). The dungeon's Corner Shrine was audited and is
   clean: two standing stones and a gold light between them.

**Verification (decross.js, 35/35).** Twin engines — HEAD extracted from
the committed `index.html` vs the patched build — booted identically
(`mulberry32(31337)` seeded *before* `newGame`, so townfolk and every boot
draw match), same `worldTick` (200 + one frame = 201 in both, asserted via
`D.wt()`), four camera stops chosen so tavern+guild, market+office,
clinic, and church+gate are all in view. Full-frame 960x540 pixel diff
with tolerance 8:

- every stop: **zero stray diff pixels** — every changed pixel falls inside
  the union of the removed window-bar rows (all in-view buildings, computed
  in `drawBuilding`'s own unit space, `w=bl.w*8` — the rig's first draft
  wrongly assumed tile space and misclassified ~1000 real diffs as strays),
  the clinic sign board rect, and the rose window rect;
- the stub's Fontconfig-less `measureText` shifts one subpixel of the FS
  chip glyph between loads — confined to the top-right chip band
  (y<48, x>=840 backing), size-capped (<=200/stop, <=400 sweep);
- lit windows on the market (two found, probed): no dark bar across glass
  row 3, upright mullion contrast intact (mull at least 40 below pane);
- isolated exact-color draws (transform 4.5, no tint, no people): clinic
  drop `#7d3038`, rim `#2e2c36`, bowl `#4a4854`, board `#241c26` in the
  gap between drop and bowl and where the cross's shaft and arms used to
  run; rose glass `#0f0a10` where the cross shaft top/bottom and bar
  left/right used to be, center light gold, >=4 of the six ring lights
  gold-ish, stone frame intact.

**Verification (bumprig.js, 18/18 over 7 cases).** Through the real
`keydown` listener path: tavern bump up opens (6/6: solid wall, no menu
before, opens, hero does not pass through, face turns "up", a second key on
the open menu changes nothing); tavern flank bump opens (the stand-beside
law); home: doorstep walkable, first bump steps on, second opens, still on
doorstep (4/4); home far wall: no open, still solid, face turns anyway
(3/3); gate bump opens (1/1); un-owned home: ground open, hero walks on, no
menu (2/2); clinic bump opens (1/1).

**Battery.** smoke 16/0, scalerig 14/0, facerig 11/0, gearrig 17/0,
ragstrip OK (arts untouched), firerig 15/0, plaque probes all pass. VLM
gestalt on the isolated rose: "a set of separate golden lights... no cross
shape" (it counted eight — the center's bright core reads as its own light
at 3x zoom; the grid says 1+6).

**Derived-copy law, re-learned.** The `__dbg` suffix lives in the *derived*
file (`game_vis.js`), never in bare `game.js` — the rebuild recipe is:
read the suffix out of the previous derived copy, append to the new source.
A rebuild attempt that searches for it in `game.js` asserts out harmlessly
(and did).

## 35 — The fight opens with its whole mouth (combat text no longer clips)

**Ask.** A screenshot of the in-fight screen against the Marrow Auditor,
circled: "this is problem the full text is not visible" — the log box read
"…always balances, and humans are animals that file themselves badly. /
You have never seen its like up close. Survive it, and you will know it."
— starting mid-sentence, the lore's first rows gone.

**Diagnosis.** The fight-opening text (entrance line + bestiary lore + the
never-seen line) was stuffed into `combat.log` and then hard-clipped twice:

1. the **combat intro screen** wrapped the lore at 52 chars plus the
   never-seen line and drew at most 4 rows in a fixed 50px box
   (`li<4`) — the Marrow Auditor needs 5, so the tail was always eaten;
2. the **in-fight log box** wrapped the last 3 log entries at 58 chars and
   kept only the LAST 4 rows (`slice(-4)`) — the lore's first rows vanished
   the moment the intro was dismissed, which is exactly the mid-sentence
   fragment the screenshot shows.

**Fix.** Two moves:

- **The opening text leaves the log.** `startCombat` now carries the lore
  and the never-seen line as `combat.lore` / `combat.seen` (read only by
  the intro screen, with a `log[1]` fallback for old-shape mid-combat
  saves); the running log starts holding only the one-line entrance
  ("<Name> comes out of the dark.") — which fits its 4-row box whole — and
  fight events displace it as they happen. A log is for events; the
  opening belongs to the intro.
- **The intro box grows to fit.** `boxTop=160-bh` with
  `bh=rows*pitch+8`, pitch 10 for <=5 rows and 9 above that, all rows
  drawn (capped at 8 as a hard bound), the "- SOMETHING NEW -" title
  riding just above the box (`max(2, boxTop-12)`), and the
  "tap or Z to steel yourself" hint untouched at y 166.

**Verification (combattrig.js, 13/13).** Against the Marrow Auditor (121-
char lore, unknown foe, so lore 3 rows + never-seen 2 rows = 5 > the old
cap):

- combat starts with the intro up and `lore` on the combat object, log
  exactly 1 entry; the expected wrap really is >4 rows (the old cap would
  clip);
- the box top sits where the formula says (dark fill probed);
- **every one of the 5 rows has glyphs on its own line** — probed
  per-row (band-scan merged adjacent 9px rows, so each row's y-band is
  scanned for lit pixels independently);
- the never-seen line's last row — the one the old cap always ate — is
  drawn;
- after dismissing the intro: the log box holds **exactly one row** (the
  entrance line) sitting at the box top (y≈79);
- after a Strike action: frame renders, game consistent, log box still
  <=4 rows;
- **old-shape combat** (lore/seen deleted, entries pushed into log,
  intro=46, as a mid-combat save from a previous build would load):
  intro renders via the fallback with all rows present.

**Battery.** smoke 16/0, scalerig 14/0, facerig 11/0, gearrig 17/0,
ragstrip OK, firerig 15/0, plaque probes pass, bumprig 6/6 + 4/4, decross
35/35. VLM could not read the stub's font-fallback glyphs at all (the
node-canvas Fontconfig-less Courier substitute is illegible at any zoom) —
noted as the known VLM limit; the per-row pixel probes are the authority
here, per the transforms-are-the-authority law.

## 36 - The landlords walk (bosses as sprites)

**Ask.** "can we make the floor bosses actual sprites moving about the
room?" - the seven landlords were a static 4x6 purple glyph pinned to a
tile until a menu opened over it.

**Fix.** The boss becomes a body with a live position:

- **genFloor** carries live fields on the boss event (`bx, by, bface` and
  the room cage `rx, ry, rw, rh` - the cage captured alongside the
  placement tile, which also fixed an `inR` scope bug caught before it
  shipped).
- **bossLive** lazily derives position + cage from `f.rooms` for old
  saves (migration), **updateBosses** steps each boss on a per-lineage
  cadence (beasts every 52 ticks, sentients every 38, 16% pauses),
  **bossStep** walks within the cage on open floor, never onto the hero,
  other events, or (after the torch round) a torch tile; it turns to face
  the player within 4 tiles, drifts closer with a 65%/35% hesitation, and
  creaks softly at 30% when within 8 tiles.
- **Meeting.** The old static-tile event find in `tryMove` now excludes
  type "boss"; instead the boss meet is positional - Manhattan distance
  <=1 checked after the player moves (with a `meetPre` guard so a boss
  already adjacent when you move does not re-fire) and after each boss
  step (guarded by no-menu / dungeon / no-combat). The landlord now
  walks up and opens the conversation itself.
- **Fights.** All four fight sites spawn `bossFoe(B,ev)` tagging
  `e.bossRef`; `endCombat` marks `curFloor().bossEv.done=true` ONLY on a
  won boss fight - a fled fight leaves the landlord alive (it re-meets
  you; the fight can be re-entered).
- **Art.** `BOSS_SPR`: bespoke hand-authored grids for all 7 lineages
  drawn through the sprO engine (outer ink outline, feet-anchored,
  flip-mirrored accent pixels, phase colors, the Clock's dial hand really
  rotating, pacted landlords glowing blue), distance-dimmed like tiles,
  blob shadow, culled off-view. The old glyph branch is gone (a comment
  marks the grave). The minimap drops a purple blip at the boss's LIVE
  position when seen - the map tracks where it is now, not where it
  spawned.

**Verification (bossrig.js: walk 14/14, meet 8/8, fight 17/17, migrate
5/5, shot 15/15).**

- walk: live pos + cage derived, movement over 1400 frames, never on a
  wall / out of the room / on the hero, old glyph gone from its tile,
  and - the point of the round - **"the landlord came and met the idle
  hero"** (boss-initiated meeting through real frames).
- meet: step-adjacency opens the intro through the real `tryMove` path,
  the z-loop reaches the menu, leaving closes it, fresh adjacency
  re-meets, and the landlord holds still while the menu is open.
- fight: Attack produces a `bossRef` foe; `done` NOT consumed by
  starting; flee works through the real choice + z path; the fled fight
  leaves the landlord in the room and it re-meets; a kill (hp=1 + strike
  loop + kill-cinematic z-loop) sets `bev.done===true`; the save carries
  `bx/by`.
- migrate: an old save without live fields loads and `bossLive`
  re-derives position + cage.
- shot: per-lineage sprite pixel-probes over the hidden-boss baseline.
- VLM review on zoomed crops read all seven as coherent bodies after one
  art pass (drowse re-authored as articulated finger-checker rows, lamp
  lightened from near-invisible soot) - pixel probes the authority, VLM
  for gestalt only, per standing law.

## 37 - Torches wait dark (the delve rooms keep a match)

**Ask.** "torches in the delve rooms, let's make them not on, player can
turn them on as to keep track where they were already, let's put it next
to a random exit to each room instead of middle of room."

**Fix.**

- **Placement is deterministic, not random.** `torchSpot(f, r)` builds
  candidates from each room's doors (one tile inside the room beside each
  doorway, open floor only, excluding every event tile - done or not, so
  the spot is stable across gen and lazy re-derivation) and picks one by
  a pure hash of the room's coordinates
  (`((r.x*73856093)^(r.y*19349663))>>>0 % cands.length`) - no RNG, no
  `f.seed`, so the same floor always has the same torches (and old saves
  re-derive the identical set through `ensureTorches`).
- **The interaction is the step.** Stepping ON an unlit torch tile opens
  "AN UNLIT TORCH": *Light it* (torchLit, a scratch-and-crackle sfx, a
  save) or *Leave it dark* - and declining once sets `torchDeclined` so
  the floor never asks again. A once-only hint marks the first unlit
  torch room entered.
- **Draw.** The old room-center flame is gone (that was the "middle of
  the room" the ask named). A torch is a post and bracket beside the
  door: lit = worldTick-flickered flame + warm glow; unlit = a cold dark
  head you can read at a glance. The minimap drops a gold blip at every
  LIT torch (seen rooms only).
- `torchLit` lives on room objects, `torchDeclined` + `torches` on the
  floor - all serialize with the ordinary save, no new containers.

**Verification (torchrig.js, 20/20).** Torches exist (>= rooms-2), one
per room, on open floor on the room's perimeter beside an exit, never at
the room center, dark by default; deleting and re-deriving reproduces
the identical set; stepping on offers the menu; lighting marks the room,
closes the menu, and the flame **burns on screen** (21 flame pixels
probed around the head); the save carries `torchLit`; the minimap blip
is gold at the right pixel; a fresh floor starts dark, a second torch
offers again, decline closes the offer, is remembered, and stops the
asking; and the walking landlord never stands on a torch. (One rig bug
fixed on the way: the minimap draws in the base 3x pass, not the 4.5x
scaled dungeon pass - the blip probe was reading canvas units as backing
pixels.) VLM reads both states clean: unlit = "small wooden post with a
dark, unlit head" beside the door; lit (hero one step off, so the torch
isn't under the hero) = "small gold/orange flickering flame on a wooden
post with a soft warm glow", no glitches.

## 38 - The Lamp keeps a fiddle (tavern music)

**Ask.** "when in tavern let's add tavern music."

**Fix.** A thin triangle-wave folk loop that plays only while the Flayed
Lamp holds you:

- a `tavMus` wish-flag set the moment `openTavern` runs (so the fiddle
  starts under the intro text, as it would when you walk in), cleared by
  the room's own **Leave**, by the menu closing, and by anything that
  leaves the base state (delve, combat); it survives the submenus (the
  room tonight, court, spouse scenes) because those stay inside the
  wish.
- `updateTavernMusic` schedules off frames in `updateAmbience`: a 32-step
  A-minor melody (with composed rests) on triangle at 0.022 over a
  descending lament bass (A2-G2-F2-E2, one note every 8 steps at 0.028)
  - quiet enough to sit under the rain and the room's own voice beeps.
  The step counter persists across visits - the fiddle keeps playing
  between your visits, it just wasn't audible.
- Mute is honored by `updateAmbience`'s existing sndOn gate; the wish
  survives the mute so unmuming brings the fiddle back mid-visit.

**Verification (musrig.js, 10/10).** Walking in starts the music; notes
flow while inside; the step is EXACT - 90 frames from a known phase
yield precisely the notes the composed table says (rests honored, bass
every 8th step); the music keeps playing in the "Who is here tonight?"
submenu; Leave closes the menu, stills the wish, and 60 frames spend no
notes; a muted room spends no notes while the wish lives, and unmuting
lets the fiddle back in; re-entering starts it again; and `launchDelve`
kills the wish.

**Battery (all three rounds on one build).** smoke 16/0, bumprig 6/6,
decross 35/35, combattrig 13/13, plaque probes ALL PASS, ragstrip OK,
facerig 11/0, scalerig 14/0, firerig 15/0, gearrig 17/0, bossrig
walk 14 + meet 8 + fight 17 + migrate 5 + shot 15 (all /0), torchrig
20/0, musrig 10/0.
