# Research Log — HarvestLink Rwanda

**Project:** Cooperative Stock Reconciliation and Post-Harvest Loss Monitoring System  
**Researcher:** JABO Ghislain  
**Student ID:** 20252SEN163  
**Prepared:** 7 November 2026  
**Status:** Initial desk research; field validation pending.

## 1. Research Objective

The purpose of this research is to understand agricultural post-harvest losses, storage practices, existing agricultural digital systems, and relevant technologies for developing HarvestLink Rwanda.

The research will also help identify a practical gap that can be addressed by an Oracle database system.

## 2. Research Sources

### Source 1 — Research Paper

**APA reference:**

Benimana, G. U., Ritho, C., & Irungu, P. (2021). Assessment of factors affecting the decision of smallholder farmers to use alternative maize storage technologies in Gatsibo District-Rwanda. Heliyon, 7(10), e08235.

https://doi.org/10.1016/j.heliyon.2021.e08235

**Key finding:** The study examined storage technology choices among 301 smallholder maize farmers in Gatsibo District. It identified factors such as farmer-group membership, access to credit, training and production quantity.

**Relevance:** Helps explain the importance of storage practices and farmer groups in reducing post-harvest losses.

**Limitation:** Focuses on maize storage technology adoption rather than cooperative inventory records or stock reconciliation.

### Source 2 — Research Review

**APA reference:**

Sheahan, M., & Barrett, C. B. (2017). Review: Food loss and waste in Sub-Saharan Africa. Food Policy, 70, 1–12.

https://doi.org/10.1016/j.foodpol.2017.03.012

**Key finding:** The review discusses post-harvest losses across different stages of agricultural value chains and highlights challenges in measuring and assessing losses.

**Relevance:** Provides broader context for understanding post-harvest losses and why accurate measurement matters.

**Limitation:** Covers Sub-Saharan Africa generally rather than the specific inventory practices of Rwandan cooperatives.

### Source 3 — Additional Research Paper

**APA reference:**

Benimana, G. U., Ritho, C., & Irungu, P. (2023). Impact of adopting maize hermetic storage technologies on smallholder farmers' income in Gatsibo District, Rwanda. Heliyon, 9(3), e14592.

https://doi.org/10.1016/j.heliyon.2023.e14592

**Key finding:** The study reports a positive and significant income impact among farmers who adopted hermetic maize storage technologies.

**Relevance:** Supports investigation of how improved storage practices relate to agricultural outcomes.

**Limitation:** Focuses on storage technology and farmer income, not cooperative stock reconciliation. Its sample may overlap with the 2021 study and should be checked.

### Source 4 — Official Agricultural Statistics Report

**APA reference:**

National Institute of Statistics of Rwanda. (2024). Seasonal Agricultural Survey — Season B, 2024.

https://www.statistics.gov.rw/statistical-publications/agriculture-environment/seasonal-agricultural-survey-season-b-2024

**Key finding:** The survey covered all 30 districts of Rwanda and collected information from 1,200 segments and 384 large-scale farmers.

**Relevance:** Provides official national agricultural context for the project.

**Limitation:** National agricultural statistics do not directly describe the stock-management procedures of an individual cooperative.

### Source 5 — Existing Agricultural Digital System

**APA reference:**

e-HingiriSoko. (n.d.). e-HingiriSoko: Digital markets ecosystem platform.

https://ehingirisoko.digital/

**Key finding:** The platform describes services for farmer digitization, market linkages, agricultural information, aggregation, trading and data collection.

**Relevance:** Helps identify existing digital agricultural services and avoid duplicating a broad agricultural marketplace.

**Potential distinction:** HarvestLink will focus specifically on cooperative inventory records, stock reconciliation and post-harvest loss monitoring.

**Limitation:** Its public website does not establish whether it already supports our exact proposed workflow.

### Source 6 — Dataset

**APA reference:**

National Institute of Statistics of Rwanda. (2026). Season B: Crop Production [Dataset]. Data Sharing Platform.

https://data.gov.rw/datasets/eb96d559-71aa-430e-b00c-09da8fbeaf1a

**Related microdata catalogue:**

https://www.microdata.statistics.gov.rw/index.php/catalog/113

**Key information:** The dataset includes crop types, harvested quantities, production utilization, storage and post-harvest losses.

The microdata catalogue reports 35,584 cases and 97 variables for the Season B production file.

**Relevance:** May support agricultural context and analysis of crop production and reported post-harvest losses.

**Limitation:** It is plot-level survey data, not a cooperative inventory transaction database. It does not directly provide the physical stock counts and stock movements needed to demonstrate reconciliation.

**Data-use note:** The data-sharing platform requires citation and prohibits redistribution without permission. Do not upload the raw dataset to this public repository unless permission is obtained.

### Source 7 — Oracle Technical Documentation

**APA reference:**

Oracle. (2024). Oracle Database PL/SQL Language Reference (21c).

https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/index.html

**Key information:** Official documentation covering PL/SQL programming, stored subprograms, triggers, packages and error handling.

**Relevance:** Supports the technical implementation of the project's database logic.

### Source 8 — Power BI Technical Documentation

**APA reference:**

Microsoft. (n.d.). Oracle database connector. Microsoft Learn. Retrieved November 7, 2026, from

https://learn.microsoft.com/en-us/power-query/connectors/oracle-database

**Key information:** Explains how Power Query and Power BI connect to Oracle databases, including prerequisites and connection methods.

**Relevance:** Supports implementation of the planned Power BI analytics dashboard connected to Oracle.

## 3. Preliminary Research Observations

The reviewed sources show that:

1. Post-harvest losses and storage practices are important agricultural research topics in Rwanda.
2. Improved storage technologies can have positive agricultural and economic outcomes.
3. Rwanda already has digital agricultural platforms offering market and farmer services.
4. NISR provides useful agricultural survey data for research and analysis.
5. Oracle PL/SQL and Power BI provide suitable technical documentation for the planned implementation.

## 4. Research Gap to Investigate

The reviewed sources do not yet establish how a particular agricultural cooperative records received produce, monitors internal stock movements, compares recorded quantities with physical stock, or documents losses.

This is the gap that needs further investigation before the final problem statement is confirmed.

## 5. Field Research Pending

A stakeholder interview is still needed to investigate:

- How produce is received and recorded.
- How stock movements are tracked.
- How physical stock counts are conducted.
- Whether stock differences occur and how they are handled.
- How post-harvest losses are documented.
- Which reports cooperative staff need.

No interview findings have been claimed in this document.

## 6. Next Research Actions

- Read the full research papers and relevant NISR report sections.
- Check the actual NISR dataset variables and access conditions.
- Investigate the existing e-HingiriSoko platform further.
- Conduct a stakeholder interview when available
- Refine the problem statement and proposed database requirements.