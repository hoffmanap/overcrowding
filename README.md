# El Paso Overcrowding: Shortage or Culture?

A data-driven check on a claim raised against the El Paso housing needs assessment: that overcrowding is a cultural pattern (multigenerational households) rather than evidence of a housing shortage.

**[→ Open the report: `index.html`](./index.html)**

## Bottom line

Overcrowding tracks **renter status, income, and household size** — not household type. Multigenerational households are common across the income spectrum and mostly live in adequately sized, single-family homes. Once size/income/tenure are controlled for, multigenerational structure adds essentially nothing to a household's odds of being overcrowded.

| Assumption | Verdict |
|---|---|
| Overcrowding is driven by multigenerational households | **FALSE** |
| Multigenerational living is a low-income cultural coping strategy | **FALSE** |
| Overcrowding concentrates among renters and low-income households | **TRUE** |
| Tighter housing markets are linked to more household doubling-up | **TRUE** |

## Data sources

- **ACS 5-Year Detail Tables** (2019–2023), tract level: occupants-per-room, median income, rent burden, tenure, vacancy — Census API (`api.census.gov`)
- **ACS 5-Year PUMS** (2019–2023), household level: `MULTG`, `NP`, `RMSP`, `HINCP`, `TEN`, `GRPIP` — Census API PUMS endpoint, for the six PUMAs covering El Paso County
- **HUD AMI income limits**, interpolated by household size, used to classify households above/below 70% AMI
- **Census Building Permits Survey**, City of El Paso, South Region place-level annual files, 2014–2023
- **2020 Decennial Census**, tract-level housing unit counts (production/stock-growth proxy)
- Derived "doubling-up" summaries (by PUMA, by poverty band, by income tercile × tenure, with residential vacancy) built from the PUMS extract above

## Files in this folder

| File | What it is |
|---|---|
| `index.html` | The report — myth-check format, four assumptions with evidence |
| `README.md` | This file |

## Key figures behind the report

- 74.1% of overcrowded households are **not** multigenerational (PUMS crosstab, n=12,932 households, weighted)
- Multigenerational share: **8.1%** above 70% AMI vs. **5.9%** below it — the opposite of what a poverty-driven-culture story predicts
- Weighted logistic regression odds ratios (overcrowded ~ multigen + below-70%-AMI + renter + workers + household size):
  - Multigenerational: **1.04×**, not significant (p=0.08; unweighted check p=0.20)
  - Below 70% AMI: **2.03×**, p<0.001
  - Renter: **3.11×**, p<0.001
- PUMA-level correlation, doubled-up household rate vs. residential vacancy rate: **r = −0.51** (n=6 PUMAs — directional, not conclusive on its own)
- City of El Paso building permits: **3,941 units (2015) → 1,852 units (2023)**, a 53% decline

## Caveats worth keeping in view

- The vacancy/doubling-up correlation is based on only 6 PUMAs — treat it as consistent with the rest of the evidence, not as standalone proof.
- Logistic regression standard errors use PUMS household weights (`WGTP`) as frequency weights, which overstates precision relative to the Census Bureau's full replicate-weight design. An unweighted robustness check confirmed the same direction and significance pattern for every variable.
- `has_roommate`, `has_unmarried_partner`, and `has_grandchild_or_parent` in the full household-profile extract were found to be uniformly `False` for every record — a bug in that file's feature engineering, not used in this analysis.
- Census's `B11017` (Multigenerational Households) table returned null at tract level for El Paso County in this ACS vintage via the API — likely a genuine small-area data-suppression gap rather than a request error. Tract-level multigenerational estimates in this analysis use a PUMA-level proxy (assigned via spatial join) rather than true tract-level Census figures.
- Overcrowded = more than 1 person per room, the standard Census/HUD threshold. Multigenerational follows Census's own `MULTG` definition (3+ generations under one roof).

## Reproducing this analysis

The Census API pull scripts (tract-level ACS tables, PUMS household crosstabs, building permits, tract-to-city and tract-to-PUMA spatial joins) are available on request — this repo contains only the finished data story. Each requires a free Census API key (`api.census.gov/data/key_signup.html`) and, for the spatial joins, `pygris` + `geopandas`.
