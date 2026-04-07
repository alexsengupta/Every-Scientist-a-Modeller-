
# Example 2 — Island Disease Spread
## Platform: Gemini AI Studio


### Prompt 1

I'm an epidemiologist and I'd like to build an interactive simulation of disease spread across a network of island communities connected by ferry routes. I have a clear conceptual picture of the system but no background in programming or mathematical modelling. I want a single self-contained HTML/JavaScript file I can open in a browser and eventually host on a website.

Before you build anything, here is a detailed description of what I need. Please read it carefully, then ask me any clarifying questions before writing any code.

**The disease model**

Track three states for each person: susceptible (not yet infected), infected (currently sick and contagious), and recovered (permanently immune). In addition, infected individuals can die — I want this as a separate state. Keep the disease model simple; my interest is in how the spatial structure and transport network shape where and when outbreaks happen.

Age matters for outcomes but not transmission. Everyone has the same probability of catching the disease on contact, but older individuals have a higher probability of dying from it. Each island has a settable proportion of senior residents.

The model should be stochastic — populations are small enough (50–150 per island) that chance events matter. A single infected commuter arriving on an island should sometimes spark an outbreak and sometimes fizzle.

**The network structure**

One central hub island surrounded by four outer islands. Ferry routes run only between outer islands and the hub — outer islands have no direct connection to each other. All inter-island transmission must pass through the hub via commuters.

**How a day works**

Each simulated day has three phases:

1. *Morning — ferry departs:* On days when a ferry is scheduled (frequency is a per-route parameter), a random sample of residents from the outer island boards the ferry. The number of commuters per trip is a per-route parameter. If health screening is active, infected commuters who are symptomatic are detected and turned back — they stay on their home island. Those who pass travel to the hub and join its population for the day.

2. *Daytime — mixing and transmission:* Transmission occurs on all islands simultaneously. The hub's population includes its own residents plus all visiting commuters. For each susceptible person on an island, the probability of becoming infected is proportional to the fraction of infected people currently present (frequency-dependent transmission). Use a stochastic draw for each individual.

3. *Evening — ferry returns:* Commuters return to their home island.

Overnight: infected individuals progress — they may recover or die based on their age group and how long they've been infected. Recovery time is a global parameter.

**Visualisation — this is important, please implement carefully**

Show each island as a circular region containing coloured dots, one per person:
- Green = susceptible
- Red = infected
- Blue = recovered
- Black = deceased

Deceased individuals should be moved to a small dedicated graveyard region just outside their island's circle — they no longer participate in the population dynamics and should be visually separated from the living.

Each timestep, gently randomise the positions of living individuals within their island circle (a subtle jitter) so the visualisation feels alive. Deceased dots in the graveyard do not move.

The ferry should be visible at all times as a distinct shape (e.g., a labelled circle or icon) moving along the route line. It should be docked at the outer edge of the source island when waiting, travel to the outer edge of the hub when in transit, and dock there on arrival. It should always be somewhere visible — never disappearing between trips. Label it with the number of commuters currently on board (zero when docked and empty).

When the ferry departs, the selected commuters disappear from the island's dot cluster. When it arrives, they reappear in the hub's dot cluster. No need to animate individual dots moving — just their appearance and disappearance at departure and arrival. On the return trip, the same logic applies in reverse.

The hub island should be visually larger than the outer islands.

**Controls**

Global parameters (in a sidebar):
- Transmission rate β
- Recovery time (days)
- Young mortality rate (%)
- Senior mortality rate (%)
- Symptomatic fraction % — the proportion of infected people who show symptoms (a property of the disease)
- Screening sensitivity % — the probability that a symptomatic commuter is detected by screening (a property of the test). These are two separate parameters.
- Simulation speed

Per-island controls, displayed as a small panel adjacent to each island on the map:
- Population size
- Senior population %
- For outer islands only: ferry interval (days between trips), commuters per ferry

Interventions (global toggles):
- Enable health screening at hub
- Ferry lockdown (shut down all routes)

Seed infection on: a dropdown to select which island the initial infection starts on.

Start / Pause / Reset buttons. Any change to a parameter or intervention toggle should automatically reset the simulation to day 0 and wait for the user to press Start.

**Charts (below the map)**

Two panels side by side:

Left: Time series showing either S, I, R, D counts over time (the user can select the preferred variable), with a separate line per island. Include checkboxes to toggle individual islands and a Total option.

Right: Bar chart showing a selected metric broken down by island, with toggle buttons to switch between: Infected, Susceptible, Recovered, Deceased. The bar chart should distinguish senior and younger counts within each bar.

**What I want you to do now**

Read this specification carefully and ask me any clarifying questions before writing any code. I would rather answer questions upfront than fix problems later.


GEMINI:
This sounds like a fantastic and highly useful project. I have a clear understanding of the overall architecture: a star-network stochastic SIRD model with frequency-dependent transmission, age-stratified mortality, visual particle jitter, and specific daily commuter phases.
To ensure the final simulation behaves exactly as you need for your epidemiological exploration, I have a few clarifying questions before I write any code.
1. Disease & Progression Mechanics
Recovery / Death Timing: You mentioned individuals "may recover or die based on their age group and how long they've been infected." Does an individual's outcome (recovery vs. death) get decided at the end of a fixed infection duration (e.g., on day 7, they either die or recover)? Or is there a daily probability of dying while they are infected?
Fixed vs. Variable Duration: Should the "Recovery Time" parameter be a strict fixed number of days for everyone, or an average duration (where individuals have a daily probability of recovery equivalent to 1 / recovery_time, which is standard for Markov/SIR models)?
Initial Seed: When selecting the initial seed island, how many individuals should start infected? Just a single randomly chosen individual (patient zero), or a small cluster?
Transmission Equation: For frequency-dependent transmission, the standard discrete-time probability of a susceptible individual getting infected in a single day is 
P
=
1
−
e
−
β
(
I
/
N
)
P=1−e 
−β(I/N)
 
. Should I use this, or a simpler 
P
=
β
×
(
I
/
N
)
P=β×(I/N)
 capped at 1.0?
2. Ferries & Commuter Logic
Hub Residents: Do residents of the Hub island ever commute to the outer islands, or is traffic strictly outer-island residents visiting the Hub for the day and returning?
Random Sampling: Are the commuters randomly selected from the outer island's population anew on every scheduled ferry day, or is there a fixed sub-group of "commuters" who always take the ferry?
Screening Timing: You mentioned screening stops symptomatic people from boarding. I assume this happens on the morning departure from the outer island. Do we also screen commuters on the return trip in the evening? (i.e., if they caught it at the Hub and somehow became symptomatic that same day, are they stranded at the Hub?)
Visualizing the Trip: Since a "day" happens in phases, how would you like the visual pacing handled? Should the simulation pause the disease math to animate the ferries slowly crossing the water, then do the daytime math, then animate them returning? Or should the ferries snap to their locations as the days tick by quickly?
3. Visuals & Technical Details
External Libraries: You requested a single, self-contained HTML/JavaScript file. Is it acceptable to include external CDN links for charting libraries (like Chart.js)? This keeps the file single and self-contained but requires an internet connection to load the charts. If you need it to work completely offline, I will build a custom charting solution from scratch.
Layout: I plan to place the Hub in the center with the four outer islands positioned North, South, East, and West. The graveyard can be a small shaded rectangle just outside the bottom edge of each island. Does this align with your vision?
Pause on Reset: When a parameter is changed and the simulation automatically resets to Day 0, should it wait in a "Paused" state until you press Start again?
Once we clear up these details, I will be ready to build the complete simulation for you!

### Prompt 2

> Good questions. Here are my answers.
>
> **Disease progression:** A daily probability makes more biological sense — people don't all recover on exactly the same day. So a daily chance of recovery (based on the recovery time parameter) and a separate daily chance of death (based on age group) while someone is infected. I don't need to track how many days someone has been infected; a simple daily draw is fine.
>
> **Initial seed:** A single patient zero — I want to observe how a single introduction sometimes sparks a full outbreak and sometimes fizzles. That stochasticity is the point.
>
> **Transmission equation:** I'll defer to you on whichever is more standard for this type of model. I don't have a strong preference on the mathematics.
>
> **Hub commuting:** One-way only — outer island residents visit the hub and return. Hub residents stay put.
>
> **Commuter selection:** Random each time, drawn from the whole island population including infected and recovered individuals. Only symptomatic infected commuters get turned back by screening.
>
> **Screening timing:** Morning departure only. If a commuter catches the disease at the hub during the day, they return home in the evening regardless — no same-day symptoms.
>
> **Visual pacing:** I'd like to see the ferry visibly travel — a brief animation of the ferry crossing the water — before the daytime mixing happens, then the return trip in the evening. The speed slider should control how fast this all plays out.
>
> **External links:** Fine, as long as the file works when opened in a browser.
>
> **Layout:** Hub in the centre, outer islands N/S/E/W sounds good. Graveyard as a small shaded area just outside each island circle works well.
>
> **Reset behaviour:** Yes — after any parameter change, reset to day 0 and wait in a paused state for me to press Start.
>
> One thing to confirm from my original specification: the per-island controls (population size, senior %, ferry interval, commuters per ferry) should appear as small panels directly adjacent to each island on the map — not in the sidebar. This is important for usability.
>
> Go ahead and build it.

GEMINI:
Here is the complete, single-file HTML simulation. It includes the stochastic SIRD model, the interactive visual canvas with island and ferry mechanics, per-island configurations positioned directly on the map, and the dynamic charts you requested.
Save the code below as an .html file (for example, island_epidemic.html) and open it in any modern web browser. (It uses the Chart.js library via a CDN, so it requires an internet connection to render the charts).
code
CODE


Researcher's response (Prompt 3 — Errors and layout):

Im getting errors when the pahe loads and I press the start button
[Error] Failed to load resource: the server responded with a status of 404 () (chart.umd.min.js.map, line 0)
[Error] TypeError: null is not an object (evaluating 'island.x')
	jitter (island_epidemic.html (1).html:227)
	forEach
	stepAnimation (island_epidemic.html (1).html:435)
	loop (island_epidemic.html (1).html:622)

    The layout also needs some work, Ive added a a screenshot


 GEMINI: 
Ah, I see exactly what happened.
1. The TypeError (The freeze on Start):
When a commuter boards the ferry, their currentIsland is temporarily set to null while they are in transit. The visual jitter function was trying to bounce them around their island without checking if they were actually on one, causing the null is not an object crash.
2. The 404 Error:
The chart.umd.min.js.map error is a harmless browser DevTools warning. It just means the browser is looking for a "source map" file to help debug the Chart.js library, which the CDN doesn't provide. It doesn't affect the simulation at all, but I have swapped the CDN link to a cleaner one so your console stays error-free.
3. The Layout:
I have completely overhauled the CSS and canvas dimensions. The canvas is now smaller, the UI is more compact, and the island control panels are strictly pinned to the empty corners of the map so they will never overlap with the islands, ferries, or graveyards. The entire interface is now constrained to 100vh (100% of your screen height) so it should fit beautifully on a Macbook without needing to scroll down to see the charts.
Here is the fixed and highly polished code. Replace your current file with this:
CODE

Researcher's response (Prompt 4 — Visual redesign):

> The simulation is running but the interface needs significant visual work. A few specific requests:
>
> **Layout — make the map larger:** The charts at the bottom are taking up too much vertical space and crowding the map. Could we make the charts collapsible — a small toggle button that folds them away so the map fills most of the screen? When collapsed, just show a thin strip with the toggle. This would make a big difference for demonstrations and teaching.
>
> **Left sidebar — replace number steppers with sliders:** The number spinners (up/down arrows) are fiddly to use. Please replace all the global parameter controls with smooth sliders, showing the current value alongside. This is much more satisfying to interact with and better for live demonstration.
>
> **Dot positioning bug:** The individual dots appear to congregate at one edge of the island rather than being distributed across it. It looks like they may be drifting toward the boundary rather than staying spread across the interior. Could you check the jitter logic — the dots should stay well within the island circle, moving gently around the interior rather than clustering at the edge.
>
> **Visual style:** The current look is quite plain. A few simple improvements: a darker, more attractive background for the map area (something like a soft ocean blue/teal); the island circles could have a subtle fill and a cleaner border; the sidebar could have clearer section separators and slightly more breathing room between controls. The graveyard areas could be labelled.
>
> **Per-island panels:** The control boxes next to each island look functional but bare. Could they have slightly rounded corners and a subtle background to visually distinguish them from the map without being obtrusive?
>
> **Ferry circles:** These are good but could be a little more visually distinct — perhaps a small boat icon or a different shape from the population dots, so they're clearly identifiable at a glance.

GEMINI:

Here is the thoroughly upgraded version of your simulation. I have addressed every point in your request, significantly improving both the visual design and the underlying math.
Key Upgrades:
Responsive Map & Collapsible Charts: The map now fills the entire available screen area. The charts area has a toggle bar at the top to smoothly collapse/expand, giving you full control during presentations.
Slider Controls: The sidebar now uses smooth range sliders with dynamic number readouts above them. It’s spaced out cleanly with modern section styling.
Beautiful Visuals: The map features a deep gradient ocean background. Islands have subtle glowing outlines, softer fills, and neatly labelled graveyard zones. The control panels use "glassmorphism" (blur and semi-transparent backgrounds) so they float cleanly over the water.
Ferry Boat Icons: Ferries are now drawn as little distinct boats that dynamically rotate to face their direction of travel.
Fixed Jitter & Distribution: I re-wrote the mathematical distribution. Individuals are now generated uniformly across the entire circle area (using the square root of a random variable), and the jitter uses a bounding clamp to prevent them from "walking" to the edge over time.
Replace your HTML file with the code below:


