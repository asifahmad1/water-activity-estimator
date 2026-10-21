# Moisture Sorption & Water Activity Estimator v1.0 — Test Report

**What it is:** A single-file browser app for the Asif SaaS Studio catalogue that predicts the stability and microbial vulnerability of shelf-stable foods. It uses the standard GAB, BET and Henderson sorption isotherm models to (a) convert between water activity (a_w) and equilibrium moisture content, (b) overlay and score a user's own measured isotherm data, and (c) predict the final equilibrium a_w when dry and semi-moist ingredients are blended in a sealed system. Target users: food science students, small producers and QA staff. By Dr. Asif Ahmad.

## Features

1. **Isotherm calculator** — GAB / BET / Henderson model selector with editable parameters (GAB: M₀, C, K; BET: M₀, C; Henderson: k, n). Two-way solving: a_w → moisture (shown on both dry basis and wet basis) and moisture → a_w (GAB/BET inverses solved analytically via the quadratic form, with a-verified-result fallback to bisection at 1e-9 tolerance; Henderson inverse is analytic). Range cautions: GAB warns above a_w 0.95 and as K·a_w approaches 1 (warning at K·a_w ≥ 0.99, caution ≥ 0.95); BET cautions outside a_w ≈ 0.05–0.45. Includes a wet-basis % ↔ dry-basis g/100 g converter.
2. **Presets** — 9 typical literature parameter sets (~25 °C), labelled in-app "typical literature values — verify for your product": wheat flour, skim milk powder, milled rice, raisins, corn starch, instant coffee, cracker/biscuit (all GAB), potato starch (BET), pasta/dried noodles (Henderson). Loading a preset fills the model and parameter fields.
3. **Isotherm chart** — canvas plot of moisture vs a_w (0–1) for the current model/parameters, with grid and axis labels; redraws live on any parameter change; overlays user data points (gold) and the last calculated point (red).
4. **Measured-data input** — paste CSV/TSV into a textarea or upload a .csv/.txt file (FileReader, parsed in-page). Tolerant parsing: comma/semicolon/tab separators, optional header row, blank lines skipped; invalid lines are reported with their line numbers, never silently dropped. Shows point count, RMSE and R² of the current model against the points; "Clear data" button.
5. **Blend equilibrium predictor** — 2–6 dynamic ingredient rows (name, mass g, initial moisture % wet basis, per-row preset dropdown + model + editable parameters). Solves by bisection on a_w ∈ (0.001, 0.999) for the point where total water is conserved across all isotherms. Reports: predicted equilibrium a_w, each ingredient's initial a_w, final moisture (dry and wet basis), whether it GAINS or LOSES water (total g and g per 100 g ingredient), and the water-conservation check figure.
6. **Stability & microbial interpretation** — shared band component shown for calculator and blend results, with the standard thresholds: <0.60 no microbial growth (most stable); 0.60–0.65 osmophilic yeasts; 0.65–0.75 xerophilic moulds; 0.75–0.80 most moulds; 0.80–0.87 most yeasts; 0.87–0.91 bacteria incl. many pathogens (with the note that *Staphylococcus aureus* can grow down to ~0.86); >0.91 most bacteria. Plus brief non-microbial notes (lipid oxidation slowest near the monolayer, enzymatic activity above ~0.75, texture loss).
7. **Outputs** — "Copy summary" (accumulating copy-ready text area, with clipboard + manual fallback) and "Download CSV" of the current isotherm curve (a_w 0.05–0.95 in 0.05 steps, moisture dry basis + wet basis).

## Honest scope

Estimates come from published model equations with *typical* parameters (~25 °C). Real products need their own measured isotherm: parameters are temperature-specific, and hysteresis is not modelled (adsorption and desorption curves differ). The blend prediction assumes a sealed system, full equilibration, no ingredient interactions beyond water transfer and no temperature change. The app is **not** a substitute for a measured a_w (dew-point / chilled-mirror meter) in commercial release decisions. No backend features are needed or faked; everything runs in-page — no server, no storage, no API keys, no data leaves the browser.

## File

- `~/workspace/your_files/water-activity-estimator/index.html` — **35,940 bytes** (limit 150 KB), all CSS/JS inline, no external assets, no localStorage/service workers/permissions/parent-window access. Responsive layout, labelled inputs, inline validation errors.

## How to run

Open `index.html` in any modern browser (double-click, or upload the file's contents into the Wix CMS `appHtml` field — see below). No build step, no server.

## Testing

**Method:** the app's PURE CORE block (model functions, inverses, basis converters, blend solver, band classifier, CSV parser, RMSE/R²) was extracted and run in Node against independently hand-computed expectations — not by calling the same formula twice. Hand checks include literal arithmetic (e.g. GAB at M₀=5, C=10, K=0.8, a_w=0.5 → 20/2.76 = 7.2464), the known BET monolayer identity (at a_w = 1/(1+√C), M = M₀ exactly), and Henderson at k·Mⁿ = 1 (a_w = 1 − e⁻¹). The full inline `<script>` was also extracted and syntax-checked with `node --check` — **OK**.

**Results: 99 / 99 checks passed.**

| Group | Checks | Result |
|---|---|---|
| GAB forward (2 hand-computed values) | 2 | ✅ |
| BET forward + monolayer identity (C=16, C=9) | 3 | ✅ |
| Henderson forward/inverse at 1 − e⁻¹ | 2 | ✅ |
| GAB inverse round-trip a_w→M→a_w, grid 0.05–0.95 (tol 1e-4) | 19 | ✅ |
| BET inverse round-trip, grid 0.05–0.45 | 9 | ✅ |
| Henderson round-trips (both directions), grid 0.1–0.9 | 18 | ✅ |
| GAB/BET inverse vs hand values; M=0 → a_w 0 | 3 | ✅ |
| Basis conversions (20 % wb = 25 db, round-trip, zero) | 4 | ✅ |
| Band classifier boundaries (0.599/0.60/0.65/0.75/0.80/0.86/0.87/0.909/0.91/0.99) | 11 | ✅ |
| CSV parser (header, semicolons, tabs, blanks, bad-line numbers, out-of-range, single column, empty, negative, spaces) | 10 | ✅ |
| fitStats (R²=1 and RMSE=0 on exact curve; hand RMSE/R² case) | 5 | ✅ |
| Blend solver (identical isotherms = pooled a_w; gain/loss directions; water residual < 1e-6) | 6 | ✅ |
| Blend flour+raisin (equilibrium a_w strictly between initial a_w values; residual; total water 39 g; direction vs a_w ordering) | 5 | ✅ |
| Blend 3-ingredient mixed models (residual < 1e-6; a_w in range) | 2 | ✅ |

## Known limitations

- Preset parameters are *typical* published-style values, not measurements of any specific commercial product; results are estimates for teaching/screening.
- Models are single-temperature (~25 °C); no temperature correction, no hysteresis.
- BET inverse above its fitting range is an extrapolation (flagged in-app); GAB results as K·a_w → 1 are flagged unreliable.
- Blend model assumes ideal water-only transfer between ingredients (no solute migration, no fat barriers, full equilibration time allowed).
- The chart's y-axis auto-scales to the visible curve/points; extremely steep high-a_w GAB curves are clipped at the top of the plot area.

## Wix upload steps

1. In the Wix dashboard, open the CMS collection **SaasProducts**.
2. Edit (or add) the item with slug `water-activity-estimator`.
3. Paste the full contents of `index.html` into the **appHtml** field; set Title, Category (Food Science), Version 1.0, Published = on.
4. Save, open the live app page, and run the smoke test: set model = BET with M₀=5, C=10 and calculate moisture at a_w 0.30 → expect ≈ 5.79 g/100 g dry solids; load the wheat flour preset and solve a_w from moisture 10 → expect ≈ 0.49; paste the 3-line sample data from the textarea placeholder and plot it; then run the default flour (12 % wb) + raisins (20 % wb) blend → expect equilibrium a_w ≈ 0.73, with the flour gaining ≈ 2.3 g of water and the raisins losing the same amount.

## Listing

- **Title:** Moisture Sorption & Water Activity Estimator
- **Description:** Predict equilibrium moisture and blend water activity with GAB, BET and Henderson isotherms — plus microbial stability bands.
- **Category:** Food Science
- **Slug:** `water-activity-estimator`
- **Version:** 1.0
