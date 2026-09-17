# PLAZA — Design Document

A fictional cartel empire-management web game. Single self-contained `index.html`
(zero dependencies, no external requests, works from `file://`). Mobile-first,
dark black / deep-green / gold aesthetic.

## High concept

Empire manager: run the numbers. You hold plazas (territories), cook product in
lab slots, move it through routes, and manage **heat**. Battles resolve as
event cards — fast pace, one decision per card. Sensible play wins in ~25–40
weeks; reckless play loses.

## Win / lose

- **Win:** empire value ≥ **$250,000** (cash + stash at 50% base value + plaza
  values + 50% of rival-held plaza value? No — rival plazas count only when
  captured) OR control **all 9 plazas**.
- **Lose:** federal raid on your last plaza; or broke + plazaless + empty stash.

### Roster discrepancy (documented, not silently "fixed")

The brief says "8 total plazas" but the supplied roster sums to **9**: 1 home
(Southside) + 2 neutral (The Market, Portside) + 3 rivals × 2 plazas each
(Hilltop/Foundry, Docks/Warehouse, Strip/Motel). The game implements the full
9-plaza roster; victory requires all 9 (or $250k value). UI text says "all 9".

## Core loop (weekly)

1. **Produce** — each owned plaza has lab slots (2 base, +1 per level to 4).
   Assign a drug per slot; production costs are charged at week end.
2. **Sell** — move stash through routes (Local / Regional / National).
   Sales generate heat.
3. **Manage heat** — bribes (-15 heat, $5k), lawyers (halve raid losses),
   laundering (10% fee removes $50k+ cash heat), laying low (no sales → -8 heat).
4. **Expand** — buy neutral plazas, capture rival plazas by force.
5. **End week** — production, rival turns, events, heat decay, win/lose check.

## Products (6, mechanically distinct)

| Drug | Prod $/u | Sale ~$/u | Heat/10u | Output/slot | Special |
|---|---|---|---|---|---|
| Weed | 50 | 100 | +2 | 30 | Bulky: +5% interception |
| MDMA | 120 | 300 | +2 | 18 | Festival season ~every 8 wks: ×1.8 sale, 2 wks |
| Meth | 200 | 450 | +3 | 14 | 3%/wk lab explosion: lose output, $2,000 damage |
| Cocaine | 500 | 1,100 | +5 | 12 | Connect-only until you capture a coke-lab plaza |
| Heroin | 400 | 900 | +4 | 12 | Price varies only ±8% (stable) |
| Fentanyl | 300 | 1,200 | +6 | 14 | Holding 20+: 4%/wk OD event → +15 heat, −20% price 2 wks |

Prices jitter ±15% weekly (heroin ±8%). Festival season is telegraphed a week
ahead ("word on the street").

## Routes (3)

- **Local** ×1.0 value, ×0.5 heat, 5% interception.
- **Regional** ×1.25 value, ×1.0 heat, 12% interception.
- **National** ×1.6 value, ×1.8 heat, 25% interception.
- Lookouts: −4% interception each (max 3). Weed bulky: +5%.
- Interception: lose the shipment, +5 heat.

## Plazas (9) & traits

- **Southside** (home, urban, 2 slots) — home defense ×1.3.
- **The Market** (neutral, rural, 1 slot, $15k) — rural: +1 heat decay, −10% raid chance.
- **Portside** (neutral, port, 2 slots, $35k) — port: −5% interception, +2% raid risk.
- Rival plazas: Hilltop, Foundry (Los Buitres); Docks, Warehouse (Serranos);
  Strip, Motel (Los Dientes). Warehouse & Docks are **coke-lab** plazas —
  capturing one unlocks cocaine production.
- Traits: **industrial** (+1 slot output, +1 heat/10 sold), **border**
  (+10% national value, +5% interception), plus urban/rural/port above.
- Upgrades: level 1→3, $3k×level, +1 slot and +50% output per level.

## Rivals (3, personality-driven)

- **Los Buitres** (aggressive): 40% attack weight… (tuned: 30%),
  expands, moderate truces. Starts 9 sicarios.
- **La Familia Serrano** (entrenched): rarely attacks (6%), expands 55%,
  +2 garrison when digging in (cap 14).
- **Los Dientes** (schemer): undercuts prices (−15% your sale value, 2 wks),
  steals shipments (lose 20% of a sale's units), 15% attack.
- All: hire sicarios, garrison plazas (2–4 on capture, up to 14), offer truces
  (3 weeks peace for $3k + $150/wk + $200/sicario — expensive by design).
- **Grace period:** no rival attacks before week 5.
- Counterattacks: attacking a rival raises its aggro (+2), making retaliation
  likelier.

## Combat (event cards)

- **Attack:** choose sicarios to send. Power = sent × (1 + 0.2×weapons) ×
  rand(0.8–1.2) vs garrison × rand(0.8–1.2). Win: capture plaza, +10 heat,
  lose 20–40% of sent. Fail: lose 30–60% of sent, +10 heat, rival aggro +2.
- **Defense:** rival force 2–6 × rand vs your sicarios × 0.9 (×1.3 at home) ×
  rand. Win: lose 1–2 sicarios, +5 heat. Lose: lose plaza + 20% sicarios.
- Sicarios: $1,000 each. Weapons: $8,000 each (+20% attack, max 3).
  Lookouts: $4,000 each (−4% interception, max 3). Lawyer: $25,000 retainer
  (halves raid cash/product losses).

## Heat & raids

- Heat 0–100. Selling, battles, events raise it; bribes, lawyers, laundering,
  laying low lower it.
- Cash > $50,000: +2 heat/week unless laundered (10% fee on the excess).
- **Heat 100 → federal raid:** lose 30% cash (15% with lawyer), half the stash
  at a random plaza, heat resets to 70. Raid on your **last plaza = game over**.
- **Snitch chain:** Snitch Warning event → pay $5k or the feds watch you
  **next week** (banner shown all week) → sell during the watched week and the
  raid comes. (Fixed during testing: the original version warned and resolved
  in the same end-of-week tick, making the choice meaningless.)

## Events (~15-card deck + chains)

Deal Gone Bad, Crooked Cop, DEA Tipoff, Snitch Warning (chain), Street War,
Festival Season (telegraphed), Lab Explosion (meth), Overdose Scandal
(fentanyl), Undercut (Dientes), Shipment Seized (Dientes), Truce Offer,
Rat in the Crew, Rival Hit (defense card), Supply Glut, Price Spike, New
Connect (heroin/fentanyl), Mayor's Favor (half-price bribes), War Rumors,
Surveillance Van (chain: +8 heat now, raid next week unless lay low or bribe).

## Money & crew actions (HQ)

Hire sicarios, buy weapons/lookouts/lawyer, launder, bribe, **street hustle**
(+$750, +2 heat, once/week — the comeback mechanic when broke or plazaless).

## Starting state

$8,000 cash (raised from $5,000 during balance testing — the original figure
couldn't fund both crew and production in week 1, a poverty trap), 20 weed,
5 sicarios, heat 10, Southside home plaza.

## Save system

`localStorage` key `plaza_save_v1` (versioned). Migration fills missing
top-level fields from a fresh state on load; nested drug/stash maps are rebuilt
per-drug so old saves gain new fields cleanly.

## Balance validation (headless Chromium, scripted bots)

- **Sensible bot** (produce → sell to heat budget → bribe → expand slowly,
  no wars): 4/5 wins, victories at weeks **18, 26, 29, 37** (target 25–40;
  bot is near-optimal, humans run slower).
- **Reckless bot** (always national route, never bribes, attacks blindly):
  3/3 losses (raid week 3, broke weeks 6 and 9).
- **Raid tests:** last-plaza raid = game over; two-plaza raid = survive with one.
- **Smoke test:** all actions incl. failure paths, zero console errors.
- Tuning changes driven by testing: rival attack weights/frequency cut, defense
  attrition 10–20% → flat 1–2 sicarios, home defense ×1.3, week-5 grace period,
  production outputs rebalanced, plaza prices raised, lay-low decay 3 → 8,
  truce spam reduced, hustle added as comeback mechanic.

## Visual map layer (added 2026-09-17)

The map is a **presentation-only canvas overlay** (`MapFX` module in
`index.html`). It reads game state and renders it; it never changes game
math. Every animation is cosmetic, tap-to-skip (any tap on the canvas clears
all in-flight effects), and driven by the game loop's own randomness —
`MapFX` keeps its own visual RNG so it can't shift gameplay rolls.

- **Territory:** deterministic procedural terrain (seeded, stable across
  frames): coastline/water east and south bay, border fence with patrols
  along the north edge, mountain range in the northwest, city street grid in
  the center. All nine plaza nodes drawn with name + trait.
- **Owner colors:** gold (player), red (Los Buitres), blue (La Familia
  Serrano), purple (Los Dientes), gray (neutral). Ownership changes tween
  color over ~0.6s plus a capture pulse ring.
- **Sales:** animated shipment dots run the real route (local = short hop
  from nearest owned plaza, regional = medium arc, cross-border = long arc
  to the border). Intercepted shipments burst red mid-route at the roll's
  "bust point". Shipment speed reflects route risk.
- **Heat:** plaza glow + red map vignette scale with heat; above 70 the map
  edge pulses red. Bribes trigger a cool-down ripple at the HQ plaza.
- **Battles:** player attacks show gold forces converging from owned plazas
  onto the target, a clash burst, then the ownership tween. Rival attacks
  play in reverse (rival-colored forces onto the player's plaza).
- **Rival activity:** gold pulse ring on the acting rival's plaza + a small
  banner naming the rival and action (attack / expand / scheme / truce).
- **Week resolution:** end-of-week production pops `+N DRUG` labels on
  producing plazas; lab explosions flash red at the plaza.
- **Events:** relevant events pin a pulsing marker at the affected plaza
  (or map center for global ones) with a banner.
- **Raids:** red expanding ring + screen flash on the raided plaza.
- **Cash counter** in the map HUD rolls toward the new value.
- **Map/Classic toggle:** segmented control above the map; Map is the
  default tab. All seven classic screens remain unchanged under their tabs.
- Plaza nodes are tappable → intel popup (owner, trait, lab, garrison for
  rivals) with contextual actions (manage labs, buy, attack, hustle...).
- Canvas is 960×1120 backing store at 2× for crisp text; logical 480×560.
  The live canvas is preserved across actions (only the HUD re-renders) so
  animations never restart mid-flight; remounts replay recent effects via
  `rebase()` without duplicating them.
- Tested headless: zero page/console errors across smoke, raid, reckless,
  and bot suites; screenshot-verified at 480px width; plaza tap popup works.

## Files

- `index.html` — the entire game (inline CSS/JS, zero dependencies).
- `DESIGN.md` — this file.
