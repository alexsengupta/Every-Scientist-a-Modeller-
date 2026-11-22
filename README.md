# Every Scientist a Modeller? — Interactive simulations

Companion code for the manuscript **"Every Scientist a Modeller? The Promise and Peril of AI-Built Simulations"** (A. Sen Gupta).

These are the self-contained, AI-built interactive simulations referred to in the paper and its Supplementary Information. Each was produced through a recorded dialogue with a general-purpose AI assistant (ChatGPT or Gemini), with the human author making and checking all of the modelling decisions, as described in the manuscript. They are provided for transparency and reproducibility.

> ⚠️ The models are illustrative teaching and exploration tools. Parameters are **not fitted to any real system**, and the simple in-browser solvers are intended for **qualitative** exploration, not quantitative prediction.

## The apps

| File | Example | Built with | What it does |
|------|---------|-----------|--------------|
| `predator_prey_delay_explorer.html` | Example 1 | ChatGPT | Predator–prey dynamics with a processing delay (after Fan & Wolkowicz 2021). Sliders for prey growth, carrying capacity, attack rate, predator mortality, conversion efficiency and delay; time-series and phase-plane plots; preset scenarios including a published-result validation preset. *(Main-text Figures 3–4; Supplementary Example 1.)* |
| `island_epidemic_v1.html` | Example 2 | Gemini | Stochastic SIRD outbreak on a hub-and-spoke island network with ferry commuting — **initial build**. |
| `island_epidemic_v2.html` | Example 2 | Gemini | As above — **bug-fix iteration**. |
| `island_epidemic_v3.html` | Example 2 | Gemini | As above — **final version from the dialogue** (visual redesign; the one referred to in the paper). Contains a ferry-scheduling fault, see [Correction](#correction-island_epidemic_v4html). *(Main-text Figure 2; Supplementary Example 2.)* |
| `island_epidemic_v4.html` | Example 2 | Version 3, corrected with Claude | **Corrected version, recommended for use.** Version 3 with the ferry-scheduling fault fixed. Not part of the published dialogue. |
| `index.html` | — | — | Landing page linking to all of the apps. |

Versions 1 to 3 of `island_epidemic` are the successive iterations described in Supplementary Example 2 (build → fix → redesign). They are kept exactly as the AI produced them, to document the *process* of AI-assisted building. `island_epidemic_v4.html` is a later correction of version 3 and is the one to use for exploring the model.

## Correction: `island_epidemic_v4.html`

After the paper's dialogue was complete, a check of version 3 found a fault in the ferry logic that the dialogue had not caught.

**What was wrong.** The routine that boards commuters each morning was meant to run once per simulated day. In version 3 it ran again on every animation frame for as long as any ferry was still waiting at its dock. With the default settings this meant that:

- every ferry sailed every day, whatever its "ferry interval" setting (South is set to every 2 days and East to every 3);
- the two daily ferries carried about 2.5 times the configured number of commuters (North about 37 instead of 15, West about 49 instead of 20).

Nothing on screen revealed this. The ferries moved and showed passenger counts, and the epidemic curves looked plausible. Version 2 has a much milder form of the same fault (double boarding on day 0 only); it became serious in version 3, the iteration that was requested as a visual redesign.

**How it was found.** By running the simulation code outside the browser and counting sailings over 30 days, which is the kind of known-answer test recommended in the paper: a ferry set to sail every 3 days should sail 10 times in 30 days, and it sailed 30 times.

**How much it mattered.** With the default settings and the infection seeded on the hub (about 1,070 simulated outbreaks per version), the fault brought the first case on each outer island forward by 1 to 4 days (median day of first case: North 6 vs 8, West 5 vs 6, South 10 vs 14, East 16 vs 18, for version 3 vs version 4). Final outbreak sizes were similar. The effect is larger when islands are weakly connected, because the ferry interval had no effect at all in version 3.

**What changed in version 4.** Two small edits, marked `v4 fix` in the source:

1. Morning boarding and the evening departure each happen exactly once per simulated day.
2. Commuters returning from the larger hub island could be drawn outside their home island's circle; they are now pulled back inside. This is a display fix only.

Nothing else in the model or the interface was changed.

**Checks run on version 4.** Sailings match each route's interval; each trip carries exactly the configured number of commuters, with nobody counted twice; the population is conserved and everyone alive is home at night; with ferry lockdown the infection never leaves the seed island; with a transmission rate of zero nobody beyond patient zero is infected; with mortality set to zero nobody dies; with perfect screening no symptomatic infected person boards; the infection probability matches 1 − exp(−β I/N); and the ferry schedule does not depend on the screen's frame rate. These checks cover the model logic. They are not a guarantee that no other faults remain.

**Modelling choices to be aware of (all versions).** These are choices made in the dialogue, not faults:

- Mortality is a chance of dying *per day of infection*, so the overall share of infected people who die is several times the slider value and also depends on the recovery time.
- A commuter turned back by screening is replaced by another resident, so ferries still travel full.
- Screening happens on morning departure from the outer islands only, and symptoms are present from the day of infection (there is no incubation period).

## Running the apps

**Online (recommended).** With GitHub Pages enabled for this repository, the apps run live in the browser:

- Landing page — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/>
- Predator–Prey Delay Explorer — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/predator_prey_delay_explorer.html>
- Island Disease Spread (corrected, version 4) — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/island_epidemic_v4.html>
- Island Disease Spread (version 3, as produced in the paper's dialogue) — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/island_epidemic_v3.html>

To enable Pages: repository **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `/ (root)`**. (Clicking an `.html` file in the normal GitHub file view shows its *source*, not the running app — Pages is what serves the working simulation.)

**Locally.** Download a file and open it in any modern web browser — no installation required.

**Dependencies.** `predator_prey_delay_explorer.html` is fully self-contained. The `island_epidemic_*` apps load Chart.js from a CDN, so they need an internet connection to draw their charts.

## Dialogues

The complete AI conversations that produced the apps are included for transparency:

- `example1_predator_prey_dialogue.md` / `.pdf` — ChatGPT (predator–prey)
- `example2_island_epidemic_dialogue.md` — Gemini (island epidemic)

## Citation

If you use these materials, please cite the paper *(citation to be added on acceptance)* and this archived release *(Zenodo DOI to be added once the GitHub release is archived)*.

## Licence

Released under the MIT Licence — see [`LICENSE`](LICENSE).
