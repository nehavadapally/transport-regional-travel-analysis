# TfSE Regional Travel Survey Analysis

## Overview

This project analyses a sample of Transport for the South East (TfSE) Regional Travel Survey trip records for a Senior Data Analyst assessment.

The analysis is structured to separate:

1. **data quality and preparation**,
2. **preliminary travel-behaviour exploration**,
3. **spatial / origin-destination analysis**, and
4. **Power BI preparation and communication**.

The main analytical question developed from the exploratory work is whether **car travel should be treated as a single behaviour**. The final analysis compares three distance markets:

- **Short:** under 5 miles
- **Medium:** 5–25 miles
- **Long:** 25+ miles

These markets are then examined using mode, purpose, time/day, observed journey duration, trip chaining and origin-destination geography.

---

## Project structure

```text
.
├── .venv/
├── data/
│   ├── external/
│   │   └── Counties_and_Unitary_Authorities_... .gpkg
│   └── TfSE Regional Travel Survey_data.xlsx
├── notebooks/
│   ├── 01_task1_data_quality_and_preparation_corrected.ipynb
│   ├── 02_task2a_travel_behaviour_analysis_corrected.ipynb
│   ├── 03_task2b_spatial_travel_analysis_corrected.ipynb
│   └── 04_prepare_powerbi_inputs.ipynb
├── output/
│   ├── figures/
│   ├── powerbi/
│   ├── task1_analysis_ready_trips.csv
│   └── task2b_spatial_enriched_trips.csv
├── README.md
└── requirements.txt
```

---

## Data inputs

### Regional Travel Survey sample

`data/TfSE Regional Travel Survey_data.xlsx`

The analysis treats one row as one recorded trip and `caseid` as the respondent identifier.

### TfSE geography

`data/external/Counties_and_Unitary_Authorities_... .gpkg`

ONS Counties and Unitary Authorities boundaries are used to assign trip origins and destinations to the 16 TfSE local transport authority areas.

Spatial work uses **British National Grid (EPSG:27700)**.

---

## Analytical workflow

```text
Raw survey data
      │
      ▼
01. Data quality and preparation
      │
      ├── missingness / consistency
      ├── duplicate review
      ├── distance and travel-time checks
      ├── respondent-trip structure
      ├── weighting
      └── analysis-ready flags
      │
      ▼
02. Preliminary travel-behaviour analysis
      │
      ├── mode
      ├── purpose
      ├── trip frequency
      ├── distance
      ├── travel time
      ├── day / time
      └── trip-chain context
      │
      ▼
03. Spatial / OD analysis
      │
      ├── LTA assignment
      ├── geographic scope
      ├── OD matrix
      ├── sample bases
      └── geographic travel patterns
      │
      ▼
04. Power BI input preparation
      │
      ├── fact table
      ├── dimension tables
      └── validation summary
      │
      ▼
Power BI insight and communication layer
```

---

## Notebook guide

### 01 — Data quality and preparation

**File:** `notebooks/01_task1_data_quality_and_preparation_corrected.ipynb`

Purpose:

- load and structure the supplied workbook,
- quantify missing values and cross-field consistency,
- inspect exact and potential duplicate records,
- review zero / implausible distance values,
- assess travel-time provenance,
- examine respondent-trip numbering,
- validate trip-sequence continuity,
- review survey weights,
- check spatial readiness, and
- export an analysis-ready trip table.

Main output:

```text
output/task1_analysis_ready_trips.csv
```

The source records are preserved and QA decisions are represented through analytical flags rather than aggressive row deletion.

---

### 02 — Preliminary travel-behaviour analysis

**File:** `notebooks/02_task2a_travel_behaviour_analysis_corrected.ipynb`

Purpose:

- establish weighted mode share,
- analyse trip purpose,
- inspect observed respondent trip frequency,
- explore distance distributions and modal mix by distance,
- describe travel duration,
- explore day-of-week and time-of-day patterns,
- document the absence of demographic variables,
- investigate validated trip chains, and
- identify candidate short- and medium-distance travel markets for deeper analysis.

This notebook is the **exploratory layer**. Its role is to establish the baseline and generate analytical questions rather than reproduce every final Power BI visual.

---

### 03 — Spatial travel analysis

**File:** `notebooks/03_task2b_spatial_travel_analysis_corrected.ipynb`

Purpose:

- assign valid trip origins and destinations to TfSE LTAs,
- classify within-LTA, between-LTA, inbound and outbound travel,
- construct a row-normalised origin-destination matrix,
- inspect geographic sample bases, and
- explore how travel behaviour varies geographically.

Main output:

```text
output/task2b_spatial_enriched_trips.csv
```

The OD matrix is row-normalised so each origin LTA shows the distribution of its destinations rather than being dominated by differences in sample size.

---

### 04 — Power BI input preparation

**File:** `notebooks/04_prepare_powerbi_inputs.ipynb`

Purpose:

- combine the Task 1 and spatial outputs,
- create analytical fields required by the report,
- create a trip-level fact table,
- create mode, purpose, distance, date, time and geography dimensions,
- assign stable relationship keys, and
- export Power BI-ready CSV files.

Main outputs:

```text
output/powerbi/fact_trips.csv
output/powerbi/dim_mode.csv
output/powerbi/dim_purpose.csv
output/powerbi/dim_distance.csv
output/powerbi/dim_date.csv
output/powerbi/dim_time.csv
output/powerbi/dim_origin_lta.csv
output/powerbi/dim_destination_lta.csv
output/powerbi/validation_summary.csv
```

---

## Run order

Run the notebooks in order:

```text
01 → 02 → 03 → 04
```

Notebook 04 requires the outputs from notebooks 01 and 03.

### Environment

Create / activate the environment and install dependencies:

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

macOS / Linux:

```bash
source .venv/bin/activate
```

Install the project dependencies:

```bash
pip install -r requirements.txt
```

Then launch Jupyter or open the repository in VS Code and run each notebook from the project root / `notebooks` directory.

---

## Core analytical definitions

### Survey weighting

`Weight_2` is used for headline behavioural shares.

Raw trip and respondent counts are retained to communicate sample bases and support QA.

### Valid distance

Positive reported distance is required for distance analysis. Records flagged by the mode-distance plausibility review are excluded from the final distance-analysis subset but retained in the source data.

### Distance markets

The final communication layer uses:

| Market | Distance |
|---|---:|
| Short | <5 miles |
| Medium | 5–25 miles |
| Long | 25+ miles |

The segmentation is used because modal mix and geographic travel patterns differ materially across these ranges.

### Validated trip chain

A trip is considered part of a validated chain only when consecutive trip numbers are supported by destination-to-next-origin spatial continuity within **100 metres**.

Trips not meeting this rule are described as **“not identified in a validated chain”**, not as standalone trips.

### Travel time

Travel time is used **descriptively only**. QA identified highly regular distance/time relationships for several modes, so duration is not interpreted as independent evidence of delay, reliability or causal mode choice.

### Geography

Origins and destinations with complete coordinates are spatially joined to the 16 TfSE LTAs using EPSG:27700.

---

## Power BI analytical narrative

The final report focuses on four questions:

1. **How does modal mix change with journey distance?**
2. **What differentiates car travel across short, medium and long markets?**
3. **How do trip chaining and OD geography change the interpretation?**
4. **What should TfSE investigate next?**

The Power BI report is an insight and communication layer rather than a duplicate of the exploratory notebooks.

---

## Key data limitations

The supplied extract does not include:

- respondent demographics,
- household composition,
- car ownership,
- stated reasons for mode choice, or
- detailed transport-service characteristics such as frequency, fares or reliability.

These are treated as **evidence gaps** rather than inferred explanations.

Subregional results should also be interpreted alongside unweighted trip and respondent bases.

---

## Reproducibility notes

- Run notebooks in numerical order.
- Do not manually edit generated CSV files.
- Preserve the original survey workbook.
- Keep QA flags in downstream data instead of deleting source rows.
- Use survey weights for behavioural shares and raw counts for sample-size context.
- Power BI should read only from `output/powerbi/`.

For full reproducibility, analytical fields used by Power BI should ideally be generated in notebook 04 rather than created manually in the `.pbix` model.

---

## Outputs

The project produces three types of outputs:

**Analysis-ready data**

```text
output/task1_analysis_ready_trips.csv
output/task2b_spatial_enriched_trips.csv
```

**Power BI data model inputs**

```text
output/powerbi/
```

**Figures**

```text
output/figures/
```

The final presentation/report should use only the visuals required to support the analytical narrative; exploratory notebook plots remain supporting evidence.

---

## Author

**Neha Vadapally**  
MSc Data Analytics  
Senior Data Analyst assessment — Transport for the South East
