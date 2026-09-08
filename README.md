# D-Cap

A browser-based screening tool for offshore wind installation vessels. No installation required — open the link and start using it.

The tool has two parts: a **WTG Calculator** for turbine installation, and a **Foundation** tab for monopile transport and installation.

---

## WTG Calculator

### What it does

Screens how many turbine sets a given vessel can carry for a specific turbine size, and whether the vessel can actually install them at a given site. It covers:

- **Deck capacity** — how many complete sets (tower + nacelle + 3 blades) fit on deck based on geometry, deck area, and payload
- **Installation gates** — water depth vs leg length, hook height vs hub height, tower single-piece lift, crane capacity vs component weight
- **Deck arrangement** — a plan view drawing showing where towers, nacelles, and the blade rack sit on deck
- **Fleet screening** — compare all vessels in the database against one turbine size in a single table

### How to use it

**Calculator tab**
1. Select a **vessel** from the dropdown on the left
2. Select a **turbine size** from the dropdown at the top right
3. Adjust the **site parameters** (water depth, tide, air gap, leg penetration) in the Operation panel if needed
4. The result appears immediately — set count, which gate is binding, and a deck plan drawing

The left panel shows all vessel parameters, all editable directly in the input fields. Click **Save Data** in the header to persist your changes.

**Fleet screening tab** — every vessel's set count, binding constraint, and gate pass/fail for the selected turbine, in one table.

**Vessel & turbine data tab** — the full database with confirmed vs estimated flags per field. Green = confirmed from operator or designer publications. Orange = estimated.

**Method & assumptions tab** — the calculation logic, gate definitions, and deck arrangement rules in detail.

### Understanding the results

**Set count** — one set = one tower (all sections) + one nacelle (hub included) + three blades.

**Binding constraint** — the single factor limiting the set count:
- *Deck arrangement* — geometric solver ran out of deck space before payload
- *Payload* — total component weight reached the deck load limit
- *Hook reach* — the crane cannot reach hub height with adequate clearance. Required height is the turbine's hub height directly (149.5 m for 15 MW, 172 m for 18 MW, 187 m for 21 MW) — no extra margin added on top.
- *Water depth* — legs too short for the site
- *Crane capacity* — crane cannot lift the heaviest component at the required radius

**Tower single-piece lift (advisory)** — if the vessel's hook height above deck is less than the turbine's erected tower length, the tower must be split into sections to lift. This doesn't fail the installation gate on its own, but the tool flags it as not recommended — extra lifts, extra rigging, reduced efficiency.

**Air gap boost** — if the hook can't quite reach hub height on the site's nominal air gap, the tool automatically checks whether jacking the hull 10 m higher (more air gap) would close the gap, but only if the legs have enough spare length to physically support standing that much higher. If both conditions hold, the result shows as passing and the verdict text explains the assumption — always double-check the extra jacking height is realistic for the actual site before relying on it.

**Deck plan drawing** — towers grouped port-side close to the crane leg, nacelles starboard-side under the boom, blade rack aft, containers filling the remainder.

---

## Foundation

### What it does

Answers a different question: **given this vessel and this monopile, how many can it carry on deck and actually install per campaign?**

Unlike the WTG calculator, this is not a fixed-count table — it's a live geometric and engineering model. It covers:

- **Deck arrangement** for both jack-up and floating vessels, with correct physical logic for each
- **Individual monopile properties** — every single monopile (including multiple piles stacked at the same position) has its own diameter, length, and weight
- **Crane radius/SWL curve** — capacity checked at the actual lifting radius for each storage position, not just the crane's headline rating
- **Realistic vessel visuals** — top view and side view showing legs, crane, boom (in its at-rest transit position), gripper, hammer, and accommodation

### Jack-up vs floating logic

- **Jack-up vessels**: monopiles are stored **transversely** (lying across the beam, between the aft and forward leg pairs) and can be **stacked** — multiple piles at the same deck position, one above the other. Tight clearance (~0.8 m) between adjacent stacking positions.
- **Floating vessels**: monopiles are stored **side by side** along the ship's length. Cannot be stacked. A configurable minimum clearance (default 4 m) is enforced between adjacent piles.

### How to use it

1. Select a **vessel** from the dropdown
2. Under **Arrangement**, each storage position ("stack") lists its monopiles individually — each with its own **diameter, length, and weight** input. Click **+ Stack another monopile here** to add another pile to that same position (jack-ups only), or **+ Add position** to add a new storage location.
3. Set the **crane side** (aft port/starboard) and add extra **crane radius/SWL points** if the crane's capacity curve needs more than one reference point
4. Set the **spreader bar weight** (default 150 t) — this is always included in every lift check automatically
5. Enter the **piles required for the project** under Campaign to get trips-per-campaign

The top view and side view update live as you change any of these. You can also drag monopile stacks, the gripper, the hammer, and (on floating vessels) the crane directly on the top-view drawing.

### Understanding the results

**Practical capacity** — the number of monopiles the vessel can both carry (deck arrangement + payload) *and* successfully lift (crane SWL at the actual radius for each position). Every individual pile is checked against the crane curve at its own stack's distance from the crane — a pile in the far stack faces a larger radius and therefore a lower SWL than one right next to the crane.

**Green/red dot per pile** — shown next to each monopile in both views. Green means the crane can lift that specific pile (its weight + the spreader bar) at that position's radius; red means it can't.

**Usable payload** — if the vessel's spec gives cargo payload directly, that figure is used as-is. If only deadweight is available, roughly 1,500 t is deducted for fuel, provisions, and auxiliaries before comparing against total pile weight.

**Warnings** — shown when a stack overlaps a leg or the crane's exclusion zone, when floating-vessel piles are closer than the configured clearance, or when total cargo weight exceeds usable payload.

**Some vessels are flagged as not foundation-capable** (e.g. Pioneering Spirit) — these are heavy-lift vessels not equipped or operationally used for monopile installation, and show 0 capacity with that reason stated explicitly rather than a misleading number.

---

## Editing and saving data

All input fields — vessel specs, turbine parameters, monopile properties — are editable directly. To persist changes:

1. Change the value in the relevant field
2. Click **Save Data** in the header

Changes are saved to the browser's local storage and persist **in that browser only** — clearing browser data or switching browsers/devices loses them.

To back up: **Export data** downloads a JSON file of the full dataset. **Import data** restores from that file on another machine. **Reset defaults** discards all changes and restores the original data.

---

## Vessel database

65 vessels across both jack-up and floating/heavy-lift types:

- **Cadeler** — Wind Peak, Wind Pace, Wind Ace/Ally/Apex, Wind Maker/Mover, Wind Orca/Osprey, Wind Scylla, Wind Keeper, Wind Zaratan, T-class
- **Van Oord** — Boreas, Aeolus, Sea Installer, Sea Challenger, Innovation, MPI Adventure/Resolution
- **Jan De Nul** — Voltaire, Vole au Vent, Les Alizés
- **Fred Olsen Windcarrier (FOWIC)** — Blue Tern, Bold Tern, Brave Tern, Blue Wind
- **Seaway 7** — Seaway Ventus, Seaway Alfa Lift, Seaway Strashnov, Seaway Yudin
- **Dominion Energy** — Charybdis
- **DEME** — Orion, Norse Energi, Norse Wind, Neptune
- **Heerema** — Aegir, Balder, Sleipnir, Thialf
- **Allseas** — Pioneering Spirit
- **Saipem** — Saipem 7000
- **Boskalis** — BOKALIFT 1, BOKALIFT 2
- **ZITON** and various smaller/regional vessels

Vessel data is sourced from operator and designer publications where available, flagged confirmed vs estimated in the Vessel & turbine data tab.

---

## Turbine sizes (WTG Calculator)

Size bands from 5 MW up to 21 MW. Component weights and footprints scale from a confirmed 10 MW anchor. Hub heights, tower lengths, and tower-top-to-hub distances for 15/18/21 MW are taken directly from confirmed reference figures; other sizes use scaled estimates.

---

## Limitations

- Deck arrangements (both tabs) are geometric models, not certified stowage plans — they don't capture every seafastening or project-specific constraint.
- Crane capacity is modelled as a multi-point radius/SWL curve with linear interpolation between points, not a full manufacturer load chart.
- Many vessels have estimated hull dimensions affecting the drawings but not always the headline capacity number.
- Both tools are screening aids. Verify results against vessel operator documentation before using them in actual project planning.

---

## Questions or corrections

If you find a data error or want to suggest a vessel to add or for other ideas - please send a message to the developer.
