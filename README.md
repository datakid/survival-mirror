# Survival Mirror — v7

A static, offline-capable mortality calculator. It shows population-level survival curves from age, sex, country (236 life tables) and ten lifestyle and health questions. Every modeling choice and limitation is shown next to the numbers.

## What's new in this version

### More honest
- **"Not sure" on every question, and it's the default.** It counts as the population average (so it doesn't move the curve), and the Monte Carlo draws a random answer from the population mix for that question, which widens the uncertainty band. Earlier versions quietly pre-filled answers like "overweight", "no activity" and "good health".
- **The period-table assumption is disclosed.** Results assume 2023 death rates stay fixed for life, so they lean conservative. This is now explained in the results card, the limitations list and the modeling choices.
- **New "Three things to keep in mind" callout** on the results card: frozen death rates, how much country choice matters compared with lifestyle, and what the uncertainty range does and doesn't cover.
- **The headline is reframed.** Ages show as whole years (no false precision), and the copy reads "half of a group like you still alive at…" instead of "median age". The middle 80% of outcomes is shown next to the median.
- **The frequency sentence is rewritten** so it describes a hypothetical group living under fixed rates, and says plainly that no one in that group is you.
- **Ledger additivity note.** The marginal effects don't sum to the total change, and the ledger now shows both numbers and explains why.
- New limitations cover habits that change over time and period-vs-cohort tables.

### Better UI and UX
- Three fact chips in the hero: local-only, about three minutes, never validated.
- An "X of 10 answered" counter on the questionnaire.
- "Not sure" pills have their own dashed style.
- A chart legend, plus markers on the curve for "9 in 10 alive" and "1 in 10 alive".
- An "Average years remaining" item in the comparison row.
- A **Print or save as PDF** button. It finishes the animation, opens every methodology section and uses a clean print stylesheet that hides the form.
- The copied results summary now includes the honesty notes and a disclaimer.
- "Wipe everything" turns red on hover so it's clearly destructive.

### Round 2
- **"How much the judgment calls matter" panel.** It recomputes the median with one assumption changed at a time:
  - death rates falling 1% or 2% a year;
  - effects of your answers fading to 20% or 60% by age 90, or not fading at all;
  - no overlap discount, or self-rated health counted in full.
  
  It also reports the total spread caused by assumptions alone.
- **Import answers.** An exported JSON file can be loaded back in. It's checked with the same `validateState` rules, and nothing changes if the file is invalid.
- **Bulk results show a "Not sure" count per row**, both on screen and in the downloaded .xlsx file. There's also a note that bulk ages are rounded to one decimal only for sorting.
- 3 new self-tests, for 20 in total.

### v5 — country population mix + assumption sliders
- **Country-specific population averages.** Answers are now measured against the people already inside the country's life table, for 5 of the 10 questions. The data is by sex, for 201 countries:

  | Question | Source | Year | What the country data sets |
  |---|---|---|---|
  | Smoking | WHO GHO `M_Est_smk_curr_std` | 2022 | the current-vs-not split |
  | Alcohol | WHO GHO `SA_0000001411` | 2020 | the abstainer share |
  | Activity | WHO GHO `NCD_PAA`, adults 18+ | 2022 | the share insufficiently active |
  | BMI | NCD-RisC (Lancet 2024), age-standardised | 2022 | the full 7-band distribution, collapsed to 5 bands |
  | Diabetes | NCD-RisC (Lancet 2024), crude | 2022 | the diabetes share inside cardiometabolic history |

  - The splits inside each country figure stay global (for example, light vs. heavy smokers), and the remaining 5 questions stay global. All of this is disclosed per country next to the country picker and in the limitations list.
  - The mapping judgment calls are listed under "Modeling choices I made up": the 150-minute activity line, the 60/40 split of the 20–25 BMI band, and how diabetes is spread across the four diabetes answers.
  - All-"Not sure" still reproduces the raw life table exactly.
- **Not used:** heavy-episodic drinking (`SA_0000001739`) and per-capita litres (`SA_0000001822`), because there's no defensible mapping onto the alcohol bands. Also unused: diabetes age-standardised (crude matches the life tables' real age mix better), the age-group activity export (`data.xlsx`), and the GBD guide (it holds no data).
- **"Try your own assumptions" sandbox.** Sliders for a yearly fall in death rates (0–2.5%), how strong your answers' effects still are at age 90 (0–100%), and each overlap discount, plus a switch between the country mix and one global mix.
  - It shows the median, the difference, the middle 80% and an overlaid curve.
  - It's clearly labeled as exploration. The headline never changes, nothing is saved, and it isn't printed.
- **Sensitivity panel** gains a "global mix instead of country mix" row.
- **Self-tests: 26.** They include a data checksum, a check that every distribution is valid, WHO spot checks, an all-Not-sure check across countries, and a check that the explorer at default settings matches the headline.
- `tools/build-country-mix.html` rebuilds the embedded table from the raw files. The raw files aren't committed, to keep the deploy small.

### v6 — age-specific activity comparison
- From age 60, the activity answer is compared with WHO's own 2022 figures for that person's age band (60–69, 70–79, 80+), by sex, for 196 countries. The source is WHO GHO `NCD_PAA` by age group, which is the `data.xlsx` export.
- WHO publishes no country bands under 60, so under 60 the all-adult figure is still used. This is disclosed in the citations, the modeling choices and the limitations list.
- The data has its own checksum test. With every answer "Not sure", the result at age 82 still equals the life table.
- The importer recognises WHO's official country names (Viet Nam, Türkiye, Russian Federation, and so on), for bulk sheets too.
- `tools/build-activity-age.html` rebuilds the table. It uses `data.xlsx`, which isn't committed.
- Self-tests: 27.

### v7 — age-specific diabetes comparison (final data round)
- The diabetes share inside "cardiometabolic history" is now compared with the user's own 10-year age band (18–29, 30–39, …, 70–79, 80+), by sex, for 199 countries and territories. The source is NCD-RisC (Lancet 2016), age-specific prevalence, **2014** (the latest year in that file).
  - The 5-year bands are averaged into 10-year bands without population weights.
  - If a country isn't in the table, it falls back to the 2022 all-adult crude figure.
  - This data is 8 years older than the 2022 figures. This is disclosed in the citations and the modeling choices.
- Country aliases were added for NCD-RisC names (Guinea Bissau, Lao PDR, Macedonia (TFYR), North Korea, China (Hong Kong SAR), Occupied Palestinian Territory). Nauru has no life table, so it's skipped.
- The data has its own checksum test (398664). The test spot-checks USA men at 18–29 (1.0%) and 70–79 (20.6%), confirms the cache is keyed by age band and that stroke and heart-attack shares stay global, and checks that all-"Not sure" at 75 still equals the life table.
- `tools/build-diabetes-age.html` rebuilds the table from `diab_m.csv` and `diab_f.csv`, which aren't committed.
- Self-tests: 28.
- **Supplied but not usable:**
  - The GBD 2023 Results export contains only *Global*, both sexes, deaths and DALYs attributable to 18 dietary risks. It has no country data and no disease prevalence, so it can't set country stroke, heart-attack, kidney or COPD mixes.
  - The NCD-RisC 2024 age-specific BMI files are about 63 MB each, over this build environment's 20 MB fetch limit. BMI therefore stays age-standardised.

## Entry points
- `index.html` — the app
- `index.html?selftest=1` — in-page self-test harness (28 checks)
- `flowtest.html` — automated flow check (gate → calculate → animation end state)

## Files
- `index.html` — the whole app (HTML, CSS, JS inline; strict CSP with `connect-src 'none'`)
- `xlsx.full.min.js` — SheetJS CE 0.20.3, served from the same origin, used only by bulk mode
- `flowtest.html` — test harness

## Data and storage
- Life tables: SSA 2023 (US), UN WPP 2024 (all other countries). They're embedded in the page.
- Answers are saved in `localStorage` under the key `survivalMirror.v3`, using schema v4. Older saves still load.
- Bulk uploads stay in memory only. Bulk sheets accept `Not sure` / `unknown` / `don't know` as answers.
- No tables or backend are used.

## Not yet implemented / ideas
- Age-specific BMI. The NCD-RisC 2024 age-specific files exist, but they need to be pre-filtered to the latest year (for example with a spreadsheet) before `tools/` can process them.
- Age-specific smoking (no source supplied).
- Country figures for stroke, heart attack, kidney disease and COPD. This needs a GBD Results export of *prevalence* by country and sex; the supplied export holds global diet-attributable burden only.
- Newer age-specific diabetes data, if a more recent age-specific file than 2014 is available.
- Feed the source files' uncertainty ranges into the "how sure" simulation. This was deliberately not done: only point estimates are embedded, and adding it would mean re-embedding interval data for every figure.
- Localization.