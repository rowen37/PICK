# PICK: Pedestrian Access to Parcel Pickup in University City

## Project Information

**Students:** Frankie Zheng, Qiwen Bian, and Manching Ho

**Study area:** University City District, Philadelphia

## Background and Research Question

Neighborhood pickup could gradually replace doorstep delivery for small parcels that local shops can handle. Consolidation may reduce delivery traffic, and a nearby shop can make pickup part of an ordinary walking trip. Access depends on where residents live within a block, where they can cross the street, and whether a pickup site is open when they arrive.

**Research question:** Where would a layer of neighborhood pickup shops make parcel collection easier on foot, and which gains hold when we account for opening hours and the location of housing within each Census block?

## Data Sources

| Data type | Source and use |
| --- | --- |
| Study boundary | [OpenDataPhilly](https://opendataphilly.org/datasets/business-improvement-districts/) provides the University City District boundary. |
| Existing pickup sites | Use the [USPS locator](https://tools.usps.com/locations/home.htm?address=19104) and [USPS pickup rules](https://faq.usps.com/articles/Knowledge/Hold-for-Pickup-Service) to verify locations, eligible services, pickup hours, and published service assignments where available. |
| Pedestrian network and shops | Query OpenStreetMap through the Overpass API with [OSMnx](https://osmnx.readthedocs.io/en/stable/user-reference.html) for walkable streets, crossings, paths, and candidate supermarkets, convenience stores, and pharmacies. Check opening hours and entrances against public listings or field observations. |
| Residential population | Use [2020 Census TIGER/Line blocks](https://www.census.gov/geographies/mapping-files/2020/geo/tiger-line-file.html) for block totals, the [Census P5 table](https://www.census.gov/programs-surveys/decennial-census/about/rdo/summary-files.html) for group quarters counts, and [Philadelphia property assessments](https://data.phila.gov/visualizations/property-assessments/) for geocoded residential properties and livable floor area. |
| Doorstep delivery benchmarks | Use a [U.S. urban delivery time study](https://www.sciencedirect.com/science/article/pii/S0965856426001151) and [Philadelphia metro postal carrier wages](https://www.bls.gov/oes/2023/may/oes_37980.htm) to estimate the labor required for final handoff. |

## Proposed AI Methods

| Engineering implementation | Method category |
| --- | --- |
| **1. Build the existing-service baseline.** Use GeoPandas to assign Census blocks to the study area and keep a buffer around the walking graph for routes that cross the boundary. Verify USPS pickup eligibility and keep any official service assignments as a separate layer. Connect demand points and verified entrances to walkable edges. Use distance-weighted Dijkstra in NetworkX to measure reachable street segments and distances to eligible sites. Spot-check routes near barriers and crossings. For the all-doorstep baseline, apply low, central, and high handoff times to 100 parcels and multiply the labor hours by the local wage benchmark. Record zero pickup walking time for residents in that scenario. | Vector data operations · Network service area analysis · Labor cost estimation |
| **2. Test how population placement affects coverage.** Compare the Census block-point baseline with a residential-property model. Join residential properties to blocks and allocate each block's household population in proportion to residential livable floor area. Keep group quarters residents at a verified residence location when available and otherwise at the block point. Use the block point when usable property data are missing. Reconcile allocated weights to Census totals and map where the two models disagree. | Spatial joins · Dasymetric population mapping · Sensitivity analysis |
| **3. Build and test the candidate shop layer.** Screen for shop type, active status, and public access. Deduplicate records and verify entrances. Compare USPS alone, candidate shops alone, and the combined network. Evaluate distance-based access and access at Wednesday noon, Wednesday 6 p.m., and Saturday noon. Treat a candidate shop's verified business hours as its potential pickup window. Compare results for confirmed hours with a scenario that assumes sites with unknown hours are open. | Spatial filtering · Time-dependent accessibility · Scenario analysis |
| **4. Measure who gains access and test how stable the gains are.** For each walking threshold, report newly covered population, distance changes for residents already covered by USPS, and the share of residents with at least two eligible sites in range. Keep the same population denominator across scenarios and count each resident once per coverage measure. Recalculate gains across both population models and all time windows. | Population-weighted accessibility · Spatial overlay · Sensitivity analysis |

All coverage uses **400 m and 800 m of one-way walking distance**. The longer threshold approximates half a mile, the distance used in [Philadelphia's ParkScore analysis](https://parkserve.tpl.org/downloads/pdfs/Philadelphia_PA.pdf) for a 10-minute walk. The shorter threshold tests a more local pickup trip. Candidate shop scenarios represent potential participation.

## Expected Outcomes

| Stage | Maps and tables | Written analysis |
| --- | --- | --- |
| **1. Existing service** | USPS walking service areas, reachable street segments, and a table of handoff hours and wage costs per 100 doorstep deliveries. | Explain the official service eligibility, the main walking gaps, and the assumptions behind the labor estimate. |
| **2. Population placement** | A map of where the block-point and residential-property models produce different coverage estimates. | Identify the blocks where moving demand points changes the result and report how often the model falls back to block points. |
| **3. Candidate shops and hours** | A map of eligible candidate locations and coverage by time window, linked to a chart of population covered by each network. | Identify places where shops extend access, places with two reachable choices, and places where evening or weekend hours change the result. |
| **4. Combined results** | An interactive change map and linked summary charts with controls for network layer, walking threshold, time window, and population model. | Distinguish gains that persist across scenarios from gains that depend on a specific cutoff, opening schedule, or population model. |

**Team workflow.** Frankie leads data preparation and population allocation. Qiwen leads pedestrian network construction and route validation. Manching leads scenario evaluation, the labor estimate, and the linked charts. The team integrates the maps and charts in a [Quarto site](https://quarto.org/docs/interactive/) and reviews the results together.
