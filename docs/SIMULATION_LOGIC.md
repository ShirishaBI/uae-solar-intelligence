# Simulation logic: how the plant data is generated

The weather is real (NASA POWER, UAE 2024). Everything else about the plants is simulated from one weather series and a small set of documented assumptions. Every rule below was **tested against all 1,098 plant-day rows of the Gold fact table; every column matched with zero error.**

## Assumptions (`dim_assumptions`)

| Parameter | Value | Meaning |
|---|---|---|
| `baseline_efficiency` | 0.80 | Share of irradiation converted to expected energy |
| `base_loss_rate` | 0.02 | Constant baseline loss, 2% of expected energy |
| `soiling_daily_increase` | 0.002 per day | Soiling grows 0.2 percentage points per day since the last cleaning |
| `soiling_cap` | 0.15 | Soiling loss never exceeds 15% |
| `outage_loss_per_downtime_hour` | 0.04 per hour | Each outage hour costs 4% of the day's expected energy |
| `tariff_aed_per_kwh` | 0.30 | Illustrative tariff to convert lost energy to AED |

All six are classified `SIMULATED`. The tariff is not a contract price.

## Plant scenarios (`dim_uae_plant`)

| Plant | Capacity | Scenario | Cleaning interval | Outage every |
|---|---|---|---|---|
| A | 10 MW | Normal | 14 days | 90 days |
| B | 15 MW | Soiling | 30 days | 60 days |
| C | 20 MW | Outage | 21 days | 15 days |

The scenarios are why the plants behave differently: Plant B is cleaned least often (soiling builds up), and Plant C has the most frequent outages.

## Daily calculation (`fact_uae_solar_daily`, one row per plant and day)

```
expected_energy_kwh   = solar_irradiation_kwh_m2_day x capacity_kw x 0.80
base_loss_kwh         = expected_energy_kwh x 0.02
soiling_loss_rate     = min(0.15, 0.002 x days_since_cleaning)
soiling_loss_kwh      = expected_energy_kwh x soiling_loss_rate
outage_loss_kwh       = expected_energy_kwh x 0.04 x outage_downtime_hours
energy_loss_kwh       = base_loss_kwh + soiling_loss_kwh + outage_loss_kwh
simulated_actual_kwh  = expected_energy_kwh - energy_loss_kwh
estimated_loss_aed    = energy_loss_kwh x 0.30
performance_vs_expected_pct = simulated_actual_kwh / expected_energy_kwh x 100
```

`days_since_cleaning` comes from the maintenance schedule: it resets to 0 on a cleaning work order and increases by one each day.

### Worked example: Plant A, 1 June 2024

| Step | Value |
|---|---|
| Irradiation | 7.9013 kWh/m2/day |
| Expected energy | 7.9013 x 10,000 kW x 0.80 = 63,210.4 kWh |
| Baseline loss | 2% = 1,264.2 kWh |
| Days since cleaning | 13, so soiling rate = 2.6% |
| Soiling loss | 63,210.4 x 0.026 = 1,643.5 kWh |
| Outage loss | 0 (no outage hours) |
| Total loss | 2,907.7 kWh |
| Simulated actual | 60,302.7 kWh (95.4% of expected) |
| Loss in AED | 2,907.7 x 0.30 = AED 872.3 |

## Maintenance records (`fact_uae_maintenance`)

89 simulated work orders across 2024, all `Completed`:

| Plant | Work orders | Cleanings | Inverter outages (2 h each) |
|---|---|---|---|
| A | 30 | 26 | 4 |
| B | 18 | 12 | 6 |
| C | 41 | 17 | 24 |
| Total | 89 | 55 | 34 |

Totals match the Operations page: 89 work orders, 55 cleanings, 34 outages, 68 hours of downtime (34 outages x 2 h). The full table is in [`data/fact_uae_maintenance.csv`](../data/fact_uae_maintenance.csv).

## Carbon scenarios (`dim_carbon_factor`)

| Factor | Region | kg CO2e per kWh | Status |
|---|---|---|---|
| DEWA | Dubai | 0.3833 | Unverified |
| EWEC | Abu Dhabi | 0.1870 | Provisional |
| Ember | UAE | 0.4697 | Provisional |

Avoided or lost emissions are the lost energy multiplied by the chosen factor. No source URL is recorded, so every carbon figure is labelled provisional. The scenario view in the dashboard compares the same generation under the three factors.

## Advisor rules (`dim_recommendation_rule`)

| Rule | Metric | Test | Priority | Suggested action |
|---|---|---|---|---|
| R001 High soiling loss | soiling loss rate | > 0.10 | High | Inspect panels and evaluate cleaning |
| R002 High outage downtime | outage downtime hours | >= 2 | High | Investigate outage and review recovery |
| R003 Low solar performance | performance vs expected % | < 85 | Medium | Investigate soiling, outages and weather |

These rules run over simulated history. They are not live alerts.

## Why the dashboard results look the way they do

- Plant C has the largest total loss because it is the largest plant (20 MW) with frequent outages.
- Plant B has the largest loss per MW because its soiling scenario lets the soiling rate climb for 30 days between cleanings.
- Soiling explains about 48% of the gap because it grows every day until the next cleaning, while the baseline loss is a flat 2%.
- The pattern is a property of the simulation rules, so the dashboard shows how a decision process works, not real plant behaviour.
