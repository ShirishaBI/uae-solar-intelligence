# LinkedIn post - UAE Solar Intelligence (final draft)

## Post text (copy from here)

Plant C lost the most money. Plant B lost the most per megawatt.

Same UAE solar portfolio. Two different answers to one question: where does the maintenance crew go first?

I built a Power BI case study to make both answers visible, then asked what a team should check before acting on either.

The 2024 numbers (3 simulated plants, real NASA POWER weather):

- 73,507 MWh generated vs 76,921 MWh expected
- 3,413 MWh shortfall = about AED 1.02M of modeled exposure
- Plant C: AED 464K, the biggest total loss
- Plant B: AED 25.8K per installed MW, the sharpest leak
- Soiling explains about 48% of the gap, so the first check is cleaning records

The lesson: the plant with the biggest bill is not always the plant with the biggest efficiency problem. A good dashboard shows both before anyone books a crew.

Built with NASA POWER weather, Microsoft Fabric (pipelines, lakehouses, PySpark), a Direct Lake semantic model and Power BI. The video walks through all four pages.

Transparency: plants, generation and work orders are simulated. AED values use an illustrative tariff.

If you ran this portfolio, which plant would you investigate first, and what would you check before sending a crew?

I'm looking for Power BI and Microsoft Fabric roles where I can take a business question from source data to a decision. Open to a conversation.

#PowerBI #MicrosoftFabric #DataAnalytics #SolarEnergy #DataVisualization

## First comment (post right after publishing)

Two-minute version: the dashboard has four pages. Overview (how much did we miss), Losses (which driver first), Trends (which plant and month), Operations (what maintenance sits behind the numbers). Happy to share how the loss split by plant size was built. Which view would you want first?

## Hook options (swap line 1 if you want to test)

1. Plant C lost the most money. Plant B lost the most per megawatt.
2. A solar plant can lose the most money and still not be the one to fix first.
3. I found AED 1.02M of modeled solar loss. Then the ranking flipped.

## Media

- Upload the video natively (not a link). 46 s, 1080x1350 (4:5) is the right shape.
- Add burned-in or uploaded captions. Most people watch muted.
- Use snapshots/1_overview.png as the custom thumbnail.
- Optional carousel instead of video: snapshots 1 to 4 in order, plus the architecture diagram as the last slide.
- Alt text per image: describe the page and the one number it proves (see snapshot names).

## Snapshots (taken directly from the dashboard tour video)

1_overview.png, 2_losses.png, 3_trends.png, 4_operations.png (1080x1350 each).

## Fact check against the model (DAX results, 15 Nov 2026)

- Generation 73,507.4 MWh, expected 76,920.57 MWh, gap 3,413.17 MWh: matches.
- Estimated loss AED 1,023,950: matches.
- Plant C AED 463,993 (largest total): matches.
- Plant B AED 25,750 per MW (largest per MW): matches.
- Soiling share of the gap 48.0%: matches.
- Removed from the ChatGPT draft: "reconciled DAX measures across 52 filter contexts". Keep it only if you can show the test. A recruiter may ask.
- Keep the "spot-checked in Power BI Service" claim only if you did it.

## Posting tips

- Best window: Tuesday to Thursday, 8 to 10 AM UAE time.
- No link in the post body. Put any link in the first comment.
- Reply to every comment in the first hour. Ask follow-ups.
- Do not tag companies you have no tie to.
- The architecture image is labelled "Proposed". Say so in its caption: the built system uses a Gold Lakehouse, not the Gold Warehouse shown.

---

## Posting order (recommended)

Before posting: pick the video. Use the 60-second file for the main post and the longer file for post 2. Hide or rename "Service Test" in the report title. Do not put the dashboard link in the post, because the Fabric trial ends in about four weeks.

1. Day 0, morning (8 to 10 AM UAE, Tuesday to Thursday): main post. Native upload of Solar_Intelligence_Tour_1min.mp4 with the post text above. Thumbnail: snapshots/1_overview.png.
2. Within 1 minute: first comment (data sources and method, text below).
3. First hour: reply to every comment. Ask a follow-up question each time.
4. Day 2 or 3: post 2, "How I built it". Carousel of the four snapshots, then the architecture diagram, then a "Data and method" slide. Caption the diagram "Proposed extension. The built system uses a Gold Lakehouse."
5. Day 5 to 7: post 3, one insight only. Snapshot 2_losses.png and the Plant B per-MW point. Ask the same investigation question again.
6. Add the video to your Featured section and mention it in your headline or About text.

## Where to put the data source details

- In the post body: one short line (already there): real NASA POWER weather, simulated plants, illustrative tariff.
- In the first comment: the full source list below. This keeps the post short and still shows rigor.
- In the video: the end card already states simulated vs real.
- In the carousel: a final "Data and method" slide with the same list.
- Optional: a short LinkedIn article or GitHub README with the full method, linked from the first comment.

## First comment: data sources and method (copy from here)

Data sources and method:
- Weather: NASA POWER, UAE 2024 (real observations), power.larc.nasa.gov
- Plants, generation, maintenance work orders and losses: simulated for this case study (3 plants: A 10 MW, B 15 MW, C 20 MW)
- Tariff: AED 0.30 per kWh, illustrative, so AED values are modeled exposure, not audited figures
- Carbon: DEWA, EWEC and Ember grid factors are provisional and unverified
- Stack: Microsoft Fabric (pipelines, lakehouses, PySpark), Direct Lake semantic model, Power BI
- Not live monitoring and not an ML forecast

Which plant would you check first, and what would you look at before sending a crew?
