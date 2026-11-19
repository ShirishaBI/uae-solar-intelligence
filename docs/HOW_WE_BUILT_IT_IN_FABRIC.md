# How it was built, start to end

An ordered walk-through of the project in Microsoft Fabric and Power BI, from an empty workspace to a published four-page report. Items are named exactly as they appear in the workspace `uae-solar-dev`.

> **Evidence note.** The definitions of `pl_02_ingest_nasa_power_weather`, `df_01_nasa_power_bronze_to_silver` and `df_02_uae_solar_gold_model` are not exported to this repository. Their roles in Steps 2 to 4 come from the item names, the notebook code and the verified contents of the Gold tables.

## Step 1. Workspace and storage

1. Create a Fabric workspace, `uae-solar-dev`, on a Fabric trial capacity.
2. Create three lakehouses in it: `lh_bronze` (raw), `lh_silver` (cleaned), `lh_gold` (reporting tables). Each lakehouse also gets a SQL analytics endpoint automatically.

## Step 2. Ingest the weather (Bronze)

1. Build a Data Factory pipeline, `pl_02_ingest_nasa_power_weather`.
2. It pulls NASA POWER daily data for the UAE for 2024 (all-sky irradiation, 2 m air temperature, 2 m wind speed, 2 m relative humidity) and stores the unchanged JSON response in `lh_bronze` at `Files/nasa_power/raw/nasa_power_uae_2024.json`.
3. Keeping the raw file untouched means every later step can be rerun from it.

## Step 3. Clean the weather (Silver)

The notebook `nb_06_nasa_power_silver_transformation` (in [`fabric/notebooks`](../fabric/notebooks)):

1. Reads the raw JSON from Bronze and stops with an error if the file is missing.
2. Extracts the daily parameters and converts the date from `YYYYMMDD` to a real date.
3. Renames the fields to clear names with units: `solar_irradiation_kwh_m2_day`, `air_temperature_c`, `wind_speed_m_s`, `relative_humidity_pct`.
4. Adds `location_name` (`Dubai_UAE_Scenario`), latitude 24.9857, longitude 55.1761 and `source_system` (`NASA_POWER`).
5. Validates: row count, column list, duplicate dates, missing values.

A Dataflow Gen2, `df_01_nasa_power_bronze_to_silver`, covers the same Bronze-to-Silver step in Power Query.

## Step 4. Build the Gold tables

The Dataflow Gen2 `df_02_uae_solar_gold_model` produces the reporting tables in `lh_gold` (schema `dbo`). The dataflow's Power Query steps are not exported to this repository, so the logic is documented from its verified output:

1. Define the three plants and their scenarios: [`data/dim_uae_plant.csv`](../data/dim_uae_plant.csv).
2. Build a 366-day calendar for 2024: `dim_uae_date`.
3. Generate the maintenance schedule from each plant's cleaning interval and outage cadence: 89 work orders in [`data/fact_uae_maintenance.csv`](../data/fact_uae_maintenance.csv).
4. Join weather, plants and maintenance by day and calculate expected energy, the three losses, simulated actual energy and the AED loss for every plant-day (1,098 rows): rules in [`SIMULATION_LOGIC.md`](SIMULATION_LOGIC.md).
5. Add configuration tables: assumptions, carbon factors, advisor rules and a plant-access mapping for future row-level security.
6. Label every simulated or provisional value with a classification field.

## Step 5. Create the semantic model

1. Create `SM_UAE_Solar_Gold` on the Gold lakehouse in **Direct Lake** mode: the model reads the Delta tables directly, with no scheduled import.
2. Add four one-to-many relationships (plant to both facts, date to both facts), single direction, no many-to-many: [`DATA_DICTIONARY.md`](DATA_DICTIONARY.md).
3. Keep the configuration tables disconnected on purpose.
4. Write 85 DAX measures in 11 folders: [`MEASURES_CATALOGUE.md`](MEASURES_CATALOGUE.md).
5. Audit the model before building the report: keys, grain, date continuity, orphans, filter propagation and Direct Lake compatibility all passed. The one failure, no source URL for the carbon factors, is why those values are labelled provisional.

## Step 6. Design and build the report

1. Plan four pages, each with one question: Overview (how much did we miss), Losses (which driver first), Trends (which plant and month), Operations (what sits behind the numbers).
2. Use a light green theme, large cards and short story text. Reference dashboards inspired the ideas; nothing was copied.
3. Generate the report from code so every page and visual is repeatable.
4. Replace plain visuals with SVG images produced by measures: gauges, rings, a waterfall, a year dial and ranking bars.
5. Add bookmarks that flip each headline card to a story side, hover tooltips, a "How to read" guide, an advisor-rules overlay and a seven-step tutorial.
6. Add slicers for plant, month, advisor rule and carbon scenario.
7. Check each change in a loop: edit, validate, reload, screenshot.

## Step 7. Publish and test

1. Publish the report to the workspace and test it in the Power BI service: card flips, gauges, waterfall, heat grid and slicers.
2. Check the headline numbers against queries on the live model: 73,507 MWh generated, 76,921 MWh expected, AED 1.02M modeled loss.

## Step 8. Capture and share

1. Capture each page at full resolution and record the slicers and story cards in use.
2. Edit the captures into short videos with zoomed shots and captions that use only numbers visible on screen.
3. Publish a LinkedIn post and keep the supporting material in this repository.

## Separate branch: US PVDAQ telemetry

A second branch profiles and transforms US PVDAQ telemetry: `nb_01` to `nb_04` and `pl_01_ingest_pvdaq_metadata` in [`fabric`](../fabric). It uses PySpark and produces `dim_plant`, `dim_date` and `fact_solar_telemetry`. The US data and the UAE weather are not co-located, so this branch is not part of the dashboard, and no PVDAQ table exists in the dashboard's model.

## Limits

- Plants, generation, maintenance and losses are simulated; the tariff is illustrative; carbon factors are provisional.
- This is a historical analysis, not live monitoring and not a forecast.
- Row-level security is designed but not deployed.
- The Power BI project files and the model definition are kept private.
