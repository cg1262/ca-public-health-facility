# UC Health California — Public Health Site Expansion Analysis

## Background

The University of California operates five complete health systems across California, anchored at its campuses in San Francisco, Davis, Los Angeles, Irvine, and San Diego. A sixth health system, based at the UC Riverside campus, is currently in development.

In May 2023, UC Health leadership published a strategic investment plan identifying two top priorities:

- **Partner with the State to Realize Access and Health Improvements**
- **Increase Access to Clinical Services in the Inland Empire and Central Valley**

This project directly addresses these priorities by analyzing California **Medical Service Study Areas (MSSAs)** — sub-county geographic units created by the California Department of Health Care Access and Information (HCAI). MSSAs are built on U.S. Census tract boundaries and updated after each decennial census; the most recent version maps all 9,129 California 2020 census tracts to established MSSA geometries. HRSA formally recognizes California MSSAs as "rational service areas" for federal designations of Health Professional Shortage Areas (HPSAs) and Medically Underserved Areas (MUAs).

This project extends the traditional use of MSSAs by layering in socioeconomic conditions, public health outcomes, existing medical facility locations, and available public lands at the census tract level — building a multi-dimensional foundation for identifying where new UC Health facilities would deliver the greatest impact.

---

## Data Architecture

This project follows a **Bronze → Silver → Gold** medallion architecture in Databricks.

### Bronze Layer — Raw Data Ingestion

Raw data was ingested from five sources:

#### 1. MSSA Boundaries
- **Source:** [HCAI Open Data Portal](https://data.chhs.ca.gov/) via ArcGIS API
- MSSA geometries (polygons) and associated tract-level attributes were downloaded to enable spatial mapping and joining throughout the pipeline.

#### 2. California General Plan Land Use
- **Source:** California statewide General Plan dataset via ArcGIS API
- Land use data was collected from 532 of California's 539 jurisdictions and standardized across county and city boundaries. The `ucd_description` field — which includes a classification of "Open Space and Public Lands" — is a key filter for identifying parcels potentially available for new facility development.

#### 3. CDC PLACES
- **Source:** [CDC PLACES — Census Tract Data](https://data.cdc.gov/500-Cities-Places/PLACES-Local-Data-for-Better-Health-Census-Tract-D/cwsq-ngmh/about_data)
- PLACES provides small-area health estimates for counties, places, census tracts, and ZIP Code Tabulation Areas (ZCTAs) nationwide. The dataset includes model-based estimates for 36 measures spanning health outcomes, preventive service utilization, chronic disease risk behaviors, disabilities, and self-reported health status — enabling identification of communities with the greatest unmet health needs.

#### 4. 2024 ACS 5-Year Survey (U.S. Census Bureau)
- **Source:** Census Bureau ACS API — 14 subject tables
- Selected socioeconomic variables include: household size and composition, vehicle availability, poverty status, age distribution and dependency ratios, health insurance coverage, grandparent caregiver status, journey-to-work patterns, and employment type. Together, these fields proxy the social connectivity and logistical barriers that shape a community's effective access to healthcare.

#### 5. HRSA Health Sites Dashboard
- **Source:** [HRSA Health Sites Dashboard](https://data.hrsa.gov/tools/site-dashboard) — download from the "Health Sites" section
- This dataset is updated daily. Data for this project was ingested on **April 12, 2026**. It enables comparison of health education and clinical service availability by facility type and supports connecting site-type data to geographic units.

---

### Silver Layer — Spatial & Attribute Joins

- **Census and CDC PLACES** datasets were joined to MSSA geometries using **FIPS codes**.
- **General Plan Land Use** and **HRSA Health Sites** datasets were joined to MSSAs via **spatial (shape) joins** on their geometric fields, with MSSA attributes appended to each record.

All data tables created in the silver layer can be found [here](https://drive.google.com/drive/folders/14F14dONnPvcRNrNNmXMbTLsOjlL6qpiP?usp=drive_link)
---

### Gold Layer — Exploratory Visualization

Heatmaps were generated to visualize the geographic distribution of key variables across California census tracts:

1. **California census tract boundaries** — establishing the base geographic layer   <img width="1189" height="1236" alt="image" src="https://github.com/user-attachments/assets/07379f9d-1eec-4646-b7d9-1edbf5718586" />

2. **Open and public lands** — by census tract, derived from General Plan land use classifications   <img width="1189" height="1236" alt="image" src="https://github.com/user-attachments/assets/62b7a41b-49e8-444b-9b18-e3d2aa72282f" />

3. **HRSA health facility sites** — by type and census tract <img width="1189" height="1236" alt="image" src="https://github.com/user-attachments/assets/fc9dac5d-fa70-4643-a762-66d85a13a3ab" />

4. **Socioeconomic and transportation indicators** — from [ACS census data](https://docs.google.com/presentation/d/12M2rJncCQ8c5FOGTAkhnevaSKY52SzK_/edit?usp=drive_link&ouid=104021899507824718620&rtpof=true&sd=true)
5. **Public health outcome measures** — from [CDC PLACES](https://docs.google.com/presentation/d/1rWvDXpZra62UYjJkh5hcZhYuz0Iu-HnB/edit?usp=drive_link&ouid=104021899507824718620&rtpof=true&sd=true)

---

## Next Steps

The data pipeline is complete and the merged dataset is ready for analysis. The immediate next step is to apply analytical and modeling techniques to identify census tracts — and by extension, MSSAs — that represent the highest-priority locations for new UC Health facilities based on a combination of:

- High socioeconomic disadvantage and logistical access barriers
- Poor health outcomes relative to the state baseline
- Proximity to or availability of open/public land suitable for facility development
- Limited existing healthcare infrastructure (as measured by HRSA site density)

Recommended approaches for the next phase include:
- **Composite scoring / index construction** — weighting and combining variables into a single "need + opportunity" score per tract
- **Cluster analysis** — identifying spatial groupings of high-need tracts
- **Geospatial optimization modeling** — locating facilities to maximize population coverage within defined travel-time thresholds
- **Regression or machine learning models** — predicting health outcomes as a function of access and socioeconomic variables to validate feature importance

All source data is available in the Silver layer tables in Databricks and is ready for downstream analysis.
