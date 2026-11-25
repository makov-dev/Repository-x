# OPENINGS — Delvings of Old Jericho (draft)

A gritty, pixel-art fantasy life-and-delve game in a single self-contained
`index.html` — no dependencies, no build step. Open the file in any modern
browser to play.

Dead hazards, dead spouses' lamps still lit, and no time limits. Jericho is
patient. You shouldn't be.

## Running it

Open `index.html` in a browser (double-click, or `python3 -m http.server` and
visit `http://localhost:8000`). Everything renders to a 320x180 grid on a
960x540 canvas (3x internal resolution, `image-rendering: pixelated`):
the pixel-art stays deliberately chunky while every glyph of text is
rasterized sharp at 27px, all art drawn procedurally in code — the
camera rides close to the hero (1.5× zoom, roughly 27×15 tiles on screen) and
darkness gathers in the screen corners, thinning toward the middle. On touch
devices the game auto-enters fullscreen when you begin (the **FS** chip in the
top-right corner toggles it manually). City buildings are drawn as pixel-art
façades: lit windows, doors, striped awnings, guild banners, the tavern's
swinging lantern, chimney smoke, and the arched Gate of Openings. It keeps its
own **shadow and light**: every building drops a soft shadow, **lampposts**
stand at the road's edge - dark iron posts with a small glass lantern hanging
under a crossarm, its flame flickering — and their light pool visibly brightens
the street tiles around them, pushing the dark back in a warm radius; windows
and dungeon torches glow
additively onto the stones, and the hero, the townfolk, monsters and even
corpses each cast a foot-shadow on the ground. The delve is lit to be read:
a lantern-glow follows you, seen floors stay visible, stairs down blaze with a
pulsing gold rim and a **▼ compass** in the HUD points to the nearest one,
and each opening's Gate arch burns with its own light. Each opening **hums its
own drone**, and the drone sinks lower and darker the deeper you go. **Depth changes the
air** — deeper floors shift from cold blue-violet through bruised purple to a
deep red mine-gloom. In a fight **the music is your heartbeat**: a slow calm
lub-dub when you are whole, pounding faster with every wound and **skipping a beat whenever steel or claw
actually finds you** — and after a kill **it settles in stages**: the beats
fall further and further apart, each one quieter, an audible unwinding until
silence takes the room back. At your death it stops dead the instant you do.
You can see the heart too: **with every beat the edges of the world pulse a
thin blood-dark**, subtler at full health, tightening as you bleed.
In the quiet between fights **greed gets a heartbeat of its own**: a quicker,
thinner, higher echo that rises in your ears as you close on treasure you can
see — and the first time it ever happens, the game tells you so: **your ears
start to ring. Something worth having is close.** Learn the sound once and it
is yours forever, the way a gambler knows a coin's weight by ear; the treasure
answers by **glinting** — a small light snapping off it in time with the ring.
And the dark can be heard before it is found: **near an undiscovered
secret wall the drone muffles and thickens**, like walking into wool, and
clears the moment the hidden thing gives itself up. And every stair down
costs the world one breath: **the whole drone falls quiet as you step
through, then rises again behind you** — the way down announced in stone
instead of words.
And Jericho
breathes: pigeons flap along
the rooftops, a steady drizzle crosshatches the streets and the rain checks
the rooftops, fog rolls off the avenue, and the whole thing hums. Something
can creak, too: each floor hides a thing that never announces itself — the
closer you stray, the more the walls grumble about it.

Rooms keep **screws loose in the world**: now and then a floor holds a
**sealed stair** (break it open on Strength, or rig it with Rope & Hook and
drop two levels in one step) or a **door with no wall around it** — a way
into hidden Openings you can force open if your Will holds, or chalk and
wake up later. Chalked doors appear at the Gate of Openings, forever after:
**The Maw Behind Plaster**, **The Debt Vaults**, and **The Ninth Seam**.

The streets are **inhabited**: named folk with a trade and a family line walk
purposefully from one door to another at an honest walking pace and slip
inside. The streets are strewn with **trash** - bottles, rags, crates, bones,
blown leaves in the gutters - and **cooking fires** burn at the doors of the
Lamp, the Grave-Market, the Guild Hall, the Washhouse and the Tallowworks
(a pot steaming over the tavern's). Walkers sometimes divert to a fire instead
of an errand: they stand by it, idle, warm their hands, and only then move
on - or wander off entirely. Tap one to get a look —
name, age, trade, and what they think of you. At first you are furniture with
pockets; kept at it, they glance, nod, eventually say "Morning, [your name]",
and in time greet you by title. They **age**: elderly townfolk can die
mid-errand, the news spreads, and the Church of the Holy Gate will bury them.

The city itself is **alive around you**: the folk come from somewhere and go
somewhere. **The Stacks** (four grim tenements) house them, and the
**Washhouse, Bone Carver, Tallowworks and Pawnbroker** employ them — each
walker is assigned a home and a bench, and spends the day commuting between
them, disappearing inside for a while, then emerging on the opposite errand.
Tenements hang their laundry in the drizzle; workshops show a painted trade
sign. Walk up to any of them and go in: you'll be told what the place smells
like and exactly who lives or labours there today.

## Controls — arrows/WASD to walk, hold-your-finger steering, no overlays

The whole game is played **directly on the screen** — no virtual pad, nothing
covering your view; the canvas is the only control surface.

- **Keyboard (primary): hold to walk.** Arrows or WASD — hold a direction and
  the hero keeps walking, steady pace, one tile at a time; tap for a single
  step. Held arrows/WASD also scroll menus (and they repeat cleanly, no
  drift). **Z/Enter** confirm, **X/Esc** back, **M** map, **F5** quicksave.
- **Touch (primary): hold a finger, you walk toward it.** Press anywhere on
  the map and the hero walks in the direction your finger sits relative to
  him — keep the finger right of him and he keeps going east; drag it above
  him and he turns north; bring it onto him and he stands. A faint gold ring
  marks your finger while you steer. Tap-to-walk still exists: tap a tile and
  he paths there, tap a building to be walked to its door and inside, tap
  the up/down stairs and he walks there and **climbs/descends** for you, tap
  a neighbour and he walks over and **they talk** — no Z needed for any of it.
- Walking rich (70g+ on you, or 80g+ in loot) can attract **muggers of
  your own trade**: pay a tithe, show steel, run, or fight — street
  fights end back in the street.

Every label and description is fit to its box: long option text shrinks
instead of being cut mid-word, menu descriptions wrap onto a second line,
and hints wrap rather than truncate — nothing you are offered goes unread.

- Walking rich (70g+ on you, or 80g+ in loot) can attract **muggers of
  your own trade**: pay a tithe, show steel, run, or fight — street
  fights end back in the street.
- Running out of coin mid-purchase does more than embarrass you: somewhere
  outside, **a loan shark** has already noticed. His arithmetic is simple
  (100 in, 300 out, collected after your next delve).
- **Click / tap the map** → the hero walks there (pathfinding handles walls;
  in the city, tapping a building walks you to it and takes you inside).
- **Click / tap dialog text** → advance it.
- **Click / tap a menu option** → pick it (**tap / X back** corner = cancel).
- **Click / tap a combat action** (Strike / Guard / Flee…) → do it.
- In combat the rolling dice themselves are tappable.
- **BAG** chip (top-right) opens your pack anywhere: wield/weapons, wear/armor,
  eat food, drop items, or just read what everything is. Bought weapons and
  armor go into your pack and auto-equip; the old ones drop in beside them.
  The carry limit is hard: when a find won't fit, the game asks what to
  **drop for it** — nothing vanishes without your say-so.
- **Options you can't take don't exist**: no rope, no "use rope"; no antitoxin,
  no "drink antitoxin" — menus only ever offer what is actually on the table.
- In a delve: on-screen chips **DESCEND / ASCEND / CLIMB OUT**, **MENU**,
  **MAP**; **SAV** quicksave and **FS** fullscreen live in the top-right corner.
- **SND** chip (top-right, next to SAV/FS) toggles all sound.
- Title and death screens: tap / click or **Enter**.
- **Right-click** = cancel/back (mouse).

Gamepad still works for those who want it: d-pad/left stick walks (held =
keeps walking), A=Z, B=X, START=Enter, SELECT=Esc, Y=map. Tapping Z/X,
pressing any movement key, or steering the finger cancels an auto-walk in
progress.

## The loop

1. **The city (your base).** Jericho's drizzly streets hold the facilities:
   - **Guild Hall** — rumors of what waits below (5g), the Wall of Names.
   - **Grave-Market** — sell your loot, buy rations/bandages/rope/antitoxin,
     and the ironmonger's rack: Surgeon's Scalpel, Iron Shard, Crude Spear,
     Bone Saw, Balanced Hand Axe, Wardriver Maul (dmg 3→8), plus Widow's
     Mail and Yes-Plate. Money only comes from what you drag out of the
     Openings.
   - **Flesh-Setter** — pay to have wounds sewn shut.
   - **The Flayed Lamp** (tavern) — hot meals, rumors, and 6 NPCs
     you can court life-sim style: talk, gift, declare intent,
     marry (needs a Bone Ring, affection, and *a house*). None of them
     are here by choice: debts, notes held by the Kindly Fellows, a
     charge ordered at the bread riot, ash-field exile, one lamp still
     lit for a husband eleven months gone. Each tells her own story
     when you talk.
     **"Who is here tonight? (court here)"** shows the whole room: ages,
     who is wed to whom, who died — and picking anyone tells you their
     story and, if they are unattached, lets you **Begin courting** them
     on the spot.
   - **Housing Office** — buy the Leaning Tenement (500g), then upgrade it:
     leakproofing, warm hearth, nursery, reinforced door — and then
     **build rooms**. Each room costs **500 + everything before it** (first
     room 500g, then 1000g, 1500g…), and each is built and walks the
     floor as furniture: the **Kitchen** fattens the pantry (+60, once
     per delve), the **Bedchamber** teaches sleep to hurry (3 days
     instead of 7), the **Washroom** draws one bath per delve that
     sweats out a sickness and sends you out scrubbed — **prettiness
     counts, in courtship** (+2 affection while the rosewater lasts).
     The **Gymnasium** (Strength or Agility) and the **Study** (Wits)
     and the **Corner Shrine** (Will) each sell a chance at a stat for
     50 stamina; the house keeps its own hours and does not always pay.
     The **Cellar** doubles storage and puts crates and barrels
     downstairs — and **one of those barrels was never in any
     inventory**. Heave the right one aside and a stair opens to the
     **Barrow Cellars**, an undead opening under your own house, chalked
     into the Gate's map from then on.
     **Your house is its own small map already**: step through the door
     and you are standing in a crooked room with a **bed** and a
     **small chest** under the floorboards, a pantry shelf, a lamp-stand,
     and corners that stay swept until you build the reasons to fill
     them. Walk to the bed and press to sleep (three days or seven);
     the chest opens storage (36 slots with the Cellar) — the place
     where a career, not a shopping trip, gets parked between delves.
   - **Grave-Market: packs.** Carry **10** items; buy a **Satchel (35g,
     +3)** or **Expedition pack (90g, +6)** up to 19. Items stack, slots
     don't; the Cellar room widens the chest instead.
   - **The Kindly Fellows.** Not on any street you'll find them on; they find you. Go to buy something and come up short, and outside the door waits a patient man: **100 gold now, 300 when you climb back out** of the Openings - collection day is the day you surface. He can be refused; he keeps the face. When the thugs wait by the Gate, you pay the 300, or **talk your way into paying later (Wits)** - succeed and you get exactly one more dive; fail and the fee for waiting is a **beatdown for 10% of your max HP and 20% of your purse**, and they ask again, right there, and every delve after, until the book closes. A frail body can genuinely die of debt. The debt rides your HUD until it is paid.
   - **Honest work, if you want it badly enough.** The Washhouse, the
     Bone Carver, the Tallowworks and the Pawnbroker will take a shift
     off you - two weeks and 35 stamina (more than the Yard ever asks)
     for a wage counted in single coins (2-6g), with maybe a one-in-
     seven chance the work itself teaches your body something (Agility
     at the wringing, Wits on the appraisal bench, Will in the vats,
     Strength portering the Market's freight). Honest work pays worse
     than the dark and teaches slower than the Yard. But it is sure,
     and sure is never cheap. Too tired to hold a mop? The option does
     not exist that day.
   - **Church of the Holy Gate** — pray for free (sincerity optional; results
     vary by Will), light a candle for the dead (+2 HP), renew vows —
     and **pay for funerals**. When city-folk die the news says who
     wasn't buried; thirty gold at the Church closes their graves and
     heals a wound of your own, or hardens you (+1 Will).
   - **The Salt Bunks** — the houseless sleep here, and the quote is honest:
     **a chair by the stove (5g, +40 stamina)**, or **a salt-crusted bunk
     (12g, stamina back to full and +6 HP)**. Borax in the ticking; no names
     asked at the door. Deck-hand Sal takes the coin and you owe nobody come
     morning. Come up short and you will meet the patient man instead.
   - **Training Yard** — pay sweat and stamina to grind STR/AGI/WIT/WLL.
   - **Gate of Openings** — choose which crack in the world to crawl into.

   **Every place is somebody's job.** Eleven facilities have a keeper
   who is always there: Master Pell the guild clerk, Weigh-mistress
   Corrin, Setter Mala, Barkeep Odo, Clerk Ivvan, the Sexton, Yard-
   master Grett, Laundress Suki, Carver Nol, Old Wick, Broker Feen and
   Deck-hand Sal.
   They know you the way the street does — first visits you are furniture,
   then a nod, eventually your name, and the day comes when "***your
   title*** first," is the whole conversation. Regulars get trusted:
   from the sixth visit on, a keeper may share what their counter hears
   — and a counter in Jericho hears the kingdom's business. (The clinic
   hears nothing but screaming; Mola keeps none of it.)

2. **The Openings (dungeons).** Three gates (danger tiers I–III), each with
   procedurally generated floors: random rooms and corridors, descending
   stairs forever (`>`), an exit back to the city (`<` at depth 1). Floors
   seeded per dive; deeper floors push the danger tier. No time limits —
   but stamina bleeds away every step, and at zero your HP follows. Below
   zero the body starts **lending you meat**: it collects as you go - an
   **injury or an illness for every stretch of the loan** (both spend your
   checks; the flesh-setter will sweat a fever out for 10g, a home rest
   breaks them, the HUD goes red and lists you as *unwell*). **-25 is the
   end of the body's credit**: past that line **every further step is a
   death roll** at the body's table. No time limits - the body keeps
   count instead, and rations and hot meals climb you back out of debt.
   For people with reasons, the flesh-setter now bottles sleep itself:
   **Traveller's Bloodrest**, 35g, wholeness in one swallow — full HP,
   drunk in the dark, even mid-fight, when you have no business
   surviving but intend to anyway.

3. **Encounters.** On first entering rooms, and at placed sites:
   - **Abominations** — 12 horrors across 3 tiers (Grinworm Clusters to
     The Long Mother). Every monster is drawn by a seeded body-horror
     generator: wobbling flesh silhouettes, random eyes/mouths/tendrils,
     bone crowns. Each looks different, every dive.
     Traits: `grasp` (holds you), `swarm` (many small bites), `weep`
     (drains a stat).
   - **Other adventurers** — some share rations and pay over the odds for
     loot; toll-gangs demand 30%; duelists offer winner-takes-the-kit.
   - **Obstacles** — rubble and foul air gather in the rooms; the corridors
     hold the traps: grown spike floors, bottomless chasms, and thin seams
     of pale dust hiding weighed plates (read them with Wits, or a Rope &
     Hook solves most things a stat check won't). Traps are only ever laid
     on **single-file corridor chokepoints** — where the generator can prove
     sealing the tile cuts the tunnel in two, it prefers those crossings — so
     the check is a toll you pay to move, not a decoration. And a chasm is
     also a door: **climb down into it** (Rope & Hook) or drop (Agility) and
     you reach the floor below — depth is the gain, the fall is the price.
   - **Traps stay armed until you truly deal with them.** Back off and the
     seam is behind you, not spent; come back and it wakes again — a failed
     attempt buys blood, not passage. The spike beds and the plates are
     *mechanisms*, and mechanisms keep their maintenance: **hunt for the
     release lever (Wits)**, throw it, and that crossing is yours forever —
     the teeth sink into the floor for good, and the defused tile draws
     plain stone instead of its glyph. Rope & Hook and Antitoxin spend just
     as permanently, and a clean stat roll opens the way outright. The chasm
     alone never closes: it is a hole in the world, not a gadget.
   - **Thresholds.** Where a room opens into a tunnel, the doorway is
     drawn as a pale worn-steel line, distinct from the gold room outline,
     so the way on is always legible.
   - **The floor's landlord.** Every floor has its own **miniboss** keeping
     one of the rooms, marked by a violet aura you can see before you step
     in. Some of them (the Gnawing Chancel, the Sow of Small Deaths, the
     Hunger With a Clock in It) are simply **bloodthirsty beasts** — strong,
     honest, and about as negotiable as gravity. But the deep also grows
     **persons**, and their minds work along different walls: to the Marrow
     Auditor, death is just an audit that finally balances; to the Drowsing
     Host, sleep is wealth and you are rich beyond blame; the Cartographer
     of Veins is certain humans are herd animals that come down here to
     scab; the Lamp-Bearer worships your lantern outright. None of them
     is helpful on sight: each **respects a different thing before it
     will receive you at all** (the Auditor hears accounted coin; the
     Cartographer those whose passing has stained its world in four
     places; the Drowsing Host only the nearly-stopped; the Lamp-Bearer
     an offering set beside the flame). Push an unearned conversation
     and the odds are the meeting becomes a bout. Once received, a Wits
     or Will check opens their world a crack, and a success can hand
     you a **truth fragment** the surface churches have never had.
     They **trade** (wet ledgers, hide-maps, bells and rations all have
     buyers below), assign **errands** (deliver a relic, or put down two
     small deaths and tell your host how it sounded), and if you honor an
     errand you gain a **pact**: your names go in their book, and every
     landlord of that lineage will meet you as a friend instead of a fight,
     forever. Fail a conversation with an unforgiving one and the audience
     becomes a bout.
   - **Corpses of delvers** (loot them, or bury them — one of them stands up)
     and old shrines that ask nothing and offer plenty.

   Each Opening has its own light: the Cessgate swims sewer-green, the
   Ossuary breathes dry gold, the Cathedral stains everything violet —
   and each one **hums its own buried pitch** while you're inside.
   Bones, rubble, cracks and wet patches mark the floors; loot and
   shrines pulse faintly, and glint harder the closer you stand when
   your ears are already ringing; dust drifts, water drips, your lamp flickers
   as you move. Robbery works both ways — walk the streets rich and
   other adventurers will look at you like arithmetic.

   **Greedy hands.** The dark owns a lender's patience. It plants
   **glints** in the rubble (Jericho-purple, tempting) that are honest
   double-loot more often than not — and *waiting* the rest of the
   time. It leaves **whimpers**: starved delvers who beg for a ration
   you can spare or can't. Feed one and half the Lamp might raise a
   cup to you; keep walking and the sound follows exactly one corridor.
   Finish it humanely, and it costs exactly one Will. And at the
   stairs, if rooms are still unemptied, the dark leans close and
   recites the gambler's whole liturgy: *one more room, one more
   room* — and if you refuse, it lets you out while the out is still
   open: you climb back to Jericho right then and there. Depth is the
   money: everything the dark gives is worth more the further down it
   gave it (+15% value and roughly a pile more per three floors), the
   kill-drops included. Greed is a stat here; it just reads as stamina.
   And the deep keeps its aristocracy: the three great horrors — the
   Hundred-Handed Host, the Wound-Warden of the Deep Ward, the Long
   Mother — are never met above the fourth depth, and below it they come
   looking for you more and more frankly the deeper you climb. Kill
   them there, or don't write home.

4. **Combat.** Turn-based: Strike, Cut Deep (risky), Guard, Bandage/Ration,
   Flee (AGI check). Damage from weapon dice + STR; wounds are applied when
   you're badly hurt and each one penalizes every skill check until a
   flesh-setter (or a devoted spouse) deals with it. Kills land
   hard: the room dims, the world holds a beat — **IT STOPS.** — and
   crit kills come apart in gold.

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
   payout, and **your own heartbeat leading every fight** (a lub-dub layered
   into octaves, so even a phone speaker hears it) — then, when the fight is
   won, the deceleration is the sound of standing down: each settling beat
   slower and softer than the last. Crits throw gold sparks
   and **punch the screen** — the whole world lurches and swells around
   the blow. Every flash, shake, and shout can be silenced with the
   SND chip.

   **Combat has a tempo.** The moment a fight starts, a fast square-bass
   ostinato kicks in with sawtooth stabs climbing a four-note run — and
   when you drop below a quarter of your vitals it doubles speed and
   drops an octave, because your own blood now has a drum section.

   **The openings have voices too.** Each gate sounds like itself: the
   Cessgate drones a low sewer-note at 55Hz, the Ossuary hums a dry
   tone that belongs to old bone, the Cathedral under the roots
   breathes a near-subsonic cathedral pitch — detuned saws and sines,
   wobbling slowly through a lowpass, fading in as you arrive and away
   when you climb out. Back in Jericho the street-ambience is a soft
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
   buys nothing. Weddings toll the Holy Gate bell three times; funerals
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
   Jericho's naming customs. Children grow (spark grows when you teach them).
   Resting at home with spouse and hearth heals more.

   **Courtship is a race.** While you dawdle above ground or loot below
   it, weeks pass, and a courting partner can wed someone else — a roper,
   the ferryman, the one holding her debt-notes — and the news reaches
   you late, the way news does. Slow suitors lose. The Rose costs 60g and
   buys four times the speed.

   **The city keeps filling up.** Roughly once a year someone new drifts
   into Jericho with the carts: debtors, the hunted, people out of
   alternatives, people who will not say why. New faces keep the Lamp
   from emptying out — and give grown heirs somebody left to court.

   **NPC life simulation:** Jericho does not pause when you go underground.
   NPCs age week by week; ignore someone and the odds are they wed someone
   else and have children of their own; past ~55 they start dying of age.
   Births, weddings and funerals reach you as news from the surface — at
   the Lamp's door, when you climb out of a delve, and with the morning
   soup after a week's rest. Neglect is a choice, and the city keeps score.

9. **The hidden history.** Nobody in Jericho knows what the Openings are
   — the city stands on something that used to be divine, opened its
   own doors to heaven, found no heaven there, and died of it. The
   Church of the Holy Gate preaches **fragments it does not understand**:
   bones of the first sinners, promised gardens, a parable about
   vigilance. The truth is found **only by delving**: kneel at old
   shrines, search dead men's packs, listen to the wrong stories told
   right in the Lamp, push to the fifth, seventh, and ninth depths,
   kill the great horrors, light the thirteenth candle for no one,
   pay for the city's graves. Each fragment discovered shows, side by
   side, *what the Holy Gate sings* and *what the dark says* — at the Guild
   Hall or the Church. Nine fragments exist. The city will never say
   any of them aloud.

   **The crown arc — fifty years on a clock that does not care who you
   are.** The kingdom's business is anchored to the campaign's first
   week, exactly so that heirs inherit it in progress. **Year 5:** a
   stranger begins attending the old king — physician, say the criers;
   and the king stops dying. **Year 9:** the king weds the stranger in a
   closed ceremony; the archbishop signs, then retires the same week.
   **Year 18:** the great houses stop saying the king's name aloud — they
   say *after* — and buy rope, armor and divers' contracts; the polite
   strangers in the Lamp stop asking what cures age and start asking
   where the stairs open. **Year 25:** the king dies ("the treatments
   worked the whole way down"); the consort signs as regent. **Year 33:**
   soldiers in crown-issue mail quarter in the guild hall — the first
   garrison in Jericho's living memory — and a corporal posts notice at
   the Gate of Openings that dives need a **crown licence** now. That is
   when patrol roped-off squares, deep-duty levies stamped onto your
   collar, and market manifests marked *evidence* start bleeding into
   street life. **Year 45:** the regent dies — a horrible accident, the
   criers say, right on coronation day of the crowned child, who now
   speaks in the regent's cadence and holds a pen the way she did. The
   houses keep their doors barred.

   Below, the pressure has teeth: during the legion era the dark gives up
   **dead soldiers** (their wounds are not shaped like anything from the
   surface, and their orders are worth reading) and patrols of **Crown
   Commandos** stop you in the tunnels for your licence, your manifest,
   and your purse. Kill enough of them — eight is the arithmetic nobody
   in Jericho could have predicted — and word crawls up the stairs: the
   banners come down along the quays, the decree says *redeployment*,
   and Jericho toasts the withdrawal the way you toast weather. No
   speeches. The dark does not sign contracts.

   **The coup — the kingdom eats itself.** Around **year 120**, or just
   eight years after the military waned (whichever comes first), the
   great houses move: the crowned one disappears — not dead, the houses
   insist, merely *transitional* — the crown is never found, and a
   committee of houses rules while the quarrel goes to war. Jericho's
   one road now runs mail shirts out and **deserters** in: newcomers
   with scrubbed sleeves, no colors, and a professional interest in
   stairs nobody checks their war record on. The tavern's long table
   changes accents; the population of the wards changes trade.

   **The figure in the square.** The day of the coup, everyone in
   Jericho behaves exactly the same except one: a **grey figure** in
   court boots stands in the town square and does not leave. It is the
   old king's adviser — the person who spent a kingdom, a war and a
   garrison buying one-way passage below, and was refused: the Gate
   kept the payment and did not keep the buyer. They stop you in the
   street, politely, often, and ask about your venture — the deepest
   room, the price of things, what the stone was pretending to be —
   always checking your sums against a ledger only they can read. And
   when you are genuinely near the Gate — the ninth depth witnessed,
   your bestiary nearly whole, the lore carried — the questions stop
   being questions: they offer a fortune, counted out in coin too new
   to be dirty, for your maps and your bestiary. Sign, and the run is
   over — a game over that is not an ending.

   **The dream loop.** Take the sale (or take the duty and pass the
   lamp) and a **new game+** begins: your successor wakes in Jericho at
   the very beginning, the whole past life just a dream — but with all
   the stats in place, and the bestiary remembered like a nightmare
   someone read aloud. Each loop the things below have *heard a keeper
   is coming*: they are stronger (HP and attack rise every tier) but
   better worth the trouble (loot value rises to match). When you
   become the **Gatekeeper** for real, you may keep living — or **pass
   the lamp**: a fresh successor line begins, Gate-Touched (the first
   death of every delve refuses them, once), and their endgame is
   different: at the ninth depth the Gate is *held* — by the previous
   character, changed. Taking it from them is the fight; after that the
   coronation stands. The loop closes on itself as long as there is
   anyone left ambitious enough to dream.

   **And beneath the truth, a second layer of wrong.** Jericho technically
   falls under the kingdom, and the kingdom's paper still finds its way
   in: thirteen scraps of decree, gossip and paperwork — no army ever
   comes to collect; the tax assessors' ledgers came back by cart while
   the assessors stayed below; every exile the kingdom wants forgotten
   is shipped in; one road in and no road out; the old, dying king's
   agents in their court accents, gently asking divers what cures age
   down there, paid from a mint nobody has seen, working from one line
   translated wrong (*HE WHO HOLDS THE GATE SHALL NOT DIE* — the line
   continues, unhelpfully, *HE SHALL NOT DIE EVER, WHICH IS DIFFERENT*).
   Rumors are found the way truths are: the sexton's pretended gossip,
   the polite strangers in the Lamp's corner, the clerk off the record,
   a dead man's court papers, the pawnbroker who has priced everything,
   and the placements in old stonework. The codex keeps both piles —
   *what the Holy Gate sings* against *what the dark says*, and below
   them, *what the kingdom says*, which is mostly mistold, self-serving,
   and — here is the cruelty of it — **almost all of it true**. None of
   it advances what the Gate asks of you. The Gate does not deal in paper.

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

    **The reckoning — the endings of the keeper.** A keeper keeps living,
    and the Gate of Openings gains two door-marks: **Keep the watch** (a
    season at the threshold; the duty pays its keeper in change — one
    measure +1) and **Descend — let the Gate look at what you have
    become**. What it sees, it answers in kind:

    - **All four measures grown past mortal counting** (Strength, Agility,
      Wits and Will each at 100): you are not overwhelmed, you do not go
      mad, your body does not fail — **you resist the call and close the
      Gate**. The city goes silent like held breath; the horrid things
      thin, sicken, and die out within the year. You remain as an
      immortal hermit, understanding you sacrificed the deepest human
      measure of all — what we interpret as soul, or being, perhaps
      existence; there is no human word for it. **This is the only time
      the game truly ends.** The cosmology remembers the closure: every
      life afterwards is lived in a city behind which lies a silence the
      old songs refuse to explain.
    - **Wits at 100 + Will at 100**: understood the incomprehensible
      patterns *and* answered the call on your own terms — you mutate,
      but stay a person: a **sentient Keeper** on the far side. Saves,
      heirs and future lives find you standing by the Gate of Openings,
      and they can **talk to you**: you will not want new divers to
      suffer the fate worse than death, and your word is worth one
      truth, given freely, once.
    - **Wits alone at 100**: you comprehended the alien logic — and
      understanding is a doorway the Gate walks through. You mutate and
      **submit**, taking your place among the keepers of the gate: not a
      person who holds the door, a feature of the doorway.
    - **Strength alone at 100**: the body keeps its shape; the eyes go
      the color of old coins in black water, the nails lattice into
      chitin — a **recognizable mutant**, knowable across a square. You
      keep your self, and keep the watch.
    - **Agility alone at 100**: your outline changes into something else
      — no footprints, a reflection that lags half a step, a shadow that
      arrives before you move. You keep your self, and keep the watch.
    - **Combinations** tell the truth about the whole body; and a
      keeper grown in **nothing**: overwhelmed, unable to comprehend,
      body failing the forces at hand — mutated, mad, into **one of the
      creatures of the forgotten city**. The thing the survivors will
      learn to fear was, until that night, a person with a name, a
      knife, and a child. The heir still wakes, and the lamp still
      passes; Jericho names the new horrors wrong, and always will.

11. **Death & the blood line.** Death is permanent — but a child of 14 or
   more *continues*: the save stands, you take over the heir with half your
   old body's leanings plus their taught "spark" for stats, the house and
   its upgrades, your good weapons, and 15g (inheritance is taxed).

   **The posthumous heir.** Die childless but with a living wife, and the
   midwife climbs the stair before the wax on your name dries: she was
   with child — yours, dated to the week you went down. Then seventeen
   years pass *in the world, not to a fade-out*: NPCs age their thousands
   of weeks, wed, and die of age — the old grandmothers go in the first
   winters, new faces arrive with the carts, the street crowd it replaced
   never knew you. Your widow may live to meet the heir she raised on
   your stories and her own unkind addenda; or Jericho raises the child
   on leavings, and your mother's grave is under the Church's slates with
   your name in her book, spelled right. You take over the heir at 17
   with a spark the single bed left bright, one house richer in grief —
   and the Gate's blood condition carried forward, not skipped. Dead with
   nothing at all? The save is wiped. A widowed spouse is remembered in
   the world — and courting your own parent is, correctly, impossible.

## Systems summary

- Stats: STR/AGI/WIT/WLL; checks are d10 + stat − wounds vs difficulty.
- Stamina economy gates everything: delving, training, burial — and
  honest work, which spends it at a loss. Stamina can run into **debt**:
  it may sink to -25, and crossing under zero (then again at -8, -16,
  -24) makes the body take a payment in **flesh** (a wound) or in
  **sickness** (an *unwell* entry); both penalize every check. Past -25
  every step is a **death roll** (d10 + Will against the body). Eating
  climbs you back out; the loan ledger resets on solid ground.
- Two ledgers of hidden lore: 9 truth fragments (they feed the Gate's
  conditions) and 25 kingdom scraps (they feed only the city's
  misreading of itself). Neither ledger mentions the other. Twelve of
  the kingdom's scraps arrive on the crown-arc clock — adviser,
  wedding, houses, regent, legions, coronation, withdrawal, coup, war,
  deserters, the adviser's failure, the grey figure — and are
  therefore different news depending on whether you read them as a
  17-year-old delver or as your own child.
- Every floor keeps a **miniboss** (7 landlord lineages: 3 beasts, 4
  alien persons). Landlords can grant truth fragments, trade, assign
  errands, and become permanent non-hostile **pacts** once an errand is
  honored. Their stats scale with the floor's danger tier.
- Aging: no time limits, but years pass as you train, delve, and rest;
  max HP declines past 40. Jericho keeps the receipts.
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
