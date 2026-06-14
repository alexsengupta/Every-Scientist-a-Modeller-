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
| `island_epidemic_v3.html` | Example 2 | Gemini | As above — **final version** (visual redesign; the one referred to in the paper). *(Main-text Figure 2; Supplementary Example 2.)* |
| `index.html` | — | — | Landing page linking to all of the apps. |

The three `island_epidemic` files are the successive iterations described in Supplementary Example 2 (build → fix → redesign). They are kept to document the *process* of AI-assisted building; `island_epidemic_v3.html` is the definitive version.

## Running the apps

**Online (recommended).** With GitHub Pages enabled for this repository, the apps run live in the browser:

- Landing page — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/>
- Predator–Prey Delay Explorer — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/predator_prey_delay_explorer.html>
- Island Disease Spread (final) — <https://alexsengupta.github.io/Every-Scientist-a-Modeller-/island_epidemic_v3.html>

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
