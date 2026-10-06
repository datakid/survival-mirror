# Survival Mirror — v4

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

## Entry points
- `index.html` — the app
- `index.html?selftest=1` — in-page self-test harness (20 checks)
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
- Country-specific prevalence weights for the population-average baseline.
- Sensitivity sliders for the age-attenuation floor and the shrinkage lambdas.
- Localization.

## Deploy
Use the **Publish tab**. Keep `xlsx.full.min.js` next to `index.html`.
