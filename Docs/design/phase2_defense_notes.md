# HarvestLink Rwanda — Phase II: 5-minute defense notes

Use this as a speaking guide, not as a script to read word-for-word. Keep the untouched raw CSV available privately for instructor inspection. Use the pseudonymized derivative for screen-shared demonstrations. Pseudonymization masks the farmer-local identifier but does not guarantee complete anonymization of all survey attributes.

## Before the presentation

Open these files in separate tabs:

1. `data/processed/sas_2024_seasonB_production_pseudonymized.csv` for the on-screen demonstration; keep `data/raw/sas_2024_seasonB_production.csv` unchanged and private for source inspection.
2. `data/dataset_card.md` — source, record meaning, size and quality findings.
3. `docs/design/normalization.md` — UNF → 3NF evidence.
4. `docs/design/erd.png` — logical ERD.
5. `docs/design/data_dictionary.md` — field mapping and column meanings.

## Five-minute flow

**0:00–0:45 — Introduce the source**

“This design uses the National Institute of Statistics of Rwanda Season B Crop Production dataset for the 2023/2024 Seasonal Agricultural Survey. The raw CSV has 35,584 records and 98 columns. One row represents a plot and a crop produced on that plot; it is not a warehouse receipt or stock movement.”

Show the CSV header and one record. Mention that `id` is the source primary key in the supplied metadata.

**0:45–1:30 — Trace one real record**

Use source record `id = 4`:

- `Segment_ID = 12001`
- `s1q1 = Kigali`; `s1q2 = Gasabo`
- `s1q6 = F00001` in the pseudonymized demonstration copy (raw source value is kept private); `s2q1 = 12`
- `s2q4 = Cucumber`; `CropCategory = Vegetables`
- `s2q24_a/b/c` contain “Use of fertilizer”, “Appropriate seeds” and “Good Agricultural practices”.

Trace it into `PROVINCE (Kigali)`, `DISTRICT (Gasabo)`, `SURVEY_SEGMENT (12001)`, `SURVEY_FARMER (12001, F00001)`, `PLOT (12001, F00001, 12)`, `CROP (Cucumber)`, `PLOT_CROP_OBSERVATION (id 4)`, and three `PLOT_CROP_EXPLANATION` child rows.

**1:30–2:20 — Explain UNF and 1NF**

“The original CSV has three fixed explanation columns. This is a repeating group. I moved each nonblank explanation into its own child row with a sequence number, linked back to the original observation ID.”

Show the three explanation child rows for `id = 4`. Also point out that source record `id = 3` stores `Flowers` in `s2q4_o`, which belongs to that crop observation rather than the shared crop-option lookup.

**2:20–3:20 — Explain 2NF and 3NF**

“The NISR `id` remains the source primary key. I also checked a business candidate key made from segment, farmer, plot and crop identity. This exposed information that belongs to a segment, farmer, plot or crop rather than to every crop observation.”

Then explain the transitive geography dependency: `Segment_ID → District → Province`; district/province are represented in their own reference tables. Mention that farmer-local ID is only meaningful within a segment.

**3:20–4:20 — Show the ERD and data dictionary**

Point out the NISR survey/reference model and the separate HarvestLink operational model. Explain the parent-child and one-to-many relationships, the `PLOT_CROP` observation link, and the explanation child table. Use the data dictionary to show where all 98 raw fields have been mapped.

**4:20–5:00 — Explain scope and business rules**

“The NISR dataset provides real agricultural survey evidence, but not cooperative inventory transactions. The HarvestLink receipt, stock movement, physical count and reconciliation workflow is represented separately; any test transaction rows are clearly synthetic. When a count finds no discrepancy, the workflow bypasses investigation and creates no loss record. A discrepancy becomes a loss record only after investigation confirms an actual loss.”

## Likely questions and concise answers

- **Why is `s1q6` not the farmer primary key by itself?** It is a local farmer/LSF ID reused across survey segments; pair it with `Segment_ID`.
- **Why keep `id` if there is a business key?** NISR metadata declares `id` as the source primary key, so it is preserved. The composite business key is used to expose semantic dependencies and check uniqueness in this file.
- **Are blanks errors?** Not automatically. Many are conditional survey skips; they are documented and not replaced with zero during Phase II.
- **Why are some farmer-level answers still on the observation?** Checks found conflicting values within some farmer-key groups. They are preserved at source-row grain until the approved cleaning/review phase rather than silently choosing one value.
- **Does a discrepancy always mean post-harvest loss?** No. Investigation must confirm an actual loss before a loss record is created.
- **Why are NISR farmers not cooperative members?** The NISR survey identifier and a cooperative's membership ID have different provenance and meanings; no direct identity is assumed.

## Important presentation reminder

Do not show respondent-level identifiers or the raw file publicly beyond what your instructor needs to inspect. Keep the original CSV unchanged. Do not claim that NISR supplied cooperative receipts, stock movements or physical-count observations.
