# Data

Simulated and public data behind the dashboard. Weather is real (NASA POWER); everything else is simulated or illustrative. Nothing here is audited or live.

| File | Rows | Content |
|---|---|---|
| `dim_uae_plant.csv` | 3 | Plants A, B, C with capacity, scenario type, cleaning and outage cadence |
| `dim_assumptions.csv` | 9 | Simulation parameters, tariff and grid emission factors |
| `dim_carbon_factor.csv` | 3 | DEWA, EWEC and Ember scenarios (provisional) |
| `dim_recommendation_rule.csv` | 3 | Advisor rule thresholds |
| `fact_uae_maintenance.csv` | 89 | Simulated cleaning and outage work orders |
| `monthly_summary_by_plant.csv` | 36 | Plant-by-month totals from the 1,098-row daily fact: average weather, expected, loss by cause, actual energy and AED loss |

The daily fact table (1,098 rows) is not published. It can be recomputed from the weather series and the rules in [`docs/SIMULATION_LOGIC.md`](../docs/SIMULATION_LOGIC.md).

## Reconciliation with the dashboard

Summing `monthly_summary_by_plant.csv` gives the dashboard's headline figures: expected 76,920.6 MWh, simulated actual 73,507.4 MWh, estimated loss AED 1,023,951 (Plant A 173,701, Plant B 386,257, Plant C 463,993). The AED figures use an illustrative tariff of AED 0.30 per kWh.

## Licence note

Weather values derive from NASA POWER (public data, power.larc.nasa.gov). Simulated values are created for this case study.
