# Data dictionary

Eight Gold tables, stored as Delta tables in the `lh_gold` lakehouse (schema `dbo`) and read by the Direct Lake semantic model. Sample data for the small tables and a monthly summary of the large one are in [`data/`](../data).

## Relationships

| From (many) | To (one) | Cardinality | Direction |
|---|---|---|---|
| `fact_uae_solar_daily[plant_id]` | `dim_uae_plant[plant_id]` | many to one | single |
| `fact_uae_maintenance[plant_id]` | `dim_uae_plant[plant_id]` | many to one | single |
| `fact_uae_solar_daily[date]` | `dim_uae_date[date]` | many to one | single |
| `fact_uae_maintenance[event_date]` | `dim_uae_date[date]` | many to one | single |

`dim_assumptions`, `dim_carbon_factor`, `dim_recommendation_rule` and `security_user_plant_access` are intentionally not connected (they are configuration, scenarios and thresholds, not facts).

## `fact_uae_solar_daily` (1,098 rows: 3 plants x 366 days)

| Column | Type | Description |
|---|---|---|
| `date` | date | Calendar day, 1 Jan to 31 Dec 2024 |
| `plant_id`, `plant_name`, `capacity_kw`, `scenario_type` | text / int | Plant attributes copied onto the fact |
| `solar_irradiation_kwh_m2_day` | decimal | NASA POWER all-sky surface shortwave irradiation (real) |
| `air_temperature_c` | decimal | NASA POWER 2 m air temperature (real) |
| `wind_speed_m_s` | decimal | NASA POWER 2 m wind speed (real) |
| `relative_humidity_pct` | decimal | NASA POWER 2 m relative humidity (real) |
| `location_name`, `latitude`, `longitude`, `source_system` | text / decimal | `Dubai_UAE_Scenario`, 24.9857, 55.1761, `NASA_POWER` |
| `expected_energy_kwh` | decimal | Weather-based expected energy (simulated rule) |
| `days_since_cleaning` | int | Days since the last cleaning work order |
| `soiling_loss_rate` | decimal | min(15%, 0.2% x days since cleaning) |
| `outage_downtime_hours` | decimal | Outage hours on the day |
| `base_loss_kwh`, `soiling_loss_kwh`, `outage_loss_kwh` | decimal | Loss by cause |
| `energy_loss_kwh` | decimal | Sum of the three losses |
| `simulated_actual_energy_kwh` | decimal | Expected minus loss |
| `estimated_loss_aed` | decimal | Loss x AED 0.30 per kWh (illustrative) |
| `performance_vs_expected_pct` | decimal | Actual as a percentage of expected |
| `data_classification` | text | Marks the row as simulated |

## `fact_uae_maintenance` (89 rows, one per work order)

| Column | Type | Description |
|---|---|---|
| `work_order_id` | text | `WO-00001` to `WO-00089` |
| `plant_id` | text | Plant the work was done on |
| `event_date` | date | 14 Jan to 29 Dec 2024 |
| `event_type` | text | `Cleaning` or `Inverter Outage` |
| `downtime_hours` | decimal | 0 for cleanings, 2 for outages |
| `status` | text | `Completed` |
| `data_classification` | text | `SIMULATED` |

## Dimensions and configuration

| Table | Rows | Columns |
|---|---|---|
| `dim_uae_plant` | 3 | `plant_id`, `plant_name`, `capacity_kw`, `scenario_type`, `cleaning_interval_days`, `outage_frequency_days` |
| `dim_uae_date` | 366 | `date`, `year`, `quarter`, `month`, `month_name`, `month_short`, `day`, `date_key`, `year_month`, `year_month_sort`, `day_of_week`, `day_name` |
| `dim_assumptions` | 9 | `assumption_name`, `value`, `unit`, `source`, `classification` |
| `dim_carbon_factor` | 3 | `carbon_factor_id`, `factor_name`, `region`, `reporting_year`, `kg_co2e_per_kwh`, `source_name`, `source_url`, `methodology`, `verification_status`, `scenario_label` |
| `dim_recommendation_rule` | 3 | `rule_id`, `rule_name`, `metric_name`, `threshold_value`, `comparison_operator`, `priority`, `recommended_action`, `rule_version`, `classification` |
| `security_user_plant_access` | 6 | `user_email`, `plant_id`, `access_role`, `is_active` (designed for row-level security; not deployed; contents not published) |

## Data classification

Each table or row carries a classification: `SIMULATED` (plants, generation, maintenance, tariff), `PUBLISHED_2025_SCENARIO` and `PROVISIONAL` (carbon factors), `Illustrative` (advisor rules). Weather columns come from NASA POWER and are real observations.
