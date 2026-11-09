# Research Log — HarvestLink Rwanda

**Project:** Cooperative Stock Reconciliation and Post-Harvest Loss Monitoring System  
**Researcher:** JABO Ghislain  
**Student ID:** 20252SEN163  
**Course:** Database Development with PL/SQL (INSY 8311)  
**Research status:** Desk research completed for proposal drafting; cooperative workflow remains a hypothetical design scenario.

> **Integrity note:** No cooperative interview has been conducted. User roles and workflow requirements are assumptions for an academic prototype, not statements attributed to a real cooperative. Read the cited source before using a finding in a graded presentation or defence.

## Source evaluation table

| # | Source (APA) | Type | Link | Key finding | Gap / relevance to HarvestLink |
|---|---|---|---|---|---|
| 1 | Benimana, G. U., Ritho, C., & Irungu, P. (2021). Assessment of factors affecting the decision of smallholder farmers to use alternative maize storage technologies in Gatsibo District-Rwanda. *Heliyon, 7*(10), e08235. https://doi.org/10.1016/j.heliyon.2021.e08235 | Peer-reviewed research paper | https://pmc.ncbi.nlm.nih.gov/articles/PMC8561309/ | Surveyed 301 smallholder maize farmers in Gatsibo; the paper reports that 41% used hermetic storage technology and discusses factors associated with storage-technology choice. | Supports the importance of storage practices and post-harvest-loss prevention in Rwanda. It studies household/farmer technology adoption, not cooperative warehouse ledgers or stock reconciliation. |
| 2 | Sheahan, M., & Barrett, C. B. (2017). Review: Food loss and waste in Sub-Saharan Africa. *Food Policy, 70*, 1–12. https://doi.org/10.1016/j.foodpol.2017.03.012 | Peer-reviewed literature review | https://pmc.ncbi.nlm.nih.gov/articles/PMC5555439/ | Reviews objectives, estimates and measurement methods for post-harvest food loss and waste in Sub-Saharan Africa, and discusses limitations in how loss estimates are produced. | Provides regional context and warns that loss measurement requires careful methods. It does not describe the day-to-day stock-control practices of a specific Rwandan cooperative. |
| 3 | National Institute of Statistics of Rwanda. (2024). *Seasonal Agricultural Survey — Season B, 2024*. https://gis.statistics.gov.rw/statistical-publications/agriculture-environment/seasonal-agricultural-survey-season-b-2024 | Official report / statistics | https://gis.statistics.gov.rw/statistical-publications/agriculture-environment/seasonal-agricultural-survey-season-b-2024 | The Season B survey covered all 30 districts; NISR reports 1,200 segments and 384 large-scale farmers for the survey. The reference period was March–June 2024. | Gives current national agricultural context and coverage. National survey statistics are not a substitute for a cooperative's own warehouse transaction records. |
| 4 | UGAMA. (n.d.). *Coops DMS — Cooperatives Digital Management and Monitoring System*. https://ugama.org.rw/cdms/ | Existing system | https://ugama.org.rw/cdms/ | The public description lists cooperative book/management functions and asset and inventory management, including stores and products; it also lists sales, purchases and financial management. | This is an existing Rwandan system in the same broad area. HarvestLink must not claim to be the first cooperative inventory system. Its academic scope is narrower: auditable receipts/movements, physical-count variance review and loss analysis. The public page does not establish whether every proposed workflow is already supported, so the distinction is a scope choice, not a proven product gap. |
| 5 | National Institute of Statistics of Rwanda. (2026). *Season B: Crop Production* [Dataset]. Data Sharing Platform. Retrieved November 7, 2026, from https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a | Dataset | https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a | The dataset describes plot-level production for Season B (March–June 2024), including crop, harvested/remaining quantities, utilization, storage and post-harvest losses by cause. NISR's microdata data dictionary lists 35,584 cases and 97 variables for the Season B production file. | Real Rwanda data with more than a few hundred cases and relevant agricultural fields. It is plot/household survey data, not a cooperative transaction ledger and does not provide warehouse stock movements or physical counts. Use only appropriate non-identifying fields; cite NISR; do not redistribute the raw file without permission. |
| 6 | Oracle. (2024). *Oracle Database PL/SQL Language Reference* (21c), F31827-07. https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/index.html | Technical source | https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/index.html | Official Oracle 21c reference documents PL/SQL subprograms, triggers, packages, error handling and related language features. | Provides the authoritative technical basis for implementing stock rules, validations and audit behaviour in PL/SQL. |
| 7 | Microsoft. (n.d.). *Oracle database connector*. Microsoft Learn. Retrieved November 7, 2026, from https://learn.microsoft.com/en-us/power-query/connectors/oracle-database | Technical source | https://learn.microsoft.com/en-us/power-query/connectors/oracle-database | Microsoft documents an Oracle connector for Power BI/Power Query, supported Oracle versions, connection modes and required client/provider setup. | Supports the proposed Power BI analytics layer. A working connection on the student's Windows/Power BI setup must still be tested; documentation does not prove the local environment is configured. |
| 8 | National Institute of Statistics of Rwanda. (2025). *Microdata Release Policy*. https://statistics.gov.rw/publications/microdata-release-policy | Data governance / reuse policy | https://statistics.gov.rw/publications/microdata-release-policy | NISR describes conditions for releasing anonymized microdata for research while protecting respondent confidentiality. NISR's general website also states that data and analysis are CC BY 4.0 unless otherwise indicated. | Dataset-specific conditions still apply. The Season B Data Sharing Platform page says to cite the dataset and not redistribute it without permission. Use the official download for analysis only; do not commit the raw data to the public GitHub repository. |

## Dataset suitability and use plan

**Selected candidate:** NISR Season B: Crop Production, from the 2023/2024 Seasonal Agricultural Survey.

**Size:** NISR's microdata catalogue lists 35,584 cases and 97 variables for `Rwa_raw_SeasonB2024_Production`. The Data Sharing Platform page displays 98 columns. This metadata difference must be reconciled against the downloaded CSV/data dictionary before data cleaning and normalization.

**Relevant variable families:** crop name/category; harvested and remaining quantity; quantity stored; storage facility; quantity stored in public storage; and loss quantities associated with different post-harvest stages, including transport and storage. Use the official data dictionary to verify each variable label before mapping it to a database column.

**How it will be used:** load a permitted, minimized subset of non-identifying agricultural variables into a staging/reference area for agricultural analysis and crop/loss reporting. Do not use farmer identifiers, names or other identifying fields in the dashboard. The source does not contain daily cooperative receipts, stock transfers or physical-count records.

**Operational demonstration data:** if test transactions are required to exercise receipt, transfer, count and reconciliation procedures, they must be labelled `SYNTHETIC_TEST_DATA` and kept separate from the NISR observations. Never describe synthetic rows as measured cooperative records. The instructor should confirm that this combination satisfies the project's real-data requirement; if not, obtain an approved transaction dataset or redesign the scope around the NISR survey variables.

**Reuse conditions:** NISR's Season B dataset page states that the dataset may be reused and cited in research publications, and separately instructs users not to redistribute the data without permission. Cite the original NISR source and keep the raw file out of the public repository.

## Research gap and project positioning

The papers and NISR survey support the importance of measuring storage and post-harvest losses. UGAMA's Coops DMS already publicly lists broad cooperative inventory-management functions. Therefore HarvestLink is positioned as a small academic prototype concentrating on the audit trail from produce receipt to stock movement, loss recording, periodic physical count and manager-reviewed variance. The public sources do not prove that cooperatives widely experience the specific recordkeeping issues assumed in this prototype, nor do they prove that existing platforms lack these features.

## Assumptions requiring instructor or stakeholder validation

1. A storekeeper records each produce receipt and stock movement.
2. A cooperative may need to compare its calculated stock balance with a later physical count.
3. A cooperative manager may review and authorize inventory adjustments.
4. Loss records can be categorized by crop, date, quantity and cause.
5. Management may benefit from monthly stock, loss and variance summaries.

These are design assumptions, not interview findings or national prevalence claims.

## Next research actions

- Download the official NISR CSV and data dictionary directly from the Data Sharing Platform / NISR catalogue.
- Check the exact number of rows, variables, missing values, units and permitted variables.
- Confirm with the instructor that the NISR agricultural dataset plus separately labelled synthetic operational test data is acceptable for the warehouse workflow.
- Keep the source file private in the working environment unless redistribution permission is granted.
- Revise the project scope if instructor feedback identifies overlap with Coops DMS or the already-registered class topics.
