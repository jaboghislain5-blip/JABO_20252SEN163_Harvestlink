# Dataset Card — NISR Season B: Crop Production

## 1. Dataset identification

| Field | Description |
|---|---|
| Dataset title | Season B: Crop Production |
| Publisher / source | National Institute of Statistics of Rwanda (NISR), Data Sharing Platform |
| Official source URL | https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a |
| Survey / collection | Rwanda Seasonal Agricultural Survey (SAS), 2023/2024 agricultural year |
| Reference period | Season B, March–June 2024 |
| Geographic coverage | Rwanda (national coverage) |
| Date downloaded / accessed | 16 November 2026 |
| Main file | `sas_2024_seasonB_production.csv` |
| Format | CSV (comma-separated values), UTF-8 text |
| Data rows | 35,584 (header excluded) |
| Columns | 98 |
| Extracted CSV size | 16,400,975 bytes (approximately 15.64 MiB) |
| Original ZIP size | 3,450,410 bytes (approximately 3.29 MiB) |

## 2. What one record represents

Each record represents one specific agricultural plot and the crops produced on that plot by a farming household during Season B (March–June 2024). It is a plot-level agricultural survey observation. It is **not** a cooperative warehouse receipt, stock movement, physical stock count, or reconciliation transaction.

## 3. Dataset contents

The production file contains information about:

- Geographic and survey identifiers, including province, district, segment and plot information.
- Farmer / respondent characteristics recorded in the survey.
- Crop identification and crop category.
- Seed type, source, quantities and purchase costs.
- Harvested and remaining quantities.
- Uses of production, including household consumption, sales, storage and other uses.
- Post-harvest losses, including reported causes such as theft, pests, animals, harvesting, transport and storage.

The downloaded ZIP also contains `README.txt`, `metadata.csv`, and PDF survey documentation. Keep these reference files with the original download; `metadata.csv` describes the CSV fields and their supplied data types and meanings.

## 4. Licence and permitted use

The NISR Data Sharing Platform dataset page marks this dataset as **PUBLIC**. Its usage guidelines state that users may use, share and adapt the data, including for commercial purposes, provided that the source is credited and users do not imply that the publisher endorses their work or conclusions.

The dataset page does not identify a specific named licence (for example, a Creative Commons licence). Therefore, this card records the platform's stated usage conditions rather than assigning an unverified licence name.

Suggested source credit:

> Source: National Institute of Statistics of Rwanda (NISR), *Season B: Crop Production*, Data Sharing Platform. Retrieved 16 November 2026 from https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a

## 5. Relevance and limitation for HarvestLink Rwanda

This dataset provides real Rwanda agricultural observations that can inform crop, harvest, storage-use and post-harvest-loss context. It does not contain the complete day-to-day operational records required by HarvestLink's cooperative stock-reconciliation workflow.

Accordingly:

- NISR observations must retain their identity as real plot-level survey data.
- Cooperative receipts, stock movements, loss transactions, physical counts and reconciliation examples used for operational testing must be separately labelled as synthetic test data.
- Synthetic test records must never be represented as NISR observations or combined with them in a way that obscures their different origins.

## 6. Raw-file preservation and privacy note

The original CSV and ZIP archive are to be preserved exactly as downloaded. Do not edit, clean, rename columns, remove rows, or overwrite the original raw file during Phase II. Record data-quality findings separately; cleaning belongs to the later implementation phase as specified in the Phase II instructions.

The supplied `metadata.csv` identifies column `s1q6` as **Farmer ID/LSF ID**. Because the Phase II instructions require personal identifiers to be anonymised, verify whether this survey identifier is already anonymised and whether the raw file may be placed in a public GitHub repository. Keep the untouched source copy locally until this is confirmed. If an anonymised derivative is needed, create it as a separate file and document the transformation; never overwrite the original raw file.

## 7. Files received in the original ZIP

- `dataset/sas_2024_seasonB_production.csv` — main production dataset.
- `metadata.csv` — field names, supplied data types and descriptions.
- `README.txt` — dataset description, coverage, period and unit of observation.
- `documentations/` — survey and questionnaire PDF documents.

## 8. Source citation

National Institute of Statistics of Rwanda. (2026). *Season B: Crop Production* [Dataset]. Data Sharing Platform. Retrieved 16 November 2026 from https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a

---

**Phase II note:** This card documents the dataset and its provenance. Data-quality counts and identified quality issues will be recorded after the separate inspection required in Task 2; no cleaning has been performed as part of this card.

---

## 9. Task 2 — Data quality assessment (raw-file profile)

**Assessed file:** `dataset/sas_2024_seasonB_production.csv` inside the downloaded ZIP archive.  
**Assessment date:** 16 November 2026.  
**Method:** The CSV was read as text so the inspection did not convert or overwrite values in the raw file. Blank cells were counted as empty strings. Numeric and date checks were performed against the supplied `metadata.csv` descriptions. No cleaning or correction was performed.

### 9.1 Overall counts

| Check | Observed result |
|---|---:|
| Data records (header excluded) | 35,584 |
| Columns in CSV | 98 |
| Total data cells | 3,487,232 |
| Empty cells | 866,751 (24.85%) |
| Columns containing at least one empty cell | 81 of 98 |
| Rows containing at least one empty cell | 35,584 of 35,584 (100.00%) |
| Exact duplicate rows | 0 |
| Blank `id` values | 0 |
| Duplicate `id` values | 0 |
| Distinct `id` values | 35,584 |
| Numeric columns declared in metadata (`INT`, `SMALLINT`, `DOUBLE`) | 50 |
| Non-empty numeric values that failed numeric parsing | 0 |
| Fractional values in metadata fields declared `INT` or `SMALLINT` | 0 |
| Blank interview dates in `s5q12` | 0 |
| Interview dates parsing successfully as `DD/MM/YYYY` | 35,584 of 35,584 |
| Distinct interview-date values | 65 |
| Interview-date range | 12/06/2024 to 25/08/2024 |

The `id` field is complete and unique for all rows. No exact duplicate rows were detected. All non-empty values in the 50 columns declared numeric in the metadata passed numeric parsing, and all values in fields declared `INT` or `SMALLINT` were whole numbers. All values in `s5q12` parsed as dates in the displayed format.

### 9.2 Empty-cell counts in selected fields

The file contains empty cells in many columns. The largest counts include:

| Column | Meaning from metadata | Empty cells | Percent of records |
|---|---|---:|---:|
| `s2q10_o` | Where did improved seeds sown come from?, (specify) | 35,522 | 99.83% |
| `s2q4_o` | Crop name (specify) | 35,496 | 99.75% |
| `s4q12_1` | Have you used any other mechanical equipment not mentioned in this plot during this season? | 35,398 | 99.48% |
| `s4q10_1` | Have you used ploughing animals (oxen) in this plot during this season? | 35,394 | 99.47% |
| `s4q11_1` | Have you used a ploughing tractor in this plot during this season? | 35,394 | 99.47% |
| `s3q17` | Did you use any type of micro-nutrients in this plot during this season? | 35,321 | 99.26% |
| `s4q9` | Did you use any mechanical equipment for agriculture activities on this plot during this agricultural season? | 35,233 | 99.01% |
| `s1q17_o` | Other Relationship of respondent to the farmer | 35,170 | 98.84% |
| `s2q37` | What is the storage facility used during this agricultural season? | 34,612 | 97.27% |
| `s2q24_c` | Explanation3 | 33,295 | 93.57% |
| `s2q18` | Amount spent for the purchase of improved seeds sown in this plot(Rwf) | 32,994 | 92.72% |
| `s2q17` | Quantity of improved seeds purchased and sown in this plot | 32,927 | 92.53% |
| `s2q16_2` | Quantity Sown | 32,927 | 92.53% |
| `s2q16_1` | Unit of improved seeds | 32,862 | 92.35% |
| `s2q10` | Where did improved seeds sown come from? | 32,492 | 91.31% |


These blank values may result from survey skip patterns or questions that apply only under certain conditions; they should not automatically be treated as errors. The raw file has at least one blank cell in every row, so the questionnaire and codebook must be consulted before deciding which blanks are genuinely missing and which are not applicable.

### 9.3 Text and category consistency observations

| Observation | Count | What was observed |
|---|---:|---|
| Trailing spaces in `s1q3` | 29,332 values | The value `Agricultural land on hillside ` includes a trailing space. |
| Trailing spaces in `s2q4` | 21 values | Some crop-name values, including `French beans   ` and `Millet `, contain trailing spaces. |
| Irregular farmer-type labels in `s1q7` | 726 records | Two labels contain spelling, spacing, capitalization or punctuation variations (`Coperative`, double space, and a trailing comma). The raw values are retained unchanged. |
| Numeric-looking category in `s2q37` | 1 record | `s2q37` is described as a storage-facility category, but one non-empty value is `12`; verify against the questionnaire/codebook. |
| Numeric-looking response in `s2q23` | 74 records | `s2q23` is described as a comparison category, but the raw value `0` appears; verify its meaning against the questionnaire/codebook. |

### 9.4 Task 2 conclusion and action for Phase IV

The raw dataset has a consistent row/column structure and a complete unique primary identifier, with no exact duplicate rows and no parsing failures in metadata-declared numeric fields or the interview-date field. The main issues to investigate during cleaning are the high volume of empty cells, trailing whitespace in selected text values, inconsistent farmer-type labels, and the two unusual coded category values described above. These observations are recorded only; **the raw data has not been cleaned, edited, or corrected**. During Phase IV, each proposed cleaning action must be checked against the NISR questionnaire and metadata before creating a separate cleaned/staging copy. The original ZIP and CSV must remain unchanged.
