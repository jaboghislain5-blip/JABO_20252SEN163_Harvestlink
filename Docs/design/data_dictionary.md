# HarvestLink Rwanda — Phase II Data Dictionary and Raw-Field Mapping

**Course:** INSY 8311 — Database Development with PL/SQL  
**Student:** JABO Ghislain | **Student ID:** 20252SEN163 | **Group:** I

## 1. Scope and notation

This is a **logical** data dictionary. It intentionally does not specify Oracle data types or sizes; those belong to the implementation phase. `PK` means primary key, `FK` means foreign key, and `UQ` means unique alternate key. `Required?` describes the proposed target design; `Conditional` means the source/business process may legitimately omit the value. NISR survey data and HarvestLink operational transactions have different provenance and remain separate.

The NISR section is based on the source CSV and metadata. The source metadata describes `CropCategory` as “Crop name”; the observed 21 values behave as category labels (78 crop names each map to one value), but confirm the exact label meaning against the NISR questionnaire/codebook before implementation. The cooperative section is a proposed logical design derived from the approved HarvestLink BPMN. Operational test transactions must be labelled `SYNTHETIC_TEST_DATA` and must never be presented as NISR observations. The source `s1q6` is a Farmer ID/LSF ID; it is pseudonymized in the shareable processed copy while the exact source value is retained only in the untouched raw file.

## 2. NISR normalized logical data dictionary

### PROVINCE

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| province_id | Generated logical identifier for one province. | Yes | Yes | Derived |
| province_name | Province name as supplied by NISR. | Yes | Yes | s1q1 |

### DISTRICT

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| district_id | Generated logical identifier for one district. | Yes | Yes | Derived |
| province_id | Province containing this district. | Yes | No | FK → PROVINCE |
| source_district_label | Original NISR district name and code label. | Yes | Yes within province | s1q2 |

### SURVEY_SEGMENT

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| segment_id | NISR IDQUEST segment identifier. | Yes | Yes | Segment_ID |
| district_id | District to which the segment belongs. | Yes | No | FK → DISTRICT |
| stratum | Survey stratum label. | Yes | No | s1q3 |
| source_segment_number | Segment/LSF number; not globally unique. | Yes | No | s1q4 |

### SURVEY_FARMER

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| segment_id | Segment containing the farmer identifier. | Yes | Composite PK/FK | Segment_ID |
| farmer_local_id | Farmer/LSF ID within the segment; not globally unique. | Yes | Composite PK | s1q6; pseudonymized as F-codes in the shareable derivative |
| farmer_type | Farmer/LSF type, stable for this key in the source file. | Yes | No | s1q7 |

### PLOT

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| segment_id | Survey segment containing the plot. | Yes | Composite PK/FK | Segment_ID |
| farmer_local_id | Farmer who manages the plot. | Yes | Composite PK/FK | s1q6; uses the same pseudonymized key as SURVEY_FARMER in the shareable derivative |
| plot_no | Plot number within the farmer/segment. | Yes | Composite PK | s2q1 |
| plot_area_sqm | Plot area in square metres. | Yes | No | s2q2 |
| organic_fertilizer_used_on_plot | Whether organic fertilizer was used on this plot. | Conditional | No | s3q3 |
| inorganic_fertilizer_used_on_plot | Whether inorganic fertilizer was used on this plot. | Conditional | No | s3q9 |
| micronutrients_used_on_plot | Whether micro-nutrients were used on this plot. | Conditional | No | s3q17 |
| pesticide_fungicide_used_on_plot | Whether pesticide/fungicide was used on this plot. | Conditional | No | s3q19 |
| erosion_degree | Reported degree of erosion on the plot. | Conditional | No | s4q1 |
| anti_erosion_activity_on_plot | Whether anti-erosion activity exists on this plot. | Conditional | No | s4q3 |
| anti_erosion_done_current_season | Whether anti-erosion activity occurred this season. | Conditional | No | s4q4 |
| plot_in_land_consolidation_site | Whether the plot is in a land-consolidated site. | Conditional | No | s4q6 |
| mechanical_equipment_used_on_plot | Whether mechanical equipment was used on this plot. | Conditional | No | s4q9 |
| ploughing_animals_used_on_plot | Whether ploughing animals were used on this plot. | Conditional | No | s4q10_1 |
| tractor_used_on_plot | Whether a ploughing tractor was used on this plot. | Conditional | No | s4q11_1 |
| other_mechanical_equipment_used_on_plot | Whether other mechanical equipment was used on this plot. | Conditional | No | s4q12_1 |
| plot_irrigated_current_season | Whether this plot was irrigated this season. | Conditional | No | s4q15 |
| interview_date | Date of interview for this plot. | Yes | No | s5q12; convert from DD/MM/YYYY text |
| plot_sampling_weight | NISR plot sampling weight. | Yes | No | plot_weight |

### CROP_CATEGORY

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| category_id | Generated logical identifier for a crop category. | Yes | Yes | Derived |
| category_name | NISR crop category label. | Yes | Yes | CropCategory |

### CROP

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| crop_id | Generated logical identifier for one NISR crop option. | Yes | Yes | Derived |
| source_crop_name | Crop option label selected in the questionnaire. | Yes | Yes | s2q4; 78 distinct option labels |
| category_id | Category assigned to the crop option. | Yes | No | FK → CROP_CATEGORY; CropCategory |

### PLOT_CROP_OBSERVATION

**Alternate business key (unique in the assessed source file):** `(segment_id, farmer_local_id, plot_no, crop_id, source_crop_name_specification)`. The source `id` remains the primary key and is retained unchanged; the conditional “Other” specification is part of the observation identity when present.

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| source_observation_id | Original NISR production-row identifier. | Yes | Yes | id; source PK |
| segment_id | Segment for this crop observation. | Yes | No | FK → SURVEY_SEGMENT; Segment_ID |
| farmer_local_id | Farmer local ID in the segment. | Yes | No | FK → SURVEY_FARMER; s1q6 |
| plot_no | Plot number for this observation. | Yes | No | FK → PLOT; s2q1 |
| crop_id | Crop option selected for this observation. | Yes | No | FK → CROP; s2q4 |
| source_crop_name_specification | Free-text crop name when an “Other” option is selected; belongs to this crop observation. | Conditional | No | s2q4_o |
| source_farmer_gender_response | Gender response retained at source-row grain due 3 farmer-key conflicts. | Conditional | No | s1q8 |
| source_farmer_age_response | Age response retained at source-row grain due 7 farmer-key conflicts. | Conditional | No | s1q9 |
| source_farmer_responded_self | Self-response answer retained at source-row grain due 9 farmer-key conflicts. | Conditional | No | s1q14 |
| source_respondent_relationship | Relationship response retained at source-row grain due 2 farmer-key conflicts. | Conditional | No | s1q17 |
| source_respondent_relationship_other | Other relationship free text. | Conditional | No | s1q17_o |
| source_number_of_main_crops | Number of main crops reported; 13 plot-key conflicts retained pending review. | Conditional | No | s2q3 |
| source_organic_fertilizer_any_plot | Farmer-level answer: organic fertilizer used on any plot; conflicting farmer-key groups. | Conditional | No | s3q1 |
| source_inorganic_fertilizer_any_plot | Farmer-level answer: inorganic fertilizer used on any plot; conflicting farmer-key groups. | Conditional | No | s3q7 |
| source_micronutrients_any_plot | Farmer-level answer: micro-nutrients used on any plot; conflicting farmer-key groups. | Conditional | No | s3q16 |
| source_pesticide_fungicide_any_plot | Farmer-level answer: pesticide/fungicide used on any plot; conflicting farmer-key groups. | Conditional | No | s3q18 |
| source_anti_erosion_any_plot | Farmer-level answer: anti-erosion activity on any plot; conflicting farmer-key groups. | Conditional | No | s4q2 |
| source_mechanical_equipment_any_plot | Farmer-level answer: equipment used on any plot; conflicting farmer-key groups. | Conditional | No | s4q8 |
| source_irrigation_any_plot | Farmer-level answer: irrigation on any plot; conflicting farmer-key groups. | Conditional | No | s4q14 |
**Individual crop-production response columns** (each source field has its own logical column; `Conditional` reflects blanks in the source and may follow questionnaire skip logic):

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| s2q5 | Number of plants in this plot (for perennial crops) | Conditional | No | s2q5 |
| s2q6 | Number of plants to be harvested in this agricultural season (for perennial crops) | Conditional | No | s2q6 |
| s2q7 | Sowing date | Yes | No | s2q7 |
| s2q8 | Expected period for crop harvesting | Yes | No | s2q8 |
| s2q9 | Did you use improved seed for this crop in any of your plots in this season? | Conditional | No | s2q9 |
| s2q10 | Where did improved seeds sown come from? | Conditional | No | s2q10 |
| s2q10_o | Where did improved seeds sown come from?, (specify) | Conditional | No | s2q10_o |
| s2q11 | Type of seeds sown in this plot | Conditional | No | s2q11 |
| s2q12 | Is the seed sown in this plot for the current season? | Conditional | No | s2q12 |
| s2q13_1 | Unit of traditional seeds | Conditional | No | s2q13_1 |
| s2q13_2 | Quantity Sown | Conditional | No | s2q13_2 |
| s2q14 | Quantity of traditional seeds purchased and sown in the plot | Conditional | No | s2q14 |
| s2q15 | Amount spent for the purchase of traditional seeds for this plot (Rwf) | Conditional | No | s2q15 |
| s2q16_1 | Unit of improved seeds | Conditional | No | s2q16_1 |
| s2q16_2 | Quantity Sown | Conditional | No | s2q16_2 |
| s2q17 | Quantity of improved seeds purchased and sown in this plot | Conditional | No | s2q17 |
| s2q18 | Amount spent for the purchase of improved seeds sown in this plot(Rwf) | Conditional | No | s2q18 |
| s2q19_1 | Unit of measurement | Conditional | No | s2q19_1 |
| s2q19_2 | Quantity already harvested in measurement unit | Conditional | No | s2q19_2 |
| s2q19_3 | Quantity already harvested (in Kg) | Conditional | No | s2q19_3 |
| s2q20_1 | Remaining quantity to be harvested in measurement unit | Conditional | No | s2q20_1 |
| s2q20_2 | Remaining quantity to be harvested(in Kg) | Conditional | No | s2q20_2 |
| s2q21 | Total quantity of harvest for this season (in Kg) | Conditional | No | s2q21 |
| s2q22 | Total quantity of harvest for this crop during the current season (Kg) | Conditional | No | s2q22 |
| s2q23 | Compare the production of this crop with its production in the same last season | Conditional | No | s2q23 |
| s2q25 | Qty of production transformed/to be transformed (Kg) | Conditional | No | s2q25 |
| s2q25_1 | Qty transformed & used as food | Conditional | No | s2q25_1 |
| s2q25_2 | Qty transformed & used as drink | Conditional | No | s2q25_2 |
| s2q25_3 | Qty transformed & used in other way | Conditional | No | s2q25_3 |
| s2q26 | Qty of production that has been sold/to be sold?(Kg) | Conditional | No | s2q26 |
| s2q27 | On which market this crop was sold? | Conditional | No | s2q27 |
| s2q28 | What was the selling price per kilogram? (Rwf/Kg) | Conditional | No | s2q28 |
| s2q29 | Qty that has been used/to be used by the household (auto consumption)? | Conditional | No | s2q29 |
| s2q30 | Qty of production used/to be used as wage for hired labour? (Kg) | Conditional | No | s2q30 |
| s2q31 | Qty of production used /to be used as farm rent? (Kg) | Conditional | No | s2q31 |
| s2q32 | Qty of production offered/to be offered as a gift?(Kg) | Conditional | No | s2q32 |
| s2q33 | Q.2.33 Qty of production exchanged/ to be exchanged with other things? (Kg) | Conditional | No | s2q33 |
| s2q34 | Qty of production used/to be used as seeds?(Kg) | Conditional | No | s2q34 |
| s2q35 | Qty of production used/to be used as fodder?(Kg) | Conditional | No | s2q35 |
| s2q36 | Qty of production stored/to be stored(Kg) | Conditional | No | s2q36 |
| s2q37 | What is the storage facility used during this agricultural season? | Conditional | No | s2q37 |
| s2q38 | Quantity of production stored in public storage (kg) | Conditional | No | s2q38 |
| s2q39 | Qty of this crop what is the quantity that has been lost? | Conditional | No | s2q39 |
| s2q40 | Qty of production used/to be used in any other way not mentioned before? | Conditional | No | s2q40 |
| s2q41 | Qty of crop stollen at farm level? (kg) | Conditional | No | s2q41 |
| s2q42 | Qty of crop damaged by insects or pests at farm level? (kg) | Conditional | No | s2q42 |
| s2q43 | Qty of crop lost due to birds or other animals? (kg) | Conditional | No | s2q43 |
| s2q44 | Qty of crop’s stalks fallen to the ground? (kg) | Conditional | No | s2q44 |
| s2q45 | Qty of crop lost during harvesting procedures? (kg) | Conditional | No | s2q45 |
| s2q46 | Qty of crop production lost in the transport of produce?(kg) | Conditional | No | s2q46 |
| s2q47 | Qty of crop production lost during storage procedures? | Conditional | No | s2q47 |
| s2q48 | Qty lost during harvest processing? (kg) | Conditional | No | s2q48 |
| s2q49 | Qty of harvest lost during packaging? (kg) | Conditional | No | s2q49 |
| s2q50 | Qty of harvest lost at sales? (kg) | Conditional | No | s2q50 |

### PLOT_CROP_EXPLANATION

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| source_observation_id | Observation whose production-comparison explanation is recorded. | Yes | Composite PK/FK | FK → PLOT_CROP_OBSERVATION |
| sequence_no | Original explanation slot order (1, 2 or 3). | Yes | Composite PK | s2q24_a/b/c position |
| explanation_text | One explanation why production differed from last season. | Yes | No | s2q24_a, s2q24_b, s2q24_c |

### COOPERATIVE

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| cooperative_id | Unique cooperative identifier. | Yes | Yes | Operational data |
| cooperative_code | Cooperative business code. | Yes | Yes | Operational data |
| cooperative_name | Registered/display cooperative name. | Yes | No | Operational data |
| status | Active/inactive status. | Yes | No | Operational data |

### COOPERATIVE_MEMBER

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| member_id | Unique cooperative member identifier. | Yes | Yes | Operational data |
| cooperative_id | Cooperative to which the member belongs. | Yes | No | FK → COOPERATIVE |
| member_code | Member code unique within a cooperative. | Yes | Yes within cooperative | Operational data |
| member_name | Member display name. | Yes | No | Operational data |
| contact_details | Optional contact details for cooperative operations. | No | No | Operational data |
| status | Membership status. | Yes | No | Operational data |

### SYSTEM_USER

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| user_id | Unique system user identifier. | Yes | Yes | Operational data |
| cooperative_id | Cooperative context for the user. | Yes | No | FK → COOPERATIVE |
| username | Login name. | Yes | Yes | Operational data |
| display_name | User display name. | Yes | No | Operational data |
| role | Receiving, Inventory or Management role. | Yes | No | Operational data |
| is_active | Whether the account is active. | Yes | No | Operational data |

### COOP_CROP

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| coop_crop_id | Unique cooperative crop identifier. | Yes | Yes | Operational data |
| cooperative_id | Cooperative that handles the crop. | Yes | No | FK → COOPERATIVE |
| crop_name | Crop name used in cooperative operations. | Yes | Yes within cooperative | Operational data |
| unit_of_measure | Standard stock unit for the crop (for example kg). | Yes | No | Operational data |
| is_active | Whether the crop is currently handled. | Yes | No | Operational data |

### STORAGE_LOCATION

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| location_id | Unique storage-location identifier. | Yes | Yes | Operational data |
| cooperative_id | Cooperative owning/using this location. | Yes | No | FK → COOPERATIVE |
| location_code | Location code unique within cooperative. | Yes | Yes within cooperative | Operational data |
| location_name | Storage area/warehouse name. | Yes | No | Operational data |
| location_type | Warehouse, store, drying area, etc. | Yes | No | Operational data |
| is_active | Whether the location is active. | Yes | No | Operational data |

### PRODUCE_RECEIPT

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| receipt_id | Unique receipt identifier. | Yes | Yes | Operational data |
| cooperative_id | Cooperative receiving the produce. | Yes | No | FK → COOPERATIVE |
| member_id | Member delivering the produce. | Yes | No | FK → COOPERATIVE_MEMBER |
| receipt_number | Human-readable receipt number, unique within cooperative. | Yes | Yes within cooperative | Operational data |
| received_at | Date and time produce was received. | Yes | No | Operational data |
| received_by_user_id | User who inspected/recorded the receipt. | Yes | No | FK → SYSTEM_USER |
| receipt_status | Draft, accepted, rejected or posted status. | Yes | No | Operational data |
| inspection_notes | Optional inspection notes. | No | No | Operational data |

### RECEIPT_DETAIL

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| receipt_detail_id | Unique line identifier within a produce receipt. | Yes | Yes | Operational data |
| receipt_id | Receipt containing this line. | Yes | No | FK → PRODUCE_RECEIPT |
| coop_crop_id | Crop received. | Yes | No | FK → COOP_CROP |
| accepted_quantity | Accepted quantity after inspection/weighing. | Yes | No | Operational data |
| unit_of_measure | Unit used for this receipt line. | Yes | No | Operational data |
| quality_note | Optional crop quality/condition note. | No | No | Operational data |

### STOCK_MOVEMENT

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| movement_id | Unique stock movement identifier. | Yes | Yes | Operational data |
| coop_crop_id | Crop moved. | Yes | No | FK → COOP_CROP |
| from_location_id | Source location; absent for a receipt into stock. | Conditional | No | FK → STORAGE_LOCATION |
| to_location_id | Destination location; absent for an issue out of stock. | Conditional | No | FK → STORAGE_LOCATION |
| movement_type | Receipt, transfer, issue or approved adjustment. | Yes | No | Operational data |
| quantity | Quantity moved; must be positive. | Yes | No | Operational data |
| unit_of_measure | Unit used for the movement. | Yes | No | Operational data |
| moved_at | Date/time of movement. | Yes | No | Operational data |
| recorded_by_user_id | User recording the movement. | Yes | No | FK → SYSTEM_USER |
| receipt_detail_id | Receipt detail that originated this movement, when applicable. | No | No | Optional FK → RECEIPT_DETAIL |
| reference_note | Optional explanation/reference. | No | No | Operational data |

### PHYSICAL_STOCK_COUNT

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| count_id | Unique physical count event identifier. | Yes | Yes | Operational data |
| cooperative_id | Cooperative being counted. | Yes | No | FK → COOPERATIVE |
| count_number | Human-readable count number unique within cooperative. | Yes | Yes within cooperative | Operational data |
| counted_at | Date/time of physical count. | Yes | No | Operational data |
| count_status | Open, submitted, reviewed or closed. | Yes | No | Operational data |
| created_by_user_id | User who initiated the count. | Yes | No | FK → SYSTEM_USER |
| reviewed_by_user_id | Manager reviewing the count, when reviewed. | No | No | Optional FK → SYSTEM_USER |

### COUNT_DETAIL

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| count_detail_id | Unique line identifier within a count. | Yes | Yes | Operational data |
| count_id | Physical count containing this line. | Yes | No | FK → PHYSICAL_STOCK_COUNT |
| location_id | Location physically counted. | Yes | No | FK → STORAGE_LOCATION |
| coop_crop_id | Crop physically counted. | Yes | No | FK → COOP_CROP |
| counted_quantity | Quantity physically counted. | Yes | No | Operational data |
| unit_of_measure | Unit used for the count line. | Yes | No | Operational data |
| unique_count_line | One line per count, location and crop. | Yes | Yes (count_id + location_id + coop_crop_id) | Business alternate key |

### STOCK_RECONCILIATION

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| reconciliation_id | Unique reconciliation identifier. | Yes | Yes | Operational data |
| count_detail_id | Count line being reconciled; one reconciliation per count line. | Yes | Yes | Unique FK → COUNT_DETAIL |
| book_quantity_snapshot | System-recorded quantity at count time. | Yes | No | Operational data |
| variance_quantity | Difference between physical and book quantity; preferably derived. | Derived | No | Computed from count detail and book quantity |
| discrepancy_status | No discrepancy / discrepancy found. | Yes | No | Operational data |
| investigation_status | Not required, pending, investigated or closed. | Yes | No | Operational data |
| investigation_findings | Explanation from an investigation, if one was required. | No | No | Operational data |
| loss_confirmed | Whether investigation confirmed an actual loss. | Conditional | No | Operational data |
| reviewed_by_user_id | Manager confirming the reconciliation decision. | No | No | Optional FK → SYSTEM_USER |
| reviewed_at | Date/time reconciliation was reviewed. | No | No | Operational data |

### LOSS_RECORD

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| loss_id | Unique confirmed-loss identifier. | Yes | Yes | Operational data |
| reconciliation_id | Reconciliation that established the loss. | Yes | No | FK → STOCK_RECONCILIATION |
| loss_quantity | Confirmed lost quantity. | Yes | No | Operational data |
| unit_of_measure | Unit used for the loss quantity. | Yes | No | Operational data |
| loss_reason | Confirmed reason/cause of loss. | Yes | No | Operational data |
| confirmed_at | Date/time loss was confirmed. | Yes | No | Operational data |
| confirmed_by_user_id | User/manager confirming the loss. | Yes | No | FK → SYSTEM_USER |
| notes | Optional supporting notes. | No | No | Operational data |

### AUDIT_LOG

| Column | Meaning | Required? | Unique? | Source / relationship |
|---|---|---|---|---|
| audit_id | Unique audit event identifier. | Yes | Yes | Operational data |
| user_id | User who performed the action. | Yes | No | FK → SYSTEM_USER |
| event_type | Create, update, approve, reverse or other recorded action. | Yes | No | Operational data |
| entity_name | Name of affected entity/table. | Yes | No | Operational data |
| record_key | Key of affected record, stored as an audit reference. | Yes | No | Operational data; generic reference |
| event_at | Date/time of event. | Yes | No | Operational data |
| old_value_summary | Optional summary of previous value. | No | No | Operational data |
| new_value_summary | Optional summary of new value. | No | No | Operational data |


## 3. Raw-field mapping — all 98 source columns

Every column in `sas_2024_seasonB_production.csv` is mapped below. No raw source column is dropped in this logical design. Blank counts are from the unmodified CSV; blank values may reflect questionnaire skip logic and should not automatically be treated as errors.

| Raw field | Source meaning (NISR metadata) | Target table.column | Blank cells | Nonblank cells | Mapping / Phase IV note |
| --- | --- | --- | --- | --- | --- |
| id | Primary Key | PLOT_CROP_OBSERVATION.source_observation_id | 0 | 35584 | Retain NISR source primary key; unique in the file. |
| Segment_ID | IDQUEST | SURVEY_SEGMENT.segment_id | 0 | 35584 | Retain as the source segment identifier and reference it from farmer, plot and observation records. |
| s1q1 | Province | PROVINCE.province_name | 0 | 35584 | One province label per source value; create a generated province key in the logical model. |
| s1q2 | District name & code | DISTRICT.source_district_label | 0 | 35584 | Preserve the combined district name/code label; do not parse the code until the source codebook confirms its structure. |
| s1q3 | Stratum | SURVEY_SEGMENT.stratum | 0 | 35584 | Trailing spaces occur in 29,332 values; trim only during Phase IV cleaning. |
| s1q4 | Segment/LSF Number | SURVEY_SEGMENT.source_segment_number | 0 | 35584 | Not unique by itself; preserve as a segment attribute. |
| s1q6 | Farmer ID/LSF ID | SURVEY_FARMER.farmer_local_id | 0 | 35584 | Composite identifier with Segment_ID; not globally unique by itself. Pseudonymized in the shareable derivative. |
| s1q7 | Farmer/LSF type | SURVEY_FARMER.farmer_type | 0 | 35584 | Consistent within each Segment_ID + farmer-local-ID group in this file. |
| s1q8 | Gender | PLOT_CROP_OBSERVATION.source_farmer_gender_response | 712 | 34872 | Do not consolidate to farmer: 3 farmer-key groups have conflicting non-null values. |
| s1q9 | Age | PLOT_CROP_OBSERVATION.source_farmer_age_response | 723 | 34861 | Do not consolidate to farmer: 7 farmer-key groups have conflicting non-null values. |
| s1q14 | Did the farmer respond him/herself? | PLOT_CROP_OBSERVATION.source_farmer_responded_self | 5 | 35579 | Do not consolidate to farmer: 9 farmer-key groups have conflicting non-null values. |
| s2q1 | Plot No | PLOT.plot_no | 0 | 35584 | Part of composite plot key: Segment_ID + farmer-local-ID + plot number. |
| s1q17 | Relationship of respondent to the farmer | PLOT_CROP_OBSERVATION.source_respondent_relationship | 25286 | 10298 | Do not consolidate to farmer: 2 farmer-key groups have conflicting non-null values. |
| s1q17_o | Other Relationship of respondent to the farmer | PLOT_CROP_OBSERVATION.source_respondent_relationship_other | 35170 | 414 | Retain source wording; it is a conditional free-text response. |
| s2q2 | Plot area(sqm) | PLOT.plot_area_sqm | 0 | 35584 | Stable for the identified plot in the source file. |
| s2q3 | Number of main crops to be surveyed on during this agricultural season in the plot. | PLOT_CROP_OBSERVATION.source_number_of_main_crops | 0 | 35584 | 13 plot-key groups have conflicting values; retain at source-observation grain pending review. |
| s2q4 | Crop name | CROP.source_crop_name | 0 | 35584 | 21 trailing-space values; trim in Phase IV, not in raw file. |
| s2q4_o | Crop name (specify) | PLOT_CROP_OBSERVATION.source_crop_name_specification | 35496 | 88 | Conditional specification for an “Other” crop option; retain exact wording. |
| CropCategory | Crop name | CROP_CATEGORY.category_name | 0 | 35584 | 21 distinct categories; each of the 78 source crop names maps to one category in this file. |
| s2q5 | Number of plants in this plot (for perennial crops) | PLOT_CROP_OBSERVATION.s2q5 | 22814 | 12770 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q6 | Number of plants to be harvested in this agricultural season (for perennial crops) | PLOT_CROP_OBSERVATION.s2q6 | 23343 | 12241 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q7 | Sowing date | PLOT_CROP_OBSERVATION.s2q7 | 0 | 35584 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q8 | Expected period for crop harvesting | PLOT_CROP_OBSERVATION.s2q8 | 0 | 35584 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q9 | Did you use improved seed for this crop in any of your plots in this season? | PLOT_CROP_OBSERVATION.s2q9 | 7 | 35577 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q10 | Where did improved seeds sown come from? | PLOT_CROP_OBSERVATION.s2q10 | 32492 | 3092 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q10_o | Where did improved seeds sown come from?, (specify) | PLOT_CROP_OBSERVATION.s2q10_o | 35522 | 62 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q11 | Type of seeds sown in this plot | PLOT_CROP_OBSERVATION.s2q11 | 7 | 35577 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q12 | Is the seed sown in this plot for the current season? | PLOT_CROP_OBSERVATION.s2q12 | 7 | 35577 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q13_1 | Unit of traditional seeds | PLOT_CROP_OBSERVATION.s2q13_1 | 16726 | 18858 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q13_2 | Quantity Sown | PLOT_CROP_OBSERVATION.s2q13_2 | 19701 | 15883 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q14 | Quantity of traditional seeds purchased and sown in the plot | PLOT_CROP_OBSERVATION.s2q14 | 19706 | 15878 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q15 | Amount spent for the purchase of traditional seeds for this plot (Rwf) | PLOT_CROP_OBSERVATION.s2q15 | 26558 | 9026 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q16_1 | Unit of improved seeds | PLOT_CROP_OBSERVATION.s2q16_1 | 32862 | 2722 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q16_2 | Quantity Sown | PLOT_CROP_OBSERVATION.s2q16_2 | 32927 | 2657 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q17 | Quantity of improved seeds purchased and sown in this plot | PLOT_CROP_OBSERVATION.s2q17 | 32927 | 2657 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q18 | Amount spent for the purchase of improved seeds sown in this plot(Rwf) | PLOT_CROP_OBSERVATION.s2q18 | 32994 | 2590 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q19_1 | Unit of measurement | PLOT_CROP_OBSERVATION.s2q19_1 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q19_2 | Quantity already harvested in measurement unit | PLOT_CROP_OBSERVATION.s2q19_2 | 906 | 34678 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q19_3 | Quantity already harvested (in Kg) | PLOT_CROP_OBSERVATION.s2q19_3 | 906 | 34678 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q20_1 | Remaining quantity to be harvested in measurement unit | PLOT_CROP_OBSERVATION.s2q20_1 | 906 | 34678 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q20_2 | Remaining quantity to be harvested(in Kg) | PLOT_CROP_OBSERVATION.s2q20_2 | 906 | 34678 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q21 | Total quantity of harvest for this season (in Kg) | PLOT_CROP_OBSERVATION.s2q21 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q22 | Total quantity of harvest for this crop during the current season (Kg) | PLOT_CROP_OBSERVATION.s2q22 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q23 | Compare the production of this crop with its production in the same last season | PLOT_CROP_OBSERVATION.s2q23 | 969 | 34615 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q24_a | Explanation1 | PLOT_CROP_EXPLANATION.explanation_text | 10799 | 24785 | Split into zero-to-three child rows per source observation, with sequence 1/2/3; blank slots create no child row. |
| s2q24_b | Explanation2 | PLOT_CROP_EXPLANATION.explanation_text | 25874 | 9710 | Split into zero-to-three child rows per source observation, with sequence 1/2/3; blank slots create no child row. |
| s2q24_c | Explanation3 | PLOT_CROP_EXPLANATION.explanation_text | 33295 | 2289 | Split into zero-to-three child rows per source observation, with sequence 1/2/3; blank slots create no child row. |
| s2q25 | Qty of production transformed/to be transformed (Kg) | PLOT_CROP_OBSERVATION.s2q25 | 956 | 34628 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q25_1 | Qty transformed & used as food | PLOT_CROP_OBSERVATION.s2q25_1 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q25_2 | Qty transformed & used as drink | PLOT_CROP_OBSERVATION.s2q25_2 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q25_3 | Qty transformed & used in other way | PLOT_CROP_OBSERVATION.s2q25_3 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q26 | Qty of production that has been sold/to be sold?(Kg) | PLOT_CROP_OBSERVATION.s2q26 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q27 | On which market this crop was sold? | PLOT_CROP_OBSERVATION.s2q27 | 17926 | 17658 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q28 | What was the selling price per kilogram? (Rwf/Kg) | PLOT_CROP_OBSERVATION.s2q28 | 16727 | 18857 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q29 | Qty that has been used/to be used by the household (auto consumption)? | PLOT_CROP_OBSERVATION.s2q29 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q30 | Qty of production used/to be used as wage for hired labour? (Kg) | PLOT_CROP_OBSERVATION.s2q30 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q31 | Qty of production used /to be used as farm rent? (Kg) | PLOT_CROP_OBSERVATION.s2q31 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q32 | Qty of production offered/to be offered as a gift?(Kg) | PLOT_CROP_OBSERVATION.s2q32 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q33 | Q.2.33 Qty of production exchanged/ to be exchanged with other things? (Kg) | PLOT_CROP_OBSERVATION.s2q33 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q34 | Qty of production used/to be used as seeds?(Kg) | PLOT_CROP_OBSERVATION.s2q34 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q35 | Qty of production used/to be used as fodder?(Kg) | PLOT_CROP_OBSERVATION.s2q35 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q36 | Qty of production stored/to be stored(Kg) | PLOT_CROP_OBSERVATION.s2q36 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q37 | What is the storage facility used during this agricultural season? | PLOT_CROP_OBSERVATION.s2q37 | 34612 | 972 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q38 | Quantity of production stored in public storage (kg) | PLOT_CROP_OBSERVATION.s2q38 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q39 | Qty of this crop what is the quantity that has been lost? | PLOT_CROP_OBSERVATION.s2q39 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q40 | Qty of production used/to be used in any other way not mentioned before? | PLOT_CROP_OBSERVATION.s2q40 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q41 | Qty of crop stollen at farm level? (kg) | PLOT_CROP_OBSERVATION.s2q41 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q42 | Qty of crop damaged by insects or pests at farm level? (kg) | PLOT_CROP_OBSERVATION.s2q42 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q43 | Qty of crop lost due to birds or other animals? (kg) | PLOT_CROP_OBSERVATION.s2q43 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q44 | Qty of crop’s stalks fallen to the ground? (kg) | PLOT_CROP_OBSERVATION.s2q44 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q45 | Qty of crop lost during harvesting procedures? (kg) | PLOT_CROP_OBSERVATION.s2q45 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q46 | Qty of crop production lost in the transport of produce?(kg) | PLOT_CROP_OBSERVATION.s2q46 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q47 | Qty of crop production lost during storage procedures? | PLOT_CROP_OBSERVATION.s2q47 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q48 | Qty lost during harvest processing? (kg) | PLOT_CROP_OBSERVATION.s2q48 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q49 | Qty of harvest lost during packaging? (kg) | PLOT_CROP_OBSERVATION.s2q49 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s2q50 | Qty of harvest lost at sales? (kg) | PLOT_CROP_OBSERVATION.s2q50 | 905 | 34679 | Crop-specific survey response; retain source value and conditional nulls. |
| s3q1 | Did you use organic fertilizer in any of your plots during this season? | PLOT_CROP_OBSERVATION.source_organic_fertilizer_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s3q3 | Have you used organic fertilizer in this plot during this season? | PLOT.organic_fertilizer_used_on_plot | 6820 | 28764 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s3q7 | Did you use inorganic fertilizer in any of your plots during this season? | PLOT_CROP_OBSERVATION.source_inorganic_fertilizer_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s3q9 | Have you used inorganic fertilizer in this plot during this season? | PLOT.inorganic_fertilizer_used_on_plot | 18122 | 17462 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s3q16 | Did you use any type of micro-nutrients in any of your plots in this season | PLOT_CROP_OBSERVATION.source_micronutrients_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s3q17 | Did you use any type of micro-nutrients in this plot during this season? | PLOT.micronutrients_used_on_plot | 35321 | 263 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s3q18 | Did you use pesticide/Fungicide in any of your plots during this season? | PLOT_CROP_OBSERVATION.source_pesticide_fungicide_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s3q19 | Have you used pesticide/Fungicide in this plot during this current season? | PLOT.pesticide_fungicide_used_on_plot | 25550 | 10034 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q1 | What is the degree of erosion on this plot? | PLOT.erosion_degree | 1 | 35583 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q2 | Is there any anti-erosion activity in any of your plots? | PLOT_CROP_OBSERVATION.source_anti_erosion_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s4q3 | Is there any anti-erosion activity on this plot? | PLOT.anti_erosion_activity_on_plot | 3861 | 31723 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q4 | Were these anti-erosion activities done during the current agricultural season? | PLOT.anti_erosion_done_current_season | 8271 | 27313 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q6 | Is this plot located in land consolidated site in this season? | PLOT.plot_in_land_consolidation_site | 1 | 35583 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q8 | Did you use any mechanical equipment for agriculture activities in any of your plots during this agricultural season? | PLOT_CROP_OBSERVATION.source_mechanical_equipment_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s4q9 | Did you use any mechanical equipment for agriculture activities on this plot during this agricultural season? | PLOT.mechanical_equipment_used_on_plot | 35233 | 351 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q10_1 | Have you used ploughing animals (oxen) in this plot during this season? | PLOT.ploughing_animals_used_on_plot | 35394 | 190 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q11_1 | Have you used a ploughing tractor in this plot during this season? | PLOT.tractor_used_on_plot | 35394 | 190 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q12_1 | Have you used any other mechanical equipment not mentioned in this plot during this season? | PLOT.other_mechanical_equipment_used_on_plot | 35398 | 186 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s4q14 | Did you practice irrigation in any of your plots during this agricultural season? | PLOT_CROP_OBSERVATION.source_irrigation_any_plot | 1 | 35583 | Question refers to any farmer plot, but conflicting values occur within farmer-key groups; retain per source row pending review. |
| s4q15 | Has this plot been irrigated during this agricultural season? | PLOT.plot_irrigated_current_season | 31271 | 4313 | Stable within the identified plot in this source file; preserve missing/conditional values. |
| s5q12 | Date of interview | PLOT.interview_date | 0 | 35584 | Text date in DD/MM/YYYY; all 35,584 non-null values parsed successfully; convert to DATE in Phase IV. |
| plot_weight | plot_weight | PLOT.plot_sampling_weight | 0 | 35584 | Stable within the identified plot in this source file; preserve missing/conditional values. |

## 4. Key and relationship summary

### NISR survey model

- `PROVINCE` 1:M `DISTRICT`; a district belongs to one province.
- `DISTRICT` 1:M `SURVEY_SEGMENT`; each segment maps to one district.
- `SURVEY_SEGMENT` 1:M `SURVEY_FARMER`; farmer-local IDs are only unique within a segment.
- `SURVEY_FARMER` 1:M `PLOT`; plot number is only unique within its farmer/segment.
- `PLOT` 1:M `PLOT_CROP_OBSERVATION`; a plot can have several crop observations.
- `CROP_CATEGORY` 1:M `CROP`; one crop option maps to one category in this dataset.
- `CROP` 1:M `PLOT_CROP_OBSERVATION`.
- `PLOT_CROP_OBSERVATION` 1:M `PLOT_CROP_EXPLANATION`; zero to three explanation rows are possible.

### HarvestLink operational model

- `COOPERATIVE` 1:M members, system users, crop catalog entries, storage locations, receipts and physical stock counts.
- `COOPERATIVE_MEMBER` 1:M `PRODUCE_RECEIPT`; one receipt has one delivering member.
- `PRODUCE_RECEIPT` 1:M `RECEIPT_DETAIL`; each detail identifies one cooperative crop and accepted quantity.
- `RECEIPT_DETAIL` 1:M `STOCK_MOVEMENT` where receipt-origin movements are recorded; other movement types may have no receipt detail.
- `COOP_CROP` 1:M receipt details, stock movements and count details.
- `STORAGE_LOCATION` 1:M stock movements (as source or destination) and count details.
- `PHYSICAL_STOCK_COUNT` 1:M `COUNT_DETAIL`.
- `COUNT_DETAIL` 1:0..1 `STOCK_RECONCILIATION` (a count line may not yet be reconciled; at most one reconciliation is proposed per line).
- `STOCK_RECONCILIATION` 1:0..M `LOSS_RECORD`; records exist only for confirmed loss events.
- `SYSTEM_USER` 1:M operational events and audit entries.

## 5. Relationships, cardinality and optionality (crow’s-foot)

`1` means exactly one; `0..1` means optional one; `0..*` means zero or many; `1..*` means one or many. The parent may have zero or many child records unless stated otherwise. Every mandatory child foreign key refers to exactly one parent. The ERD uses crow’s-foot markers with an optional circle where the minimum is zero.

### NISR survey/reference model

| Parent | Child | Parent-to-child cardinality | Child-to-parent participation | Explanation |
|---|---|---:|---|---|
| PROVINCE | DISTRICT | 0..* | Each district has exactly 1 province | A province may have many districts; each district belongs to one province. |
| DISTRICT | SURVEY_SEGMENT | 0..* | Each segment has exactly 1 district | Each survey segment belongs to one district. |
| SURVEY_SEGMENT | SURVEY_FARMER | 0..* | Each survey farmer has exactly 1 segment | Farmer-local IDs are interpreted within their segment. |
| SURVEY_FARMER | PLOT | 0..* | Each plot has exactly 1 farmer/segment | Plot number is scoped to the farmer within the segment. |
| PLOT | PLOT_CROP_OBSERVATION | 1..* | Each observation has exactly 1 plot | A plot represented in this production file has at least one crop observation. |
| CROP_CATEGORY | CROP | 1..* | Each crop option has exactly 1 category | Observed categories each contain at least one crop option. |
| CROP | PLOT_CROP_OBSERVATION | 0..* | Each observation has exactly 1 crop option | One crop option may occur on many plots. |
| PLOT_CROP_OBSERVATION | PLOT_CROP_EXPLANATION | 0..3 | Each explanation has exactly 1 observation | The source has three explanation slots; blank slots create no child row. |

### HarvestLink cooperative-operational model

| Parent | Child / relationship role | Parent-to-child cardinality | Child-to-parent participation | Explanation |
|---|---|---:|---|---|
| COOPERATIVE | COOPERATIVE_MEMBER | 0..* | Each member has exactly 1 cooperative | Member codes are unique within a cooperative. |
| COOPERATIVE | SYSTEM_USER | 0..* | Each user has exactly 1 cooperative | User accounts are scoped to a cooperative. |
| COOPERATIVE | COOP_CROP | 0..* | Each crop catalog entry has exactly 1 cooperative | Cooperative crop catalogue is distinct from NISR CROP. |
| COOPERATIVE | STORAGE_LOCATION | 0..* | Each location has exactly 1 cooperative | Storage locations belong to one cooperative. |
| COOPERATIVE | PRODUCE_RECEIPT | 0..* | Each receipt has exactly 1 cooperative | Each receipt belongs to the receiving cooperative. |
| COOPERATIVE | PHYSICAL_STOCK_COUNT | 0..* | Each count has exactly 1 cooperative | Each count belongs to the cooperative being counted. |
| COOPERATIVE_MEMBER | PRODUCE_RECEIPT | 0..* | Each receipt identifies exactly 1 delivering member | One member may deliver through many receipts. |
| SYSTEM_USER | PRODUCE_RECEIPT (received by) | 0..* | Each receipt has exactly 1 receiving user | A receipt must be inspected/recorded by a user. |
| PRODUCE_RECEIPT | RECEIPT_DETAIL | 0..* | Each detail belongs to exactly 1 receipt | Draft receipts may temporarily have no lines; a posted receipt must have at least one. |
| COOP_CROP | RECEIPT_DETAIL | 0..* | Each detail has exactly 1 cooperative crop | Each receipt line identifies one crop. |
| RECEIPT_DETAIL | STOCK_MOVEMENT (receipt-origin) | 0..* | Each movement references 0..1 receipt detail | A movement may be a transfer, issue or adjustment without a receipt origin. |
| COOP_CROP | STOCK_MOVEMENT | 0..* | Each movement has exactly 1 cooperative crop | Each stock movement affects one crop. |
| STORAGE_LOCATION | STOCK_MOVEMENT (source) | 0..* | Each movement has 0..1 source location | Receipts into stock may have no source location. |
| STORAGE_LOCATION | STOCK_MOVEMENT (destination) | 0..* | Each movement has 0..1 destination location | Issues out of stock may have no destination location. |
| SYSTEM_USER | STOCK_MOVEMENT (recorded by) | 0..* | Each movement has exactly 1 recording user | Every movement records its responsible user. |
| PHYSICAL_STOCK_COUNT | COUNT_DETAIL | 0..* | Each count detail belongs to exactly 1 count | Open counts may be created before details are entered. |
| STORAGE_LOCATION | COUNT_DETAIL | 0..* | Each count detail identifies exactly 1 location | A count line is location-specific. |
| COOP_CROP | COUNT_DETAIL | 0..* | Each count detail identifies exactly 1 crop | One line represents one crop at one location in one count. |
| COUNT_DETAIL | STOCK_RECONCILIATION | 0..1 | Each reconciliation refers to exactly 1 count detail | A count line may be pending reconciliation; at most one reconciliation is proposed per line. |
| STOCK_RECONCILIATION | LOSS_RECORD | 0..* | Each loss record refers to exactly 1 reconciliation | Loss records are created only after investigation confirms a real loss. |
| SYSTEM_USER | PHYSICAL_STOCK_COUNT (created by) | 0..* | Each count has exactly 1 creator | Count initiation is attributable to a user. |
| SYSTEM_USER | PHYSICAL_STOCK_COUNT (reviewed by) | 0..* | Each count has 0..1 reviewer | Review is optional until the count is reviewed. |
| SYSTEM_USER | STOCK_RECONCILIATION (reviewed by) | 0..* | Each reconciliation has 0..1 reviewer | A reviewer is recorded when review occurs. |
| SYSTEM_USER | LOSS_RECORD (confirmed by) | 0..* | Each loss record has exactly 1 confirming user | A loss must be confirmed by an authorized user. |
| SYSTEM_USER | AUDIT_LOG | 0..* | Each audit entry has exactly 1 user | The logical design attributes each audit event to a system user. |

**Cross-entity validation rules:** the receipt member and receiving user must belong to the same cooperative as the receipt; count-detail location and crop must belong to the count’s cooperative; source/destination locations and crop on a stock movement must belong to the same cooperative. These cross-row rules will be implemented and tested in Phase IV/V.

## 6. Business rules reflected in the design

1. A receipt contains one or more receipt-detail lines.
2. A stock movement must identify a crop, positive quantity, movement type, time, and responsible user. A transfer identifies source and destination locations; receipt and issue movements may have only one side.
3. A count event contains count lines, each for one crop at one location.
4. A reconciliation compares the book quantity snapshot with the physical count. Variance can be calculated from these values.
5. If no discrepancy exists, the process bypasses investigation and creates no loss record.
6. A discrepancy is not automatically a loss. Investigation must establish the cause; only a confirmed loss creates a `LOSS_RECORD`.
7. Power BI reporting should read approved views/queries. The dashboard and scheduled synchronization are not transactional entities and do not require their own tables in this ERD.
8. NISR survey farmer identifiers are not cooperative member identifiers. No direct relationship between `SURVEY_FARMER` and `COOPERATIVE_MEMBER` is assumed.

## 7. Data conversion and quality checklist for Phase IV

- Convert `s5q12` from `DD/MM/YYYY` text to a date after loading validation; the assessment parsed all 35,584 values successfully.
- Trim whitespace in `s1q3` (29,332 trailing-space values) and `s2q4` (21 trailing-space values) only during cleaning; keep the original CSV unchanged.
- Keep `s2q7` and `s2q8` as categorical period labels; they are not single dates.
- Keep conditional blanks and document the questionnaire skip logic. Do not replace them indiscriminately with zero or “No”.
- Investigate the conflicting farmer-level/plot-level groups documented in `normalization.md` before consolidating attributes into parent records.
- `s1q2` combines district name and code; parse into separate elements only when an authoritative data dictionary rule is available.
- Numeric values are already represented as numeric in the downloaded CSV; no numeric-text conversion was identified.

## 8. Design boundaries

No Oracle types, sizes, sequences, constraints or PL/SQL are defined here. Those are implementation decisions for later phases. The ERD is logical and describes the survey-reference model and the cooperative-operational model separately.
