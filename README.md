# UAE Solar Intelligence

Microsoft Fabric and Power BI Solar Energy Intelligence Platform

A four-page Power BI dashboard on a simulated three-plant UAE solar portfolio, built end to end in Microsoft Fabric.

**Question it answers:** where does the maintenance crew go first?

> Plant C lost the most money. Plant B lost the most per megawatt.

## Key findings (2024)

| Measure | Value |
|---|---|
| Generated vs expected | 73,507 MWh vs 76,921 MWh |
| Shortfall | 3,413 MWh, about AED 1.02M of modeled loss |
| Plant C | AED 464K, the largest total loss |
| Plant B | AED 25.8K per installed MW, the highest loss per MW |
| Soiling | about 48% of the gap, so the first check is cleaning records |

The plant with the biggest bill is not always the plant with the biggest efficiency problem. A good dashboard shows both before anyone books a crew.

**Full technical write-up:** [`docs/PROJECT_DEEP_DIVE.md`](docs/PROJECT_DEEP_DIVE.md) covers the data sources, architecture, star schema, data-quality checks, the DAX layer and the report build.

## Dashboard pages

| Page | Question | Visuals |
|---|---|---|
| Overview | How much did we miss? | Generation, Performance and Financial loss cards with story flips; headline sentence; generation year dial; expected-to-delivered waterfall; where to act first |
| Losses | Which driver first? | Estimated, soiling and outage loss cards; loss drivers by month; plant ranking; days with the largest shortfall |
| Trends | Which plant, which month? | Generation YTD; latest month vs prior; weather context; performance vs expected; plant performance through the year; daily generation pulse; plant-by-month heat grid; who generates what |
| Operations | What sits behind the numbers? | Emissions not avoided; maintenance; days since cleaning; advisor rules; grid factor comparison; avoided emissions by scenario; cleaning and outage events |

Screenshots are in [`screenshots/`](screenshots).

## Architecture (as built)

NASA POWER weather + simulated plant data → Microsoft Fabric pipelines → PySpark notebooks into Silver and Gold lakehouses → Direct Lake semantic model with DAX measures → Power BI report.

A broader proposed enterprise design (Gold Warehouse, ML scoring, telemetry replay with alerts) is in [`images/architecture_proposed.png`](images/architecture_proposed.png). That design is a proposal, not part of the built report.

## Data and method

- **Weather:** NASA POWER, UAE 2024 (real observations), power.larc.nasa.gov
- **Plants, generation, maintenance work orders and losses:** simulated for this case study (3 plants: A 10 MW, B 15 MW, C 20 MW)
- **Tariff:** AED 0.30 per kWh, illustrative, so AED values are modeled exposure, not audited figures
- **Carbon:** DEWA, EWEC and Ember grid factors are provisional and unverified
- **Stack:** Microsoft Fabric (pipelines, lakehouses, PySpark), Direct Lake semantic model, Power BI
- Not live monitoring and not a machine-learning forecast

## Videos

- [`videos/dashboard_tour_2min.mp4`](videos/dashboard_tour_2min.mp4): the short tour
- [`videos/dashboard_tour_1min.mp4`](videos/dashboard_tour_1min.mp4): one-minute version

## Fabric engineering work

The [`fabric/`](fabric) folder holds the Fabric notebooks and a pipeline definition from the data-engineering work: PVDAQ source profiling, Bronze and Silver transformation, telemetry ingestion and a Gold solar model. The PVDAQ (US telemetry) branch is separate and is not used in the dashboard shown here, which runs on NASA POWER weather and simulated UAE plants.

## What is not in this repository

The Power BI project files (`.pbip`, report and semantic model definitions) are kept private on purpose.
