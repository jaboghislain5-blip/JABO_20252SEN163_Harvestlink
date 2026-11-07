# HarvestLink Rwanda — Assumed Requirements

**Project:** Cooperative Stock Reconciliation and Post-Harvest Loss Monitoring System
**Student:** JABO Ghislain
**Student ID:** 20252SEN163

## 1. Basis of Requirements

This document defines preliminary requirements for a hypothetical agricultural cooperative scenario. No direct stakeholder interview has been conducted. The requirements are design assumptions informed by publicly available agricultural research and reports.

The hypothetical organization used for system modelling is called HarvestLink Cooperative. It is not being presented as a real registered cooperative.

## 2. Assumed Users

**Storekeeper**

- Records incoming produce.
- Records stock movements.
- Documents losses.
- Performs physical stock counts.

**Cooperative Manager**

- Reviews stock balances.
- Reviews stock discrepancies.
- Approves inventory adjustments.
- Views management reports.

**System Administrator**

- Manages user accounts and access permissions.
- Maintains system configuration.

## 3. Functional Requirements

FR1. The system shall maintain records of crops and produce types.

FR2. The system shall maintain storage-location records.

FR3. The system shall record produce received, including the crop, quantity, unit, date and supplier reference.

FR4. The system shall record stock movements, such as transfers between storage locations and dispatches.

FR5. The system shall record post-harvest losses, including quantity, date and reason.

FR6. The system shall calculate the current recorded stock balance.

FR7. The system shall allow users to record physical stock counts.

FR8. The system shall calculate differences between recorded stock and physical stock.

FR9. The system shall allow an authorized manager to review and approve stock adjustments.

FR10. The system shall generate inventory and post-harvest loss reports.

FR11. The system shall preserve an audit trail of important inventory operations.

## 4. Proposed Business Rules

1. All recorded quantities must be greater than zero.
2. A stock movement cannot remove more produce than is available.
3. A recorded loss cannot exceed the available quantity in the relevant stock batch.
4. Each receipt must identify the produce type, quantity, unit and receipt date.
5. Each loss record must include a reason and a date.
6. Physical stock counts must not silently overwrite recorded stock balances.
7. Differences between physical and recorded quantities must be reviewed.
8. Only authorized users may approve stock adjustments.
9. Important inventory changes must be recorded for auditing.

## 5. Preliminary Stock Calculations

**Recorded stock balance:**

Opening stock + receipts + transfers in − transfers out − recorded losses.

**Stock variance:**

Physical stock count − recorded stock balance.

A negative variance indicates a shortage. A positive variance indicates that physical stock exceeds the recorded balance.

**Post-harvest loss percentage:**

For an individual batch:

(Total recorded loss quantity ÷ Total quantity received for that batch) × 100.

## 6. Preliminary Database Entities

The following entities are proposed for further design:

- Cooperatives
- Users
- Suppliers
- Crops
- Storage locations
- Produce receipts
- Receipt details
- Stock movements
- Loss records
- Physical stock counts
- Stock reconciliation records
- Inventory audit records

The final tables and relationships will be confirmed during database modelling.

## 7. Proposed Reports

- Current stock by crop and storage location.
- Produce received by month.
- Stock movement history.
- Post-harvest losses by crop and reason.
- Physical stock versus recorded stock.
- Monthly stock discrepancy trends.

## 8. Project Boundaries

The initial project will focus on cooperative inventory records, physical stock reconciliation and post-harvest losses.

It will not initially implement:

- A public agricultural marketplace.
- Online buyer-seller transactions.
- Transport route tracking.
- Cooperative accounting and payroll.
- A complete cooperative management platform.

## 9. Limitations

These requirements are based on a hypothetical scenario and secondary research. Actual cooperative practices may differ.

The publicly identified NISR agricultural dataset provides agricultural survey information, not a complete cooperative warehouse transaction ledger. Any simulated transaction records used for software testing must be labelled as simulated rather than real observations.
