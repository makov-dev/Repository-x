# OPENINGS — Delvings of Old Vharn (draft)

A gritty, pixel-art fantasy life-and-delve game in a single self-contained
`index.html` — no dependencies, no build step. Open the file in any modern
browser to play.

Dead hazards, dead spouses' lamps still lit, and no time limits. Vharn is
patient. You shouldn't be.

## Running it

Open `index.html` in a browser (double-click, or `python3 -m http.server` and
visit `http://localhost:8000`). Everything renders to a 320x180 canvas scaled
3x, `image-rendering: pixelated`, all art drawn procedurally in code. On touch
devices the game auto-enters fullscreen when you begin (the **FS** chip in the
top-right corner toggles it manually). City buildings are drawn as pixel-art
façades: lit windows, doors, striped awnings, guild banners, the tavern's
swinging lantern, chimney smoke, and the arched Gate of Openings.

## Controls — mouse or touch, no overlays

The whole game is played **directly on the screen** — nothing else matters up
your view. No virtual pad; the canvas is the only control surface.

- Walking rich (70g+ on you, or 80g+ in loot) can attract **muggers of
  your own trade**: pay a tithe, show steel, run, or fight — street
  fights end back in the street.
- **Click / tap the map** → the hero walks there (pathfinding handles walls;
  in the city, tapping a building walks you to it and takes you inside).
- **Click / tap dialog text** → advance it.
- **Click / tap a menu option** → pick it (**tap / X back** corner = cancel).
- **Click / tap a combat action** (Strike / Guard / Flee…) → do it.
- In combat the rolling dice themselves are tappable.
- In a delve: on-screen chips **DESCEND / ASCEND / CLIMB OUT**, **MENU**,
  **MAP**; **SAV** quicksave and **FS** fullscreen live in the top-right corner.
- **SND** chip (top-right, next to SAV/FS) toggles all sound.
- Title and death screens: tap / click or **Enter**.
- **Right-click** = cancel/back (mouse).

Keyboard and gamepad still work for those who want them: arrows/WASD move,
Z/Enter confirm, X/Esc back, M map, F5 quicksave; gamepad A=Z, B=X,
START=Enter, SELECT=Esc, Y=map. Tapping Z/X or pressing a movement key cancels
an auto-walk in progress.

## The loop

1. **The city (your base).** Vharn's drizzly streets hold the facilities:
   - **Guild Hall** — rumors of what waits below (5g), the Wall of Names.
   - **Grave-Market** — sell your loot, buy rations/bandages/rope/antitoxin,
     and the ironmonger's weapons and armor. Money only comes from what
     you drag out of the Openings.
   - **Flesh-Setter** — pay to have wounds sewn shut.
   - **The Flayed Lamp** (tavern) — hot meals, rumors, and 6 NPCs
     you can court Princess-Maker-2-style: talk, gift, declare intent,
     marry (needs a Bone Ring, affection, and *a house*). None of them
     are here by choice: debts, notes held by the Kindly Fellows, a
     charge ordered at the bread riot, ash-field exile, one lamp still
     lit for a husband eleven months gone. Each tells her own story
     when you talk.
     **"Who is here tonight?"** shows the whole room: ages, who is wed
     to whom, who died.
   - **Housing Office** — buy the Leaning Tenement (250g), then upgrade it:
     leakproofing, warm hearth, nursery, deep cellar, reinforced door.
   - **Church of Cinders** — pray for free (sincerity optional; results
     vary by Will), light a candle for the dead (+2 HP), renew vows —
     and **pay for funerals**. When city-folk die the news says who
     wasn't buried; thirty gold at the Church closes their graves and
     heals a wound of your own, or hardens you (+1 Will).
   - **Training Yard** — pay sweat and stamina to grind STR/AGI/WIT/WLL.
   - **Gate of Openings** — choose which crack in the world to crawl into.

2. **The Openings (dungeons).** Three gates (danger tiers I–III), each with
   procedurally generated floors: random rooms and corridors, descending
   stairs forever (`>`), an exit back to the city (`<` at depth 1). Floors
   seeded per dive; deeper floors push the danger tier. No time limits —
   but stamina bleeds away every step, and at zero your HP follows.

3. **Encounters.** On first entering rooms, and at placed sites:
   - **Abominations** — 12 horrors across 3 tiers (Grinworm Clusters to
     The Long Mother). Every monster is drawn by a seeded body-horror
     generator: wobbling flesh silhouettes, random eyes/mouths/tendrils,
     bone crowns. Each looks different, every dive.
     Traits: `grasp` (holds you), `swarm` (many small bites), `weep`
     (drains a stat).
   - **Other adventurers** — some share rations and pay over the odds for
     loot; toll-gangs demand 30%; duelists offer winner-takes-the-kit.
   - **Obstacles** — rubble, grown spike floors, foul air, bottomless
     chasms (a Rope & Hook solves most things a stat check won't).
   - **Puzzles** — a door of fused vertebrae that riddles you, a three-lever
     panel with scratched hints, a basin that trades blood for barrows.
   - **Corpses of delvers** (loot them, or bury them — one of them stands up)
     and old shrines that ask nothing and offer plenty.

   Each Opening has its own light: the Cessgate swims sewer-green, the
   Ossuary breathes dry gold, the Cathedral stains everything violet —
   and each one **hums its own buried pitch** while you're inside.
   Bones, rubble, cracks and wet patches mark the floors; loot and
   shrines pulse faintly; dust drifts, water drips, your lamp flickers
   as you move. Robbery works both ways — walk the streets rich and
   other adventurers will look at you like arithmetic.

   **Greedy hands.** The dark owns a lender's patience. It plants
   **glints** in the rubble (Vharn-purple, tempting) that are honest
   double-loot more often than not — and *waiting* the rest of the
   time. It leaves **whimpers**: starved delvers who beg for a ration
   you can spare or can't. Feed one and half the Lamp might raise a
   cup to you; keep walking and the sound follows exactly one corridor.
   Finish it humanely, and it costs exactly one Will. And at the
   stairs, if rooms are still unemptied, the dark leans close and
   recites the gambler's whole liturgy: *one more room, one more
   room.* Below the fifth depth, everything pays time-and-a-half —
   the dark wants you deep. Greed is a stat here; it just reads as
   stamina.

4. **Combat.** Turn-based: Strike, Cut Deep (risky), Guard, Bandage/Ration,
   Flee (AGI check). Damage from weapon dice + STR; wounds are applied when
   you're badly hurt and each one penalizes every skill check until a
   flesh-setter (or a devoted spouse) deals with it.

   **The dice.** Attacks, Cut Deep and Flee roll two live d6 on screen —
   they spin at different speeds; catch each one with a tap (or Z / X).
   **Twin sixes = crit** — double damage, and Cut Deep pierces entirely.
   Two ones fumble. The same dice roll when the enemy brings it to you:
   catch both and the blow meets the space where you stood; one crowned
   die still shaves damage off. Guard and the flask don't gamble — they
   just work. Below a quarter of your HP the **Last Stand** kicks in and
   every die crawls, slow enough to catch.

   **Sound & fury.** The game hums with procedural square-wave grit:
   dice tick while they spin, a **chime when you catch a six**, a sting
   for crits, thuds for hits, blood-flash on wounds, coin-jingle on
   payout, a heartbeat during the Last Stand. Crits throw gold sparks
   and **punch the screen** — the whole world lurches and swells around
   the blow. Every flash, shake, and shout can be silenced with the
   SND chip.

   **The openings have voices too.** Each gate sounds like itself: the
   Cessgate drones a low sewer-note at 55Hz, the Ossuary hums a dry
   tone that belongs to old bone, the Cathedral under the roots
   breathes a near-subsonic cathedral pitch — detuned saws and sines,
   wobbling slowly through a lowpass, fading in as you arrive and away
   when you climb out. Back in Vharn the street-ambience is a soft
   looped rain hiss under the lamp haze. New things in the dark get a
   **cinematic entrance**: screen darkens, the thing drawn large and
   twice its fight size, its lore written across the dark — *- SOMETHING
   NEW -* — until you tap to steel yourself. Repeat meetings skip it.

   People have small music as well: every named NPC has a three-note
   voice motif hashed out of their name, so Sera always sounds a little
   different from Mila when they speak. Sell loot at the market and
   the broker's coins **cascade** out one by one — worthier hauls ring
   longer, relics spark out violet with their banner. Training arcs
   bright when a stat rises and lands flat and sour when the week
   buys nothing. Weddings toll the Cinders bell three times; funerals
   twice; and when you die, three long tolls walk you under.

   **After the fight:** killed foes are carved into your bestiary — first
   meetings show only a description and "vitals unknown"; survive a kill
   and you know the thing forever (or learn Deep Reading at the Yard).
   Consecutive crit-kills build a streak that pays bonus gold. Loot is
   graded COMMON / PRIZED / RARE / RELIC the moment it leaves the dark,
   and relics earn a banner.

5. **Reputation.** The city names what you do. Seven kills of one kind of
   thing and you are its **Slayer**; marry and you are **Wed Under the
   Lamp**; a child makes you **Father**; coin makes you *the Gilded*,
   depth makes you *the Deep-Drowned*, three crit-kills in a row —
   **Sixtongue**, three tavern wins — **Spitefinger**. The Guild clerk
   greets you by your latest name; your titles ride your HUD and your
   gravestone.

6. **The Training Yard** drills stats, and — if the numbers are there —
   masters sell **maneuvers**: Riposte (dodge twin sixes, next Strike
   crits free), Gravedigger's Cleave (Cut Deep never fumbles), Ashen
   Stare (the weepers can't drink you), Deep Reading (vitals on sight),
   **Parry Eternal** (twin sixes on defense and the thing loses its
   next swing entirely, AGI 8), **Martyr's Calm** (once the Last Stand
   begins, the slow dice never speed up again, WLL 8) and **The
   Beheader's Hour** (twin sixes against a foe below a quarter of its
   vitals end it outright — a STR 9 character can behead things that
   out-stat them four to one).

   **Skill beats stat blocks.** Every caught six stacks one **EDGE**
   (max 3) — each point rides the steel as bonus damage, a perfect
   dodge stacks it, and taking a hit bleeds it all away. A careful
   gambler faces down horrors ten times their pay grade; a sloppy one
   dies rich and full of potential.

7. **The Flayed Lamp** serves food, rumor, marriage and now the
   **Bones Game**: catch twin sixes against the house and your stake
   triples. In combat, your last action stays in the menu as a one-tap
   **Repeat**.

8. **Family.** Court one of six NPCs (affection from talk, gifts — the
   true gifts are Bone Rings and Pale Roses), own a house, marry, install
   the nursery, try for a child, name is rolled from the leftovers of
   Vharn's naming customs. Children grow (spark grows when you teach them).
   Resting at home with spouse and hearth heals more.

   **Courtship is a race.** While you dawdle above ground or loot below
   it, weeks pass, and a courting partner can wed someone else — a roper,
   the ferryman, the one holding her debt-notes — and the news reaches
   you late, the way news does. Slow suitors lose. The Rose costs 60g and
   buys four times the speed.

   **The city keeps filling up.** Roughly once a year someone new drifts
   into Vharn with the carts: debtors, the hunted, people out of
   alternatives, people who will not say why. New faces keep the Lamp
   from emptying out — and give grown heirs somebody left to court.

   **NPC life simulation:** Vharn does not pause when you go underground.
   NPCs age week by week; ignore someone and the odds are they wed someone
   else and have children of their own; past ~55 they start dying of age.
   Births, weddings and funerals reach you as news from the surface — at
   the Lamp's door, when you climb out of a delve, and with the morning
   soup after a week's rest. Neglect is a choice, and the city keeps score.

9. **The hidden history.** Nobody in Vharn knows what the Openings are
   — the city stands on something that used to be divine, opened its
   own doors to heaven, found no heaven there, and died of it. The
   Church of Cinders preaches **fragments it does not understand**:
   bones of the first sinners, promised gardens, a parable about
   vigilance. The truth is found **only by delving**: kneel at old
   shrines, search dead men's packs, listen to the wrong stories told
   right in the Lamp, push to the fifth, seventh, and ninth depths,
   kill the great horrors, light the thirteenth candle for no one,
   pay for the city's graves. Each fragment discovered shows, side by
   side, *what Cinders sings* and *what the dark says* — at the Guild
   Hall or the Church. Nine fragments exist. The city will never say
   any of them aloud.

10. **The Threshold.** When the dark has told you enough — six truths,
    the three great horrors of the deep given their deaths, the ninth
    depth witnessed, five graves paid for, and a child left behind who
    will outlive you — the descending stairs stop being stairs. The
    DESCEND chip becomes **THE GATE**. What waits there is old and vast
    and *tired*, holding a door the way a jaw holds a scream, and its
    face does something nothing down here has ever done: it is
    **relieved**. Take the duty and you become **the Gatekeeper** — a
    god of the small local kind: the Gate gives you the last truths,
    raises body and soul, and thereafter *refuses* to let you die.
    Refuse the duty and you climb back into a city you now understand,
    carrying the relief of an abandoned god for the rest of your short
    life.

11. **Death & the blood line.** Death is permanent — but a child of 14 or
   more *continues*: the save stands, you take over the heir with half your
   old body's leanings plus their taught "spark" for stats, the house and
   its upgrades, your good weapons, and 15g (inheritance is taxed). Dead
   too young to leave a grown heir? The save is wiped. A widowed spouse is
   remembered in the world — and courting your own parent is, correctly,
   impossible.

## Systems summary

- Stats: STR/AGI/WIT/WLL; checks are d10 + stat − wounds vs difficulty.
- Stamina economy gates everything: delving, training, burial.
- Aging: no time limits, but years pass as you train, delve, and rest;
  max HP declines past 40. Vharn keeps the receipts.
- Inventory is slot-capped; the Deep Cellar upgrade adds 8 slots and lets
  a spouse trickle in salvage coin.
- Saves: localStorage (`openings_save`), wiped on death *unless a grown
  heir continues the line*; F5/F9 manual. Dungeon fog/visited data is
  serialized as sets and revived on load.

## Status: draft

Deliberately skeleton-clean: one city map, three openings, menu-driven
facilities, one combat screen. Obvious next steps: interior scenes for
facilities, more openings/enemies/loot, torch light radius as a resource,
deep-city factions, the adult-child inheritance as a playable continuation.
