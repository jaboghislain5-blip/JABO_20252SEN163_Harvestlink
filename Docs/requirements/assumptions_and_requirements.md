# HarvestLink Rwanda — Assumed Requirements

**Project:** Cooperative Stock Reconciliation and Post-Harvest Loss Monitoring System  
**Student:** JABO Ghislain  
**Student ID:** 20252SEN163

## 1. Basis of Requirements

This document defines preliminary requirements for a hypothetical agricultural cooperative scenario. No direct stakeholder interview has been conducted. The requirements are design assumptions informed by publicly available agricultural research and reports. The hypothetical organization “HarvestLink Cooperative” is a modelling example, not a claimed real registered cooperative.

## 2. Assumed Users

**Storekeeper:** records produce receipts, stock movements and losses, and performs physical stock counts.

**Cooperative manager:** reviews stock balances and discrepancies, approves adjustments and views reports.

**System administrator:** manages user access and system configuration.

## 3. Functional Requirements

- FR1: Maintain crop and produce-type records.
- FR2: Maintain storage-location records.
- FR3: Record produce receipts, including crop, quantity, unit, date and supplier reference.
- FR4: Record stock movements, including transfers between storage locations and dispatches.
- FR5: Record post-harvest losses, including quantity, date and reason.
- FR6: Calculate recorded stock balances.
- FR7: Record physical stock counts.
- FR8: Calculate variances between recorded balances and physical counts.
- FR9: Allow authorized managers to review and approve stock adjustments.
- FR10: Generate inventory and post-harvest loss reports.
- FR11: Preserve an audit trail of important inventory operations.

## 4. Proposed Business Rules

1. Quantities entered for receipts, movements and losses must be greater than zero.
2. A stock movement cannot remove more produce than is available at the source location.
3. A recorded loss cannot exceed the available quantity in the relevant stock batch.
4. Each receipt must identify the produce type, quantity, unit and receipt date.
5. Each loss record must include a reason and date.
6. A physical stock count must not silently overwrite the recorded balance.
7. Variances must be reviewed before an adjustment is approved.
8. Only authorized users may approve stock adjustments.
9. Important inventory changes must be audited.

## 5. Preliminary Calculations

**Recorded stock balance:** opening stock + receipts + transfers in − transfers out − recorded losses.

**Stock variance:** physical stock count − recorded stock balance.

A negative variance indicates a shortage; a positive variance indicates that physical stock exceeds the recorded balance.

**Post-harvest loss percentage:** (loss quantity ÷ defined starting quantity for the same batch/period) × 100. The denominator must be stated in every report; it should not mix different batches or units.

## 6. Preliminary Entities

Cooperatives, users, suppliers, crops, storage locations, produce receipts, receipt details, stock movements, loss records, physical stock counts, stock reconciliations and inventory audit records. A minimized NISR SAS reference/staging area may also be designed. Final tables and relationships will be decided during normalization and ERD work.

## 7. Project Boundaries

The initial project focuses on cooperative inventory receipts, movements, post-harvest loss records, physical stock counts and variance review. It excludes a public marketplace, buyer matching, transport route tracking, food donation management, payroll and full cooperative accounting.

## 8. Limitations

The requirements are based on a hypothetical workflow and secondary research. Actual cooperative practices may differ. The NISR data source is plot/household-level survey information, not a complete cooperative warehouse transaction ledger. Any synthetic operational transactions used for testing must be labelled and kept separate from actual NISR observations.
