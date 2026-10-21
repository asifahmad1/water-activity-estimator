# Moisture Sorption & Water Activity Estimator

By **Dr. Asif Ahmad** — Asif SaaS Studio

Category **Food Science** · Version **1.0** · MIT License

Predict equilibrium moisture and blend water activity with the standard GAB, BET and Henderson sorption isotherms — plus microbial stability bands. **100% in your browser**: no uploads, no accounts, and no data ever leaves your device.

**▶ Live on Asif SaaS Studio:** open the tools catalogue and choose *Moisture Sorption & Water Activity Estimator* (slug `water-activity-estimator`).

## Run it

Open `index.html` in any modern browser — or from this repo via any static host. No build step, no dependencies, no server.

## What it does

1. **Isotherm calculator** — GAB / BET / Henderson model selector with editable parameters (GAB: M₀, C, K; BET: M₀, C; Henderson: k, n). Two-way solving: a_w → moisture (shown on both dry and wet bases) and moisture → a_w (analytic quadratic inverses with a bisection fallback; Henderson inverse is analytic). Built-in range cautions: BET outside a_w ≈ 0.05–0.45, GAB above 0.95 and as K·a_w approaches 1.
2. **Nine food presets** — typical literature parameter sets (~25 °C), labelled in-app "typical literature values — verify for your product": wheat flour, skim milk powder, milled rice, raisins, corn starch, instant coffee, cracker/biscuit (all GAB), potato starch (BET), pasta/dried noodles (Henderson).
3. **Live isotherm chart** — canvas plot of moisture vs a_w for the current model and parameters, redrawn as you type, with your measured points overlaid in gold and the last calculated point marked.
4. **Measured-data input** — paste CSV/TSV into the box or upload a .csv / .txt file (read in-page). Tolerant parsing (comma/semicolon/tab, optional header, blank lines skipped) with invalid lines reported **by line number**, never silently dropped. Shows point count, RMSE and R² of the current model against your points.
5. **Blend equilibrium predictor** — 2–6 ingredient rows (name, mass, initial moisture % wet basis, per-row preset/model/parameters). Solves by bisection for the single equilibrium a_w at which total water is conserved, and reports each ingredient's final moisture (both bases) and whether it GAINS or LOSES water, in total grams and g per 100 g — plus the water-conservation check figure. Worked example (the app's default): 100 g flour at 12 % wb + 100 g raisins at 20 % wb → equilibrium a_w ≈ 0.73, flour gains ≈ 2.3 g.
6. **Stability & microbial bands** — any computed a_w is classified into the standard bands: <0.60 no microbial growth; 0.60–0.65 osmophilic yeasts; 0.65–0.75 xerophilic moulds; 0.75–0.80 most moulds; 0.80–0.87 most yeasts; 0.87–0.91 bacteria incl. many pathogens (*Staphylococcus aureus* can grow down to ~0.86); >0.91 most bacteria — with brief non-microbial notes (lipid oxidation slowest near the monolayer, enzymatic activity above ~0.75, texture loss).
7. **Outputs** — "Download CSV" of the current isotherm curve (a_w 0.05–0.95 in 0.05 steps, dry + wet basis) and a copy-ready text summary of calculator and blend results.

## Honest scope

- Estimates come from published model equations with *typical* parameters (~25 °C). Real products need their own measured isotherm: parameters are temperature-specific, and hysteresis is not modelled (adsorption and desorption curves differ).
- The blend prediction assumes a sealed system, full equilibration, no ingredient interactions beyond water transfer and no temperature change.
- **Not a substitute for a measured a_w** (dew-point / chilled-mirror meter) in commercial release decisions.
- BET results above its fitting range are extrapolations (flagged in-app); GAB results as K·a_w → 1 are flagged unreliable.

## Files

- `index.html` — the complete app, self-contained: all CSS and JavaScript inline, 35,940 bytes. No localStorage, no service workers, no browser permissions, no parent-window access — safe in a sandboxed iframe, and it works fully offline.
- `water-activity-estimator-test-report.md` — the full test report, honest-scope notes and the Wix listing details.
- Documentation (PDF) → `docs/water-activity-estimator-docs.pdf`

## Testing

99 automated checks run against the shipped PURE CORE — all passed. They include hand-computed anchors (GAB at M₀=5, C=10, K=0.8, a_w=0.5 → 7.2464; BET at M₀=5, C=10, a_w=0.30 → 5.79), the BET monolayer identity (at a_w = 1/(1+√C), M = M₀ exactly), Henderson at k·Mⁿ = 1 (a_w = 1 − e⁻¹), full round-trip grids for all three models, band-classifier boundaries, CSV-parser cases with line-number reporting, fit statistics (R² = 1, RMSE = 0 on exact-curve points), and blend-solver tests (identical isotherms return the pooled a_w; water-conservation residual < 1e-6; equilibrium a_w strictly between the ingredients' initial values). Full report: `water-activity-estimator-test-report.md`.

## Documentation

Complete user guide and technical documentation — the science, the three models, the presets, the blend method with a verified worked example, the stability bands, honest scope, and the tested core verbatim: `docs/water-activity-estimator-docs.pdf`.

Created by Dr. Asif Ahmad · Asif SaaS Studio · v1.0
