# Phase 0 presentation — suggested talk track

1. **Title:** Introduce HarvestLink Rwanda, your identity, course and repository.
2. **Background/problem:** Storage and post-harvest loss have been studied in Rwanda. The warehouse reconciliation workflow is the prototype problem, not a measured national finding.
3. **Motivation:** Explain the value of traceable records and focused reporting. Acknowledge that existing cooperative systems already provide inventory functions.
4. **Objectives:** Walk through the main objective and four specific objectives.
5. **Research:** Summarize the two peer-reviewed studies and UGAMA Coops DMS. Explain that your distinction is a narrow workflow, not a claim that no system exists.
6. **Dataset:** Name the NISR Season B 2024 dataset, size, key variables, limitation and reuse condition. State that it is plot-level and not a warehouse ledger. Do not display raw microdata if its terms prohibit redistribution. Slide examples labelled SYN are synthetic test data, not survey observations.
7. **Solution/innovation:** Explain Oracle as the database, PL/SQL as the business-rule layer and Power BI as the dashboard.
8. **Database plan:** Present the preliminary entities. Explain that final keys and normalization are for the next phases.
9. **PL/SQL:** Give one concrete example for each feature (for example, `move_stock` rejects a movement larger than available stock).
10. **Expected results:** Describe what you plan to demonstrate, not what is already built.
11. **Limitations:** Be honest: no interview, no cooperative transaction data, and Power BI connection not yet tested. Ask instructor to confirm dataset/test-data approach.
12. **References:** Point to the papers, NISR, UGAMA, Oracle and Microsoft sources.

## Important phrases for the defence

- “The cooperative scenario is hypothetical; I did not conduct an interview.”
- “NISR's dataset is real and has 35,584 Season B production records, but it is plot-level survey data, not a warehouse ledger.”
- “Any synthetic operational test rows will be clearly marked and separated from the NISR records.”
- “Coops DMS already advertises cooperative inventory management. My proposed scope is a narrower audit and stock-variance workflow, subject to instructor approval.”
- “The Power BI connection is planned; I will only claim it works after a live Oracle connection is demonstrated.”
