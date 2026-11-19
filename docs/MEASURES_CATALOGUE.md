# Measures catalogue

85 DAX measures in the semantic model, organised in 11 display folders. This list gives the names and purpose only; the model definition itself is not published.

| Folder | Count | Measures |
|---|---|---|
| 01 Core Measures | 13 | Expected Generation MWh, Simulated Actual Generation MWh, Generation Gap MWh, Performance vs Expected %, Estimated Financial Loss AED, Installed Capacity MW, Work Order Count, Maintenance Downtime Hours, Energy Loss %, Plant Count, Observed Days, Average Daily Generation MWh, Capacity Factor % |
| 02 Time Intelligence: Generation | 8 | Generation MTD, QTD, YTD, previous month, MoM %, previous year, YoY %, rolling 7-day |
| 02 Time Intelligence: Financial Loss | 7 | Financial loss MTD, QTD, YTD, previous month, MoM %, previous year, YoY % |
| 02 Time Intelligence: Performance | 3 | Performance YTD %, previous month %, MoM change in percentage points |
| 03 Generation and Performance | 3 | Rolling 30-day performance %, average daily irradiation, performance ratio (industry) % |
| 04 Loss Attribution | 11 | Baseline, soiling and outage loss in MWh; potentially controllable loss; unattributed loss; soiling, outage and baseline share of loss %; dominant loss driver; controllable loss share %; average soiling loss rate % |
| 05 Financial | 10 | Loss per MW, total loss rank, loss per MW rank, scenario tariff, soiling, outage, baseline and controllable loss in AED, top loss plant, top loss plant share % |
| 06 Maintenance | 5 | Cleaning events, outage events, last cleaning date, average days since cleaning, outage downtime hours |
| 07 Sustainability | 5 | Selected carbon factor, estimated avoided emissions tCO2e, emissions opportunity from lost generation, default carbon factor, lost generation tCO2e at the default factor |
| 08 Advisor | 4 | Advisor matching days, advisor rule status, advisor recommended action, maintenance priority |
| 09 Narratives and Story | 13 | Executive headline and supporting narrative, performance diagnostic headline, maintenance decision narrative, advisor narrative, carbon scenario narrative, plant panel narrative, five story-card texts (headline, what happened, why, why it matters, what to do), simulation disclosure |
| 10 Weather | 3 | Average air temperature, average wind speed, average relative humidity |

## Design notes

- **Time intelligence:** the data covers one year, so previous-year and year-over-year measures exist but return blank. No year-over-year visual was built.
- **Narratives:** headline sentences, such as "Plant C carries 45% of the modeled loss", are computed by measures, so they change with the plant and month filters.
- **Report-level measures:** the report adds measures that return small SVG images for the gauges, rings, waterfall and ranking visuals. They live in the report, not the model, so the model stays unchanged.
- **Examples:**
  - `Expected Generation MWh = DIVIDE ( SUM ( fact_uae_solar_daily[expected_energy_kwh] ), 1000 )`
  - `Performance vs Expected % = DIVIDE ( [Simulated Actual Generation MWh], [Expected Generation MWh] )`
