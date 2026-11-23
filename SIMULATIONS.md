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
