# Field Command

A phone-playable real-time strategy game against an AI, rendered as a topographic
military survey map with NATO-style unit counters. Expand, research your way to
armour, take the enemy HQ.

One self-contained HTML file. No build step, no dependencies, no network calls.

## Files

| File | What it is |
|---|---|
| `index.html` | The entire game. No dependencies. |
| `manifest.webmanifest` | Makes it installable as a web app |
| `icon-180.png` | iOS home screen icon |
| `icon-192/512/1024.png` | Android and PWA icons |

All five need to sit in the same folder.

## Host it (GitHub Pages)

1. New repo. Put all the files in the root and push.
2. Settings → Pages → Deploy from a branch → `main` → `/ (root)`.
3. Open the URL on your phone → Share → **Add to Home Screen**.

It launches full screen with no Safari chrome, uses the tank icon, respects the
notch and the home indicator on every iPhone size, and won't rubber-band or
pinch-zoom the page out from under you.

## The research tree

This is the centre of the game. **Vehicles do not exist until you unlock them**,
and research needs Research Labs on the map. Tier 1 needs 1 lab, tier 2 needs 2,
tier 3 needs 3. Extra labs beyond the tier requirement speed research up.

| Branch | Tier 1 | Tier 2 | Tier 3 |
|---|---|---|---|
| Logistics | Field Rations, Conscription | Supply Lines | |
| Infantry | Rifling | Squad Tactics, Body Armour, AT Rifles | |
| Armour | Motorisation | Engines, Armour Plate | Composite, Main Gun |
| Artillery | Field Guns | Ballistics, Howitzers | Rocket Battery |
| Recon | Optics | Radar | |

Unlocks chain into buildings: Motorisation gives you the Motor Pool, Armour Plate
gives you the Tank Factory and the Light Tank, Composite gives the Medium Tank,
Main Gun gives the Heavy Tank. Field Guns opens the Artillery Park.

## Units and what beats what

Twelve units across five classes. The damage matrix is `MTX` in the source.

| Class | Beats | Loses to |
|---|---|---|
| Infantry | artillery, AT teams | vehicles |
| AT teams | vehicles (2.6×) | infantry |
| Vehicles | infantry, artillery, AT teams | AT teams, massed guns |
| Artillery | structures (2.5×), infantry | anything that closes the distance |
| Recon | nothing, it is eyes | everything |

Artillery has a **minimum range** and physically reverses away from anything that
gets inside it. Howitzers and rockets fire real shells with travel time and splash,
so clumped-up formations get punished.

## Menus

Four tabs across the bottom, at thumb height. Tapping one slides a sheet up over
the map; tapping it again closes it.

**BUILD** — every installation in one grid, with its cost, what it does, and a
count of how many you already have. Locked ones say which technology unlocks
them, unaffordable ones say exactly how much you are short. Tap one to enter
placement mode.

**TRAIN** — every unit in one grid. You do not have to find and select the right
building: tap a unit and it goes to a free Barracks, Motor Pool, Tank Factory or
Artillery Park automatically, choosing the shortest wait if they are all working.
Each building holds a queue of five. The top of the sheet shows everything
currently in production as chips; tap a chip to cancel it and get a full refund.
There is also a **global rally point** so everything you train walks to the same
place instead of piling up wherever it was made. A per-building rally still
overrides it.

**FORCES** — a roster of what you own by type with counts and a damaged count.
Tap a type to select every one of them across the whole map and jump the camera
to them. Plus all-combat and idle selectors.

**TECH** — the research graph.

The tab bar shows a pip with how many units are in production, and a dot when the
labs are busy.

## Controls

| Action | How |
|---|---|
| Pan / zoom | Drag / pinch |
| Jump | Tap the minimap |
| Select | Tap a unit |
| Select all of that type on screen | Tap the same unit twice |
| Move or attack | Tap ground or an enemy |
| Attack-move | Press and hold |
| Everything you own | FORCES tab |
| Train anything | TRAIN tab |
| Research | TECH tab |
| Speed | 1× / 2× / 3× |

## Unit art

Everything is drawn top-down from the unit's own frame, so hulls rotate with the
direction of travel while **turrets track their target independently**. Tanks have
tracks with visible links, sloped hull plates, a rotating turret with a hatch, and
a gun barrel that kicks back on firing with a muzzle flash. Guns have split trails,
wheels and a shield. Infantry are two-man teams with helmets, packs and weapons,
walking with a slight step cycle. Vehicles kick up track dust as they move.

Below about 60% zoom the detailed icons turn to mush, so the renderer drops to a
simple readable blip per unit. Buildings are drawn as installations: sawtooth
factory roof, a rotating radar sweep, a spinning lab dish, hexagonal bunkers with
an embrasure, a fluttering flag on the HQ, zigzag trenches.

## Map

The heightfield is generated first, then everything is drawn from it: elevation
bands, contour lines by marching squares with heavier index contours every third
line, hard shorelines, woodland stipple, rock hachures, and a 5-tile survey grid
with references. Fog of war applies to both sides. The AI only reacts to units it
can actually see, and it sends recon out to find you.

## Tech tree

The TECH button opens a real dependency graph: tiers as columns, branches as
lanes, prerequisites drawn as curved edges. Edges light up amber once the
prerequisite is researched and go green once the node itself is done. Each node
shows its tier, its effect, what it physically unlocks, and exactly what is
blocking it. Pan it with a finger.

## Tuning

All at the top of the `<script>` block: `U` units, `B` buildings, `TECH` the tree,
`MTX` the counter matrix, `DIFF` the four difficulties, `RESEARCH_RATE` the global
research speed. `aiTick()` and `nextBuild()` are the AI's whole brain.

## Tested

Both sides driven by identical logic, 5 games per difficulty on a 56×56 map:

| Difficulty | Player record | Typical length | First tanks |
|---|---|---|---|
| Cadet | 5W 0L | 11–13 min | ~9 min |
| Regular | 2W 2L 1 stale | 11–30 min | ~10 min |
| Veteran | 0W 5L | 8–12 min | ~7 min |
| General | 0W 5L | 5–10 min | ~7 min |

Regular is a coin flip against a bot, so a human paying attention should beat it.
Veteran and General expect you to actually play well.

416 units on a 72×72 map costs 2.65 ms per tick. Stable across every map size and
difficulty combination.

## Still missing

- No save. Close the app and the game is gone.
- No multiplayer.
- The AI does not retreat damaged units or focus-fire.
- Long games on Cadet can stall out past 20 minutes.
- Largest map holds an 18 MB terrain canvas. Fine on modern phones, possibly not on old ones.
