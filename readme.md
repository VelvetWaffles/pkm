You are a game developer.

Create a single-page idle game DEMO in a single HTML file with inline CSS and JavaScript (no build tools, no external dependencies).

Theme: Pokémon-style egg hatching & idle work blind-box collection.

Core loop:
money → buy eggs → hatch eggs into Pokémon → assign them to work → earn more money → buy more eggs.

GENERAL:
- Everything must be inside one index.html file.
- Use modern, clean UI with good-looking layout (flexbox/grid), soft colors, and some simple effects (CSS transitions, glows, small particle-like animations using simple DOM elements).
- No external images required: use colored circles/cards with text to represent Pokémon and eggs.
- Code must be clearly structured and commented.

CURRENCIES:
- Two currencies: Money and Berries.
- Money is used to buy eggs and unlock extra slots.
- Berries are used to feed Pokémon (hunger).
- If current Berries are lower than the total required food for all Pokémon in this tick:
  - All working Pokémon produce 0 resources (efficiency = 0),
  - UI should show a warning like: "Not enough berries, all work efficiency = 0".

EGGS & HATCHING:
- Player can buy eggs at a fixed price.
- Up to 3 egg slots:
  - At the start, only 1 egg slot is unlocked.
  - The other 2 egg slots must be unlocked with Money.
- Each egg has a hatching timer that counts down every tick.
- When the timer finishes, the player can click “Hatch” to get a random Pokémon.
- The stronger the Pokémon:
  - the longer the egg hatching time,
  - the more berries it needs per tick when working.
- Implement at least 10 different Pokémon-style species (use Pokémon-like names but do NOT use any copyrighted sprites, text-only is fine).
- Each species should have at least:
  - name
  - rarity
  - basePower
  - hatchTimeMultiplier
  - berryNeedMultiplier
  - preferredLocation (e.g. "daycare", "forest", "powerPlant")
  - workMultipliers: an object like { daycare: 1.0, forest: 1.2, powerPlant: 0.8 } to define different efficiency per location
  - spriteKey (placeholder for future art)
  - voiceKey (placeholder for future voice)

PLACEHOLDERS FOR ART & VOICE:
- In each Pokémon card UI, create:
  - a visible avatar placeholder area (e.g. a div with text "Art Placeholder"),
  - a “Play Voice” button that currently only triggers a simple placeholder behavior (like alert or console.log).
- The data fields spriteKey and voiceKey should be included in the Pokémon data structure but only used as labels/placeholders for now.

HUNGER & BERRIES:
- Each Pokémon has a required berry consumption per tick when working.
- When NOT working (idle/standby), its required berries are only one fifth (1/5) of its working berry need.
- Total required berries per tick = sum of all Pokémon needs (working + idle).
- If player’s current Berries < total required berries:
  - All working Pokémon’s actual production rate becomes 0.
  - Show a clear warning in the UI (“Not enough berries, all work efficiency = 0”).
- If berries are enough:
  - working Pokémon generate resources normally, subject to stamina and work multipliers.

STAMINA SYSTEM:
- Each Pokémon has a stamina system:
  - maxStamina (e.g. 100),
  - current stamina.
- While a Pokémon is working (assigned to a work location):
  - stamina decreases over time each tick (e.g. -1 or -2 per tick; you can choose a simple constant).
- When stamina reaches 0:
  - that Pokémon’s work efficiency becomes 0 (no production),
  - a 😔 emoji bubble is displayed over its head to show it is exhausted.
- When a Pokémon is idle (not assigned to any work location):
  - stamina regenerates over time at HALF the speed of its work drain.
  - For example: if work drain is -2 per tick, idle recovery is +1 per tick.
- A simple stamina bar or text like “Stamina: 45/100” must be shown on the Pokémon card.
- For simplicity, if stamina > 0, the Pokémon can produce at full efficiency; if stamina <= 0, its efficiency is 0. (Do not produce if stamina is 0.)

WORK LOCATIONS:
There are three work locations:
  1. Daycare: speeds up egg hatching.
  2. Nameless Forest: generates Berries.
  3. Power Plant: generates Money.

- Each location can hold up to 6 Pokémon at the same time.
- At the beginning, each location only has 2 available slots.
- The extra 4 slots per location must be unlocked by spending Money.
- Assigning/removing Pokémon should be done via simple buttons in the UI (e.g. select a Pokémon from a list and click “Assign to Daycare / Forest / Power Plant” or “Remove from work”).

DIFFERENT EFFICIENCY PER LOCATION:
- The same Pokémon must have different efficiency depending on the location.
- Use the workMultipliers object for each species:
  - For example: effectivePower = basePower * workMultipliers[locationId].
- The total production for a location each tick should consider:
  - each Pokémon’s basePower,
  - that location’s multiplier for that Pokémon,
  - whether Berries are sufficient,
  - whether stamina > 0.

EMOJI INTERACTION & BALLOONS:
- When clicking on a Pokémon card:
  - display a small dialogue balloon above the Pokémon’s head (positioned over the card),
  - show an emoji to create a sense of interaction (e.g. 🙂, 😆, 😴, etc.),
  - the balloon should fade out after a short time via CSS transition.
- When assigning a Pokémon to a work location:
  - If the location matches its preferredLocation:
    - show a ❤️ emoji in a balloon above the Pokémon (happy in its favorite job).
  - If the location is NOT its preferredLocation:
    - show a 💔 emoji balloon (it doesn’t like this job as much).
- When stamina reaches 0 while working:
  - the Pokémon’s efficiency becomes 0,
  - show a 😔 emoji balloon over its head (exhausted),
  - keep showing that its stamina is 0 until it recovers by resting.

GAME LOOP:
- Use a main game loop with setInterval (e.g. every 1 second) to:
  - Update hatching timers for eggs.
  - Apply Daycare effect to decrease egg remaining time faster based on assigned Pokémon effective power.
  - Recalculate total berry requirement for all Pokémon (working and idle).
  - Check if berries are sufficient; if not, set all production to 0 and show warning.
  - Update stamina for each Pokémon:
    - decrease when working,
    - increase when idle at half the speed.
  - Apply production from each work location if berries and stamina conditions are met:
    - Daycare: faster egg progress.
    - Nameless Forest: add berries based on total effective power (power * location multiplier) of Pokémon assigned there.
    - Power Plant: add money based on total effective power (power * location multiplier) of Pokémon assigned there.
- Pokémon “strength/power” should increase both:
  - their resource production (more power = more production),
  - their hatch time and berry consumption (more power = more time + more berries).

UI LAYOUT (SUGGESTED):
- Top bar: display current Money, current Berries, total required berries this tick, plus a status text (e.g. “Working normally” / “No berries, work stopped!”).
- Left panel: Egg section
  - Show up to 3 egg slots (locked slots should show cost to unlock).
  - Each active egg slot shows:
    - If empty: “Buy egg” button with price.
    - If egg in progress: remaining time and a progress bar.
    - If egg finished: “Hatch” button to add Pokémon to collection.
- Middle/right panel: Pokémon collection
  - List all hatched Pokémon cards with: 
    - name, rarity, power, berryNeed, stamina info, current assigned location (“Idle”, “Daycare”, “Forest”, “Power Plant”),
    - art placeholder area,
    - “Play Voice” button (placeholder).
  - Buttons to assign/unassign from locations.
  - Clicking a Pokémon card must show the emoji balloon interaction above it.
- Bottom or side panel: Work locations
  - Daycare, Nameless Forest, Power Plant sections.
  - Show: unlocked slots / max slots, Pokémon currently assigned (name + power + effective power), and total effect (e.g. “Total hatch speed bonus”, “Total berries per tick”, “Total money per tick”).
  - Buttons to unlock extra slots.

VISUAL EFFECTS:
- Use CSS transitions and box-shadow glows when:
  - gaining Money or Berries (briefly highlight the currency text),
  - hatching a Pokémon (egg card glow + small “spark” text or simple particle divs).
- Implement simple “particle-like” effects by creating small absolutely positioned divs that float up and fade out when resources are earned or when a Pokémon is hatched.
- Emoji balloons should fade in and out smoothly with CSS transitions.

CODE QUALITY:
- No external libraries or frameworks (pure HTML/CSS/JS).
- Use clear function names like:
  - tick(), updateUI(), buyEgg(), hatchEgg(), assignPokemon(), unassignPokemon(), updateStamina(), showEmojiBubble(), etc.
- Put all game state in a single JS object (e.g. gameState) to keep it organized.
- Add enough comments so that someone reading the code can easily understand and modify the logic.
