# OPENINGS — Delvings of Old Vharn (draft)

A gritty, pixel-art fantasy life-and-delve game in a single self-contained
`index.html` — no dependencies, no build step. Open the file in any modern
browser to play.

Dead hazards, dead spouses' lamps still lit, and no time limits. Vharn is
patient. You shouldn't be.

## Running it

Open `index.html` in a browser (double-click, or `python3 -m http.server` and
visit `http://localhost:8000`). Everything renders to a 320x180 canvas scaled
3x, `image-rendering: pixelated`, all art drawn procedurally in code.

## Controls

| Key | Action |
| --- | --- |
| Arrows / WASD | Move |
| Z / Enter | Confirm, talk, interact, use stairs |
| X / Esc | Back / cancel |
| M | Dungeon map (when delving) |
| F5 | Quicksave (auto-saves in city and on camps too) |
| Enter | Confirm on title / death screens |

### Gamepad

Plug in a controller (Bluetooth or USB-OTG on Android work): stick or D-pad to
move (with hold-to-repeat), **A** = Z, **B** = X, **START** = Enter, **SELECT**
= Esc, **Y** = map, **X (square)** = quicksave.

### Phone / touch

Open `index.html` in a mobile browser — on touch devices a virtual pad appears
under the canvas: D-pad (hold to walk), big **Z**, **X**, **⏎**, **MAP**,
**SAV** quicksave, and **FS** for fullscreen (recommended, landscape). The
canvas scales to the screen; saves live in browser localStorage, so keep using
the same browser to continue a run.

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

4. **Combat.** Turn-based: Strike, Cut Deep (risky), Guard, Bandage/Ration,
   Flee (AGI check). Damage from weapon dice + STR; wounds are applied when
   you're badly hurt and each one penalizes every skill check until a
   flesh-setter (or a devoted spouse) deals with it.

5. **Family.** Court one of six NPCs (affection from talk, gifts — the
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

6. **Death & the blood line.** Death is permanent — but a child of 14 or
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
