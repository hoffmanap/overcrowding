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
| Tight, unaffordable housing markets track higher overcrowding (not just doubling-up) | **TRUE** |

## Data sources

- **ACS 5-Year Detail Tables** (2019–2023), tract level: occupants-per-room, median income, rent burden, tenure, vacancy — Census API (`api.census.gov`)
- **ACS 5-Year PUMS** (2019–2023), household level: `MULTG`, `NP`, `RMSP`, `HINCP`, `TEN`, `GRPIP` — Census API PUMS endpoint, for the six PUMAs covering El Paso County
- **HUD AMI income limits**, interpolated by household size, used to classify households above/below 70% AMI
- **Census Building Permits Survey**, City of El Paso, South Region place-level annual files, 2014–2023
- **2020 Decennial Census**, tract-level housing unit counts (production/stock-growth proxy)
- Derived "doubling-up" summaries (by PUMA, by poverty band, by income tercile × tenure, with residential vacancy) built from the PUMS extract above
- **13-metro comparison**: doubling-up rate, multigenerational share, and median household income (ACS PUMS) matched against Zillow ZORI (rent) and ZHVI (home value) indices, June 2026
- **13-metro overcrowding/vacancy panel**: household-level PUMS pull (Houston excluded — data collection timed out) computing actual overcrowded rate, vacancy rate, and doubled-up rate from the same sample for each metro, split by income-to-poverty ratio (`POVPIP`)

## Files in this folder

| File | What it is |
|---|---|
| `index.html` | The report — myth-check format, four assumptions with evidence |
| `README.md` | This file |

## Key figures behind the report

- 74.1% of overcrowded households are **not** multigenerational (PUMS crosstab, n=12,932 households, weighted)
- Multigenerational share: **8.1%** above 70% AMI vs. **5.9%** below it — the opposite of what a poverty-driven-culture story predicts
- Why: multigenerational households average **1.94 workers** per household vs. **1.19** for everyone else — more earners under one roof, which is also why only 34.3% of them fall below 70% AMI vs. 42.2% of non-multigenerational households
- 80.5% of multigenerational households live in single-family homes (not a symptom of being squeezed into small units)
- Weighted logistic regression odds ratios (overcrowded ~ multigen + below-70%-AMI + renter + workers + household size):
  - Multigenerational: **1.04×**, not significant (p=0.08; unweighted check p=0.20)
  - Below 70% AMI: **2.03×**, p<0.001
  - Renter: **3.11×**, p<0.001
- PUMA-level correlation, doubled-up household rate vs. residential vacancy rate: **r = −0.51** (n=6 PUMAs — directional, not conclusive on its own)
- City of El Paso building permits: **3,941 units (2015) → 1,852 units (2023)**, a 53% decline
- Across 13 comparison metros (Houston excluded — data collection timed out), overcrowded rate correlates strongly with doubled-up rate (**r = 0.84**) but only weakly and non-significantly with raw metro-level vacancy rate (**r = 0.25**) — the metro-level relationship runs through affordability, not raw vacant-unit counts
- In **13 of 13** metros tested, the highest income band (400%+ of the poverty line) has the lowest overcrowding rate, with no exceptions — e.g., El Paso 10.3% → 1.9%, Fresno 17.8% → 3.3%, Los Angeles 16.4% → 4.3%. In a few high-cost coastal metros (LA, San Francisco, Honolulu), households just above the poverty line are sometimes *more* crowded than those below it — plausibly because deep poverty carries better access to subsidized housing than the near-poor get
- El Paso itself lands in the middle of this 13-metro group on both overcrowding (5.7%) and vacancy (7.6%) — not an outlier in either direction

## Caveats worth keeping in view

- The vacancy/doubling-up correlation is based on only 6 PUMAs — treat it as consistent with the rest of the evidence, not as standalone proof.
- Logistic regression standard errors use PUMS household weights (`WGTP`) as frequency weights, which overstates precision relative to the Census Bureau's full replicate-weight design. An unweighted robustness check confirmed the same direction and significance pattern for every variable.
- `has_roommate`, `has_unmarried_partner`, and `has_grandchild_or_parent` in the full household-profile extract were found to be uniformly `False` for every record — a bug in that file's feature engineering, not used in this analysis.
- Census's `B11017` (Multigenerational Households) table returned null at tract level for El Paso County in this ACS vintage via the API — likely a genuine small-area data-suppression gap rather than a request error. Tract-level multigenerational estimates in this analysis use a PUMA-level proxy (assigned via spatial join) rather than true tract-level Census figures.
- Overcrowded = more than 1 person per room, the standard Census/HUD threshold. Multigenerational follows Census's own `MULTG` definition (3+ generations under one roof).

## Reproducing this analysis

The Census API pull scripts (tract-level ACS tables, PUMS household crosstabs, building permits, tract-to-city and tract-to-PUMA spatial joins) are available on request — this repo contains only the finished data story. Each requires a free Census API key (`api.census.gov/data/key_signup.html`) and, for the spatial joins, `pygris` + `geopandas`.
