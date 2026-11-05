# PICK: Pedestrian Access to Parcel Pickup in University City

## Project Information

**Students:** Frankie Zheng, Qiwen Bian, and Manching Ho

**Study area:** University City District, Philadelphia

## Background and Research Question

Neighborhood pickup could gradually replace doorstep delivery for small parcels that local shops can handle. Consolidating parcels at shops may reduce delivery traffic and give residents a place to collect packages on foot. This project uses Python GIS to measure walking access and makes a separate estimate of doorstep delivery labor.

**Research question:** How would adding neighborhood pickup shops to the existing post office network change where residents can collect parcels on foot, and whose walk would become shorter?

## Data Sources

| Data type | Source and use |
| --- | --- |
| Study boundary | [OpenDataPhilly](https://opendataphilly.org/datasets/business-improvement-districts/) provides the University City District boundary. |
| Existing pickup sites | The [USPS locator](https://tools.usps.com/locations/home.htm?address=19104) and [USPS pickup rules](https://faq.usps.com/articles/Knowledge/Hold-for-Pickup-Service) provide locations and service eligibility. Use official service boundaries or address assignments where USPS publishes them for the relevant pickup service. |
| Walking network and candidate shops | Query OpenStreetMap through the Overpass API with [OSMnx](https://osmnx.readthedocs.io/en/stable/user-reference.html) for walkable streets, paths, crossings, supermarkets, convenience stores, and pharmacies. |
| Residential population | [2020 Census TIGER/Line blocks](https://www.census.gov/geographies/mapping-files/2020/geo/tiger-line-file.html) provide population counts and representative points for estimating coverage. |
| Doorstep delivery benchmarks | A [U.S. urban delivery study](https://www.sciencedirect.com/science/article/pii/S0965856426001151) provides delivery service time benchmarks. [BLS wage data](https://www.bls.gov/oes/2023/may/oes_37980.htm) provide a Philadelphia metro postal carrier wage benchmark. |

## Proposed AI Methods

| Engineering implementation | Method category |
| --- | --- |
| **1. Establish the baseline.** Use GeoPandas to align the boundary, Census blocks, and verified USPS sites. Record service eligibility and any published USPS assignments separately from the modeled walking coverage. Connect sites and population points to the OSMnx pedestrian graph. Use distance-weighted Dijkstra searches in NetworkX to calculate reachable street segments and blocks. Retain walkable links across the study boundary. For an all-doorstep scenario, apply low, medium, and high handoff times to 100 parcels and multiply the resulting work hours by the local hourly wage. Report delivery labor time and wage cost separately from residents' pickup walking time, which is zero for doorstep delivery. | Vector data operations · Network service area analysis · Scenario analysis |
| **2. Screen potential partner shops.** Filter by shop type, operating status, and public access. Remove duplicates, verify entrances, and calculate the walking coverage of the full set of eligible shops using the same graph and thresholds. | Spatial filtering · Network service area analysis |
| **3. Compare the coverage layers.** Evaluate post offices alone, candidate shops alone, and the combined network. Weight each Census block by population. Count residents once per scenario and distinguish newly covered blocks, blocks with shorter walks, and blocks that remain outside the threshold. | Spatial overlay · Population-weighted accessibility analysis |

Use **400 m and 800 m of one-way walking distance** throughout. The 800 m threshold rounds half a mile, a distance used in [Philadelphia's ParkScore analysis](https://parkserve.tpl.org/downloads/pdfs/Philadelphia_PA.pdf) to approximate a 10-minute walk. The 400 m threshold tests a shorter walk for parcel pickup.

## Expected Outcomes

| Stage | Maps and tables | Written analysis |
| --- | --- | --- |
| **1. Existing service** | A map of existing USPS sites, reachable street segments, and covered Census blocks at both thresholds. A table of delivery labor hours and wage costs per 100 doorstep parcels. | Describe current walking coverage, identify gaps, and explain the time and wage assumptions. |
| **2. Candidate shops** | A map of candidate shops by category, their walking coverage, and overlap with existing sites. | Explain the screening criteria and identify where shops could extend walking coverage. |
| **3. Combined coverage** | A change map showing newly covered blocks, shorter walks, and remaining gaps, plus a coverage chart comparing all three scenarios at both thresholds. | Quantify who gains access, where pickup becomes closer, and how the results change with the walking threshold. |
