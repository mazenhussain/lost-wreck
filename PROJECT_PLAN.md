# Project: Lost Wreck
## Autonomous Shipwreck Discovery Agent

### 1. Mission

Build a lightweight, interactive research application that analyzes publicly available, high-resolution seafloor bathymetry to identify potentially undiscovered shipwrecks.

The primary objective is to **find a credible, previously uncharted shipwreck candidate** in existing sonar surveys.

The secondary objective is to cross-reference promising candidates against historical shipwreck records and potentially determine their identities.

This is a discovery-focused side project, not a commercial product. Optimize for the probability of finding something genuinely new rather than software sophistication.

### 2. Hard Constraints

These requirements take priority over all other decisions.

- **Time:** The initial project must be achievable within approximately 8–12 hours of focused development and investigation.
- **Downloads:** Never exceed 1 GB of total downloaded data. Aim for under 300 MB.
- **Cost:** Use entirely free APIs, datasets, libraries, and services initially. Never initiate paid usage without explicit approval.
- **Scope:** Analyze one promising survey area first. Do not attempt to scan entire countries or oceans.
- **Interface:** Build a simple interactive local application for exploring results.
- **Data:** Prioritize API queries, cloud-hosted imagery, and small geographical subsets instead of complete survey downloads.
- **Machine learning:** Do not train custom deep-learning models unless conventional methods demonstrably fail and the additional effort is justified.
- **Discovery first:** Do not spend hours perfecting the interface while analysis remains untested.
- **Research integrity:** Never describe an anomaly as a confirmed shipwreck or original discovery without supporting evidence.
- **Autonomy:** Make reasonable technical and research decisions independently. Ask for approval only when a hard constraint or external permission is involved.

### 3. Research Targets and Data Sources

Begin with Ireland, but do not remain committed to Ireland if another region offers substantially better accessible data.

**Priority 1: Ireland**

- INFOMAR: https://www.infomar.ie/data
- Geological Survey Ireland marine datasets: https://www.gsi.ie/
- National Monuments Service wreck inventory: https://www.archaeology.ie/
- Ireland's public ArcGIS REST services for surveys and known wrecks.

**Priority 2: United States**

- NOAA Bathymetric Data Viewer: https://www.ncei.noaa.gov/maps/bathymetry/
- NOAA Digital Coast: https://coast.noaa.gov/dataviewer/
- NOAA hydrographic survey and charted wreck information.

**Priority 3: United Kingdom**

- UK Hydrographic Office: https://datahub.admiralty.co.uk/

Do not assume API endpoints, field names, image resolutions, or download links are still valid. Verify them before implementing integrations.

#### Dataset selection requirements

Prefer surveys with:

1. Native bathymetry resolution of 0.25–1 m per cell.
2. Downloadable numerical elevation data, preferably GeoTIFF or BAG.
3. Small downloadable files or support for geographic subsetting.
4. Accessible survey metadata, including acquisition dates and coverage.
5. Nearby historical shipwreck activity.
6. An accessible inventory of previously documented wrecks.
7. Seafloor conditions suitable for recognizing ship-shaped objects.

Do not use coarse national bathymetry mosaics as the primary detection source.

**Required decision:** Identify and compare up to three promising surveys, then select the strongest one based on resolution, download size, relevant historical records, and existing catalogue coverage.

If no suitable survey can be accessed within the constraints, document the blocker and stop rather than building against unsuitable data.

### 4. Implementation Strategy

Build the application incrementally. Each phase must produce a useful and testable result before proceeding.

#### Phase 1: Dataset discovery and selection

**Target effort: 1–1.5 hours**

Tasks:

- Investigate available survey metadata through public APIs or catalogues.
- Identify areas with high-resolution seabed mapping.
- Estimate file sizes before downloading.
- Check whether candidate surveys contain known shipwrecks suitable for testing detection.
- Review available historical shipwreck inventories.
- Select one survey area.

Deliverable:

`reports/survey_selection.md`

Include the selected survey, source URL, acquisition date, native resolution, coverage, expected download size, rationale, and any limitations.

**Go/no-go:** Do not proceed without suitable numerical bathymetry data.

#### Phase 2: Build the interactive viewer

**Target effort: 1–1.5 hours**

Implement a basic Streamlit application supporting:

- Interactive bathymetry viewing.
- Zooming and panning.
- Survey metadata display.
- Known shipwreck markers where available.
- Selection and inspection of geographic features.
- Measurement of candidate dimensions.
- Candidate ranking and inspection.

Use Plotly, Folium, or another lightweight mapping library as appropriate.

Avoid complex frontend frameworks, authentication, cloud hosting, and unnecessary infrastructure.

Deliverable:

A working local application displaying the selected survey.

#### Phase 3: Implement shipwreck candidate detection

**Target effort: 2–3 hours**

Start with traditional image and terrain processing.

Recommended methods:

- Local seabed background estimation.
- Local-relief analysis.
- Bathymetric slope and hillshading.
- Morphological filtering.
- Connected-component analysis.
- Elongated-object detection.
- Shape, size, and relief measurements.

Detect suspicious elevated or depressed formations that could correspond to wreckage.

For each candidate, calculate where supported by the raster:

- Approximate length and width.
- Height or depth relative to surrounding seabed.
- Elongation ratio.
- Morphological characteristics.
- Coordinates and source survey.
- Detection score.

Inspect invalid-data masks and possible survey artifacts.

**Validation requirement:** Test the detector against at least one documented wreck using comparable-resolution data where feasible. Record whether the known wreck was detected and whether obvious false positives were identified.

Do not treat a positive result on one wreck as proof of general detection accuracy.

Deliverable:

`results/candidates.csv` containing ranked detection candidates, plus visualizations.

#### Phase 4: Check for genuinely unknown objects

**Target effort: 1.5–2 hours**

This is one of the most important phases.

For the strongest 10–20 candidates:

1. Compare locations with official wreck inventories.
2. Search available nautical charts and obstruction records.
3. Review existing survey descriptions and reports.
4. Investigate whether the feature is already documented elsewhere.
5. Check alternative explanations such as rocks, reefs, pipelines, fishing equipment, or survey artifacts.
6. Label the remaining candidates according to the available evidence.

Use the following statuses:

- **Known wreck:** Already documented.
- **Likely natural/artificial non-wreck feature:** An alternative explanation is supported.
- **Unresolved anomaly:** Interesting but insufficient evidence.
- **Potentially undocumented wreck:** Ship-like structure with no matching record found in the sources checked.

Absence from a single catalogue does not establish novelty.

Deliverable:

An evidence-based shortlist containing the strongest unresolved candidates.

#### Phase 5: Historical vessel matching

**Target effort: 1–2 hours, only if useful candidates exist**

For potentially undocumented wrecks:

- Identify historical ship losses in the surrounding region.
- Extract vessel names, types, approximate lengths, dates, and reported locations.
- Compare the available characteristics with sonar candidates.
- Rank plausible matches.
- Include supporting historical references.
- List alternative identities and conflicting evidence.

Use deterministic matching initially.

LLMs or embedding-based retrieval may assist with ambiguous historical descriptions, but do not require paid APIs.

A speculative match must be explicitly labeled as speculative.

Deliverable:

`results/historical_matches.csv`

#### Phase 6: Final report and decision

**Target effort: 1 hour**

Generate a report summarizing:

- Which survey was searched.
- How the data were obtained.
- Survey resolution, coverage and limitations.
- Detection methods.
- Number of candidates and exclusions.
- Most interesting remaining findings.
- Relevant images and measurements.
- Prior-record searches and their limitations.
- Historical matches, if any.
- Recommended next steps.
- Whether further investigation is justified.

Deliverable:

`reports/discovery_report.md`

If a promising candidate exists, prepare a concise evidence package suitable for review by a maritime archaeologist or appropriate heritage authority.

Do not independently contact authorities, publish sensitive wreck coordinates, or claim official discovery without my approval.

### 5. Technology Stack

Prefer:

- Python 3.11+
- Streamlit for the interface.
- Requests for HTTP APIs.
- Pandas for structured records.
- Rasterio for raster processing.
- NumPy and SciPy for numerical analysis.
- scikit-image for geometric detection.
- Plotly or Folium for visualization.
- CSV, JSON, and GeoJSON for storage.

Use PDAL only if raw point clouds become necessary.

Do not introduce databases, Docker, cloud services, vector databases, or AI-agent frameworks unless there is a specific demonstrated need.

### 6. Suggested Repository Structure

```text
lost-wreck/
├── app.py
├── requirements.txt
├── README.md
├── sources/
│   ├── survey_catalog.py
│   ├── bathymetry.py
│   ├── wreck_inventory.py
│   └── historical_records.py
├── analysis/
│   ├── terrain.py
│   ├── detect.py
│   ├── rank.py
│   └── historical_match.py
├── data/
├── results/
│   ├── candidates.csv
│   └── historical_matches.csv
├── reports/
│   ├── survey_selection.md
│   └── discovery_report.md
└── tests/
```

Keep downloaded datasets out of Git.

Preserve source URLs, survey identifiers, coordinate reference systems, processing parameters, and acquisition dates so analyses can be reproduced.

### 7. Discovery Evaluation

Evaluate each promising candidate on two independent dimensions.

**A. Is this likely to be a shipwreck?**

Consider its shape, dimensions, relief, surrounding seabed, and available imaging evidence.

**B. Is this potentially a new discovery?**

Consider whether it appears in existing inventories, previous survey reports, charts, or historical records.

Keep both scores separate. Do not combine them into a misleading single probability of discovery.

Distinguish between:

- A newly detected anomaly.
- A previously uncharted wreck candidate.
- A confirmed new wreck.
- A newly identified historical vessel.

The project can generate the first two outcomes. The latter two generally require additional evidence and expert confirmation.

### 8. Time and Resource Management

Optimize for the initial 8–12-hour investigation.

Follow these rules:

1. Start with API metadata queries, not bulk downloads.
2. Obtain just one suitable high-resolution survey.
3. Never exceed the 1 GB download ceiling.
4. Keep initial analysis geographically limited.
5. Do not train custom machine-learning models.
6. Avoid spending time on UI polish.
7. Prioritize manual review of the strongest candidates.
8. If the detection pipeline performs poorly, simplify rather than repeatedly expanding the architecture.
9. If the selected survey yields no useful leads, do not automatically start a large second survey.
10. Stop when the initial research budget has been exhausted and document the findings.

Do not let the project evolve into an open-ended research initiative.

### 9. Definition of Done

The initial project is complete when:

- [ ] One suitable public survey has been selected and its resolution verified.
- [ ] Data were obtained within the download budget.
- [ ] A functional interactive viewer displays the survey.
- [ ] The detector produces inspectable, ranked candidates.
- [ ] At least one known-wreck validation attempt is documented, if a suitable reference is accessible.
- [ ] The strongest candidates have been compared with available known-wreck records.
- [ ] A reproducible research report has been generated.
- [ ] The final report recommends continuing or stopping based on evidence.

A confirmed discovery is **not** required for successful project completion.

The project must not fabricate findings or imply that an unresolved anomaly has been officially identified.

### 10. Agent Operating Instructions

You are acting as both a software engineering agent and a research assistant.

Follow this decision-making hierarchy:

1. Maximize the chance of making a credible original finding.
2. Stay within the time, cost, and download constraints.
3. Prefer verifiable evidence over speculative interpretations.
4. Favor simple working implementations over sophisticated architecture.
5. Minimize unnecessary questions and implementation overhead.

Before writing substantial code, verify that an appropriate dataset is accessible.

Work incrementally, testing each phase before expanding the application.

Keep a concise record of decisions, blockers, source links, and results.

If a dataset or approach is unproductive, make a justified decision to change direction without exceeding the initial project scope.

Do not represent survey anomalies, historical matches, or absence from public catalogues as proof of a new discovery.

### 11. First Action

**Begin with research, not implementation.**

Find up to three high-resolution bathymetric surveys with public numerical data that can be accessed within the download budget.

Compare Ireland, NOAA, and UK sources as needed.

For each survey, report:

- Survey name and geographical region.
- Official data source.
- Native grid resolution.
- Available numerical file formats.
- Download size or geographic-subsetting options.
- Existing wreck records in the area.
- Whether the survey has already been specifically investigated for shipwrecks.
- Potential advantages and limitations.

Select the strongest survey, then proceed directly to building the minimal investigation pipeline.

**Ultimate objective: Discover something genuinely new, not merely demonstrate that the software works.**
