# HarvestLink Rwanda
## Cooperative Stock Reconciliation and Post-Harvest Loss Monitoring System

**Student:** JABO Ghislain  
**Student ID:** 20252SEN163  
**Group:** I — Monday  
**Institution:** Adventist University of Central Africa (AUCA)  
**Course:** INSY 8311 — Database Development with PL/SQL  
**Instructor:** Eric Maniraguha  
**GitHub:** https://github.com/jaboghislain5-blip/JABO_20252SEN163_Harvestlink  
**Date:** 7 November 2026

### 1. Problem Statement

Post-harvest storage and food-loss measurement are important agricultural issues. A Rwanda study of 301 smallholder maize farmers in Gatsibo reported that 41% used hermetic storage technology, while a regional review highlights the difficulty of measuring post-harvest losses consistently (Benimana et al., 2021; Sheahan & Barrett, 2017). NISR's Season B 2024 survey also records plot-level harvested, stored and lost quantities. However, these public agricultural sources do not provide the day-to-day cooperative warehouse ledger needed to trace every receipt, movement and physical stock count.

Rwanda already has cooperative platforms with broad inventory functions, including UGAMA's Coops DMS. HarvestLink will therefore not claim to be the first cooperative inventory system or a replacement for existing platforms. It proposes a narrower academic prototype focused on traceable produce receipts and movements, post-harvest loss records, physical stock counts and manager-reviewed stock variances. The cooperative workflow is a hypothetical design scenario because no direct stakeholder interview has been conducted; assumptions will be presented honestly and refined if instructor feedback is received.

### 2. Proposed Solution and Objectives

HarvestLink will use Oracle Database and PL/SQL to keep inventory records and enforce business rules. Storekeepers will record produce receipts, stock movements, losses and physical counts. The database will calculate recorded balances and variances, preserve an audit trail, and provide reporting views for management. Power BI will connect to Oracle to present inventory, loss and variance indicators.

**Main objective:** Design and implement an Oracle database system for auditable cooperative stock reconciliation and post-harvest loss monitoring.

**Specific objectives:**
- Record produce receipts, storage movements and losses with dates, quantities and reasons.
- Calculate available recorded stock and compare it with physical stock counts.
- Apply validation, approval and audit rules using PL/SQL.
- Present crop, stock, loss and variance summaries in a Power BI dashboard connected to Oracle.

### 3. Data Source

The candidate real dataset is NISR's *Season B: Crop Production* from the 2023/2024 Seasonal Agricultural Survey. The NISR microdata catalogue lists 35,584 cases and 97 variables for the Season B production file. Relevant fields include crop identification, harvested and stored quantities, storage facility, public-storage quantity and post-harvest losses. The dataset is plot/household survey data, not cooperative warehouse transactions; it will be used for agricultural reference and crop/loss analysis, not misrepresented as a warehouse ledger. NISR says to cite the dataset and not redistribute it without permission. Only suitable non-identifying fields will be used, and the raw file will not be uploaded to GitHub. Any additional operational test transactions will be explicitly labelled synthetic and kept separate from NISR observations. Instructor confirmation of this data plan is required if only real operational records are accepted.

### 4. Planned Tables

Preliminary tables: `COOPERATIVES`, `USERS`, `SUPPLIERS`, `CROPS`, `STORAGE_LOCATIONS`, `PRODUCE_RECEIPTS`, `RECEIPT_DETAILS`, `STOCK_MOVEMENTS`, `LOSS_RECORDS`, `PHYSICAL_COUNTS`, `RECONCILIATIONS`, `INVENTORY_AUDIT`, and a minimized `NISR_SAS_REFERENCE` staging table. Final relationships and normalization will be confirmed during database modelling.

### 5. Planned PL/SQL Features

- **Procedures:** `record_receipt`, `move_stock`, `record_loss` and `approve_reconciliation` to enforce validated inventory operations.
- **Functions:** calculate recorded stock by crop/location and loss percentage for a defined receipt or period.
- **Package:** group inventory procedures, functions and shared validation logic.
- **Triggers and audit:** record important changes and prevent invalid stock updates where appropriate.
- **Cursors and exceptions:** generate inventory/loss summaries and handle invalid quantities, unavailable stock or unapproved adjustments.

### 6. Innovation

**Analytics dashboard — Microsoft Power BI connected to Oracle Database.** The dashboard will display produce received, recorded available stock, storage and other post-harvest losses, physical-versus-recorded stock variance, and monthly trends. Core business rules remain in PL/SQL; Power BI is the interactive analytics layer. Microsoft documents Oracle connectivity for Power BI, with provider/client configuration depending on the installed Power BI version. A working live connection will be tested and demonstrated; static screenshots alone will not be treated as a successful integration.

### References

Benimana, G. U., Ritho, C., & Irungu, P. (2021). Assessment of factors affecting the decision of smallholder farmers to use alternative maize storage technologies in Gatsibo District-Rwanda. *Heliyon, 7*(10), e08235. https://doi.org/10.1016/j.heliyon.2021.e08235

Microsoft. (n.d.). *Oracle database connector*. Microsoft Learn. Retrieved November 7, 2026, from https://learn.microsoft.com/en-us/power-query/connectors/oracle-database

National Institute of Statistics of Rwanda. (2024). *Seasonal Agricultural Survey — Season B, 2024*. https://gis.statistics.gov.rw/statistical-publications/agriculture-environment/seasonal-agricultural-survey-season-b-2024

National Institute of Statistics of Rwanda. (2026). *Season B: Crop Production* [Dataset]. Data Sharing Platform. Retrieved November 7, 2026, from https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a

Oracle. (2024). *Oracle Database PL/SQL Language Reference* (21c). https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/index.html

Sheahan, M., & Barrett, C. B. (2017). Review: Food loss and waste in Sub-Saharan Africa. *Food Policy, 70*, 1–12. https://doi.org/10.1016/j.foodpol.2017.03.012

UGAMA. (n.d.). *Coops DMS — Cooperatives Digital Management and Monitoring System*. https://ugama.org.rw/cdms/
