# HarvestLink Rwanda — Phase II Normalization (UNF → 3NF)

**Course:** INSY 8311 — Database Development with PL/SQL  
**Student:** JABO Ghislain | **Student ID:** 20252SEN163 | **Group:** I  
**Source:** NISR, *Season B: Crop Production* (2023/2024 SAS), file `sas_2024_seasonB_production.csv`  
**Source grain:** one row represents one agricultural plot and a crop produced on that plot during Season B.

## 1. Normalization scope and verified source facts

- 35,584 rows and 98 columns; `id` is identified as the source primary key in `metadata.csv`.
- `id` has 35,584 unique, non-null values; there are no exact duplicate rows.
- A candidate business key for a crop observation is `(Segment_ID, s1q6, s2q1, s2q4, s2q4_o)`. This combination is unique for all 35,584 source rows in this file. `s1q6` is only a farmer-local identifier and must be paired with `Segment_ID`. The shareable working copy pseudonymizes this field while preserving the pair relationships.
- 17,261 distinct plot keys exist using `(Segment_ID, s1q6, s2q1)`; 14,333 farmer-in-segment keys exist using `(Segment_ID, s1q6)`.
- There are 78 source crop names, 107 distinct crop-name/specification combinations, and 21 crop categories. Each of the 78 `s2q4` values maps to one `CropCategory` value in this file.
- 866,751 cells are blank (24.85% of all cells); all 35,584 rows have at least one blank and 81 columns contain blanks. Many are conditional survey skips, not automatically errors.

For privacy in this report, farmer-local IDs shown in sample tables are pseudonyms from the shareable derivative: the source farmer-local identifier within `Segment_ID=12001` is displayed as `F00001`. The original raw CSV remains byte-for-byte unchanged in `data/raw/`; the derived file in `data/processed/` replaces `s1q6` with a stable per-segment token and stores no reverse lookup.

The raw CSV must remain unchanged. The logical design below is a normalized design for analysis/reference; it does not turn NISR plot observations into cooperative warehouse transactions. HarvestLink operational receipt, movement, count and confirmed-loss data remain a separate synthetic test dataset, as approved by the instructor.

## 2. UNF — original wide form

The source has three explanation slots for the same production-comparison question: `s2q24_a`, `s2q24_b` and `s2q24_c`. These are a repeating group. The following are actual source rows with IDs 3, 4 and 5 (blank values are shown as an em dash):

| id | Segment_ID | s1q6 | s2q1 | s2q4 | s2q4_o | s2q24_a | s2q24_b | s2q24_c |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 3 | 12001 | F00001 | 12 | Other annual crops (specify). | Flowers | — | — | — |
| 4 | 12001 | F00001 | 12 | Cucumber | — | Use of fertilizer | Appropriate seeds | Good Agricultural practices |
| 5 | 12001 | F00001 | 12 | Sweet pepper | — | Use of fertilizer | Appropriate seeds | Good Agricultural practices |

**Problem:** each additional explanation is stored in another numbered column. This makes the structure awkward to query and extend.

## 3. First Normal Form (1NF)

Keep one source observation per row and move the repeating explanation slots into a child relation. Each child row stores one explanation and a sequence number. The parent observation retains its original NISR `id`.

### 1NF parent sample (actual source rows)

| Observation_ID | Segment_ID | Farmer_ID | Plot_No | Crop option | Other crop specification |
| --- | --- | --- | --- | --- | --- |
| 3 | 12001 | F00001 | 12 | Other annual crops (specify). | Flowers |
| 4 | 12001 | F00001 | 12 | Cucumber | — |
| 5 | 12001 | F00001 | 12 | Sweet pepper | — |

### 1NF explanation child sample (actual nonblank explanation values)

| Observation_ID (FK) | Sequence_No | Explanation |
| --- | --- | --- |
| 4 | 1 | Use of fertilizer |
| 4 | 2 | Appropriate seeds |
| 4 | 3 | Good Agricultural practices |
| 5 | 1 | Use of fertilizer |
| 5 | 2 | Appropriate seeds |

**Key design:** `PLOT_CROP_OBSERVATION.source_observation_id` is the parent key. `PLOT_CROP_EXPLANATION` uses `(source_observation_id, sequence_no)` as its composite primary key; `source_observation_id` is also a foreign key.

**Dependency removed:** the repeating group `(s2q24_a, s2q24_b, s2q24_c)` is replaced by one explanation value per child row. There can be zero to three explanation rows for an observation.

## 4. Second Normal Form (2NF)

2NF requires 1NF and no non-key attribute to depend on only part of a composite candidate key. For the business-level crop observation, the verified candidate key is `(Segment_ID, Farmer_ID, Plot_No, Crop_Name, Crop_Other_Specification)`. The source's surrogate `id` remains the source observation key; because it is a single-column key, the original CSV by itself has no formal partial dependency relative to `id`. The business key exposes the semantic partial dependencies that should be separated in the logical model.

| Determinant (part of the business key) | Attributes that belong with it | Decomposition |
|---|---|---|
| `Segment_ID` | district label, province, stratum, source segment number | `SURVEY_SEGMENT` (district and province labels still present here at 2NF) |
| `(Segment_ID, Farmer_ID)` | stable farmer type | `SURVEY_FARMER` |
| `(Segment_ID, Farmer_ID, Plot_No)` | plot area, stable plot measurements/characteristics, interview date, plot weight | `PLOT` |
| `Crop_Name` | crop option identity and its category | `CROP` (the conditional free-text specification remains on the observation) |
| Full crop-observation key | crop-specific answers and source-row context | `PLOT_CROP_OBSERVATION` |

### Actual-row illustration

The observation and crop rows below use source IDs 3, 4 and 5. The farmer sample shows three actual distinct farmer/segment keys from the full source, and the plot sample shows three actual plots for farmer pseudonym F00001.

**SURVEY_FARMER** — three actual farmer/segment keys (farmer IDs pseudonymized for display)

| Segment_ID (PK part) | Farmer_ID (PK part) | Farmer type |
|---:|---:|---|
| 12001 | F00001 | Large scale farmer as individual |
| 12003 | F00002 | Large scale farmer as individual |
| 12004 | F00003 | Large scale farmer as individual |

**PLOT** — three actual plot keys (farmer IDs pseudonymized for display)

| Segment_ID | Farmer_ID | Plot_No | Plot area (sqm) |
|---:|---:|---:|---:|
| 12001 | F00001 | 1 | 172,737.170 |
| 12001 | F00001 | 9 | 130,199.200 |
| 12001 | F00001 | 12 | 29,915.363 |

**CROP**

| Source crop option (s2q4) | Crop category |
|---|---|
| Other annual crops (specify). | Other crops |
| Cucumber | Vegetables |
| Sweet pepper | Vegetables |

**PLOT_CROP_OBSERVATION**

| NISR ID (PK) | Segment_ID | Farmer_ID | Plot_No | Source crop option | Other crop specification |
|---:|---:|---|---:|---|---|
| 3 | 12001 | F00001 | 12 | Other annual crops (specify). | Flowers |
| 4 | 12001 | F00001 | 12 | Cucumber | — |
| 5 | 12001 | F00001 | 12 | Sweet pepper | — |

**Dependencies removed (one sentence per split):**

- `Segment_ID → Stratum, source segment number and its district assignment` is moved to `SURVEY_SEGMENT` because those attributes describe the survey segment rather than an individual crop observation.
- `(Segment_ID, Farmer_ID) → Farmer_Type` is moved to `SURVEY_FARMER` because farmer type belongs to the farmer within its segment, not to each crop observation.
- `(Segment_ID, Farmer_ID, Plot_No) → Plot_Area and stable plot attributes` is moved to `PLOT` because those values describe the plot and would otherwise repeat for every crop on it.
- `Crop_Name → Crop_Category` is moved to `CROP` because the selected questionnaire crop option determines its category; the free-text `Other` specification remains with the individual observation because it qualifies that record.


## 5. Third Normal Form (3NF)

3NF requires 2NF and removal of transitive dependencies. The verified geographic dependency is:

`Segment_ID → District → Province`

The source repeats district and province labels on every crop observation. We separate these into `PROVINCE`, `DISTRICT` and `SURVEY_SEGMENT`. The raw data confirms each district label maps to one province, and each `Segment_ID` maps to one district and province in this file.

A crop classification lookup is also separated for clarity: `CROP_CATEGORY` holds the 21 distinct values in the source `CropCategory` column, and `CROP` references one category. `CROP` holds the 78 distinct `s2q4` option labels; `s2q4_o` remains a conditional observation attribute because it specifies an “Other” crop for a particular record. The source confirms that each of the 78 `s2q4` crop names maps to one category. The NISR metadata description for `CropCategory` says “Crop name”, so the exact business meaning of this field should be confirmed against the survey documentation; the design preserves the source label rather than inventing a new interpretation.

### 3NF sample (actual source labels; generated logical IDs are illustrative)

| PROVINCE | DISTRICT | Segment_ID | Stratum | Source segment no. |
|---|---|---:|---|---:|
| Kigali | Gasabo | 12001 | Large Scale Farmer/LSF | 1 |
| Kigali | Kicukiro | 13001 | Large Scale Farmer/LSF | 1 |
| South | Nyanza | 21001 | Large Scale Farmer/LSF | 1 |

**CROP** — generated IDs shown for the logical target; source crop labels are actual values

| Crop_ID | Source crop option | Category_ID (FK) |
|---|---|---|
| C001 | Maize for fodder | CAT01 |
| C002 | Other annual crops (specify). | CAT02 |
| C003 | Cucumber | CAT03 |

**CROP_CATEGORY** — generated IDs shown for the logical target; category labels are actual values

| Category_ID | Category name |
|---|---|
| CAT01 | Fodder crops |
| CAT02 | Other crops |
| CAT03 | Vegetables |

**Transitive dependencies removed (one sentence per split):**

- `Segment_ID → District → Province` is represented by `SURVEY_SEGMENT.district_id` and `DISTRICT.province_id`, so the province label is stored once in `PROVINCE` rather than repeated with each segment and crop observation.
- `Crop_ID → Category_ID → Category_Name` is represented by `CROP.category_id` referencing `CROP_CATEGORY`, so the category name is stored once in the category lookup rather than repeated for each crop option.


## 6. Resulting NISR logical tables

1. `PROVINCE`
2. `DISTRICT`
3. `SURVEY_SEGMENT`
4. `SURVEY_FARMER`
5. `PLOT`
6. `CROP_CATEGORY`
7. `CROP`
8. `PLOT_CROP_OBSERVATION`
9. `PLOT_CROP_EXPLANATION`

The target keys, meanings, required/unique status and raw-field destinations are in `data_dictionary.md`. The full ERD, including the separate HarvestLink operational model, is `erd.png`.

## 7. Source anomalies preserved for Phase IV review (not cleaned now)

Functional-dependency checks were run on the unmodified CSV. These conflicts mean the indicated values must not be silently consolidated to a single parent record during Phase II:

- Within the 14,333 `(Segment_ID, s1q6)` farmer keys: `s1q8` (gender) varies in 3 groups; `s1q9` (age) in 7; `s1q14` in 9; `s1q17` in 2.
- The farmer-level “any of your plots” answers vary within farmer keys: `s3q1` in 152 groups, `s3q7` in 198, `s3q16` in 18, `s3q18` in 193, `s4q2` in 162, `s4q8` in 20 and `s4q14` in 84.
- `s2q3` varies within 13 plot keys. Other selected plot-level fields checked (including plot area, plot weight, interview date and the plot-specific `s3`/`s4` answers) do not vary among non-null values within their plot key in this file.
- `s5q12` contains dates in `DD/MM/YYYY`; all 35,584 values are valid under that format. It is stored with the plot in the target design because it is consistent within each plot.
- `s1q3` has trailing spaces in 29,332 values and `s2q4` in 21 values. Preserve raw values now; trim during Phase IV.

These are data-quality findings, not permission to overwrite source values. Preserve the original CSV and record any later cleaning decisions separately. Fields whose expected farmer-level or plot-level dependency is contradicted by the source are retained at source-observation grain in this logical design, rather than arbitrarily choosing one conflicting value; Phase IV cleaning must document the resolution before moving them to a parent table.

## 8. Type-conversion checklist for Phase IV

- `s5q12`: text `DD/MM/YYYY` → DATE; all source values parsed successfully in the assessment.
- Numeric source fields are already represented as numeric values in the CSV; no numeric-text conversion was identified in this file.
- `s2q7` and `s2q8` are categorical period/range responses, not single calendar dates; do not cast them directly to DATE.
- Preserve question responses and units as reported. Normalize/standardize labels and trim identified whitespace only during the approved cleaning stage.
- No source field is dropped in this logical mapping. All 98 fields have a destination in the field-mapping table in `data_dictionary.md`.

## 9. Provenance and implementation boundary

NISR observations remain survey/reference data. HarvestLink's operational receipt, movement, physical count, reconciliation and confirmed-loss transactions are represented separately in the operational half of the ERD and must be labelled as synthetic test data. Do not use NISR farmer identifiers as cooperative member IDs. Power BI should read reporting views; it is not a transactional entity/table. This is a logical design only: Oracle data types, sizes, constraints and PL/SQL implementation belong to later phases.
