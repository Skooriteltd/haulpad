# Haulpad Product Requirements Specification (PRS)

**Version:** 1.0  
**Status:** Implementation baseline (MVP)  
**Derived from:** HaulCentral Product Constitution & Functional Product Document v1.0  
**Market:** United States  
**Primary customer:** Independent and small for-hire trucking carriers operating 3–10 power units  
**Governing rule:** Every feature must improve a material carrier decision or operating outcome, support reliable data capture, or strengthen the economic intelligence layer. Features that fail this test are out of scope for MVP.

This PRS is written for direct implementation. It uses “shall” for mandatory behavior. Where the source document is silent, the PRS defines the minimal unambiguous rule. No additional features, screens, or intelligence are to be invented.

---

## 1. Scope

### 1.1 In scope for MVP (P0)
- Free Load Check (no account required for first evaluation)
- Conversion of an evaluation into a tracked load
- Basic carrier, truck, and driver records
- Configurable cost assumptions (fuel, variable, basic fixed)
- Driver pay configuration for percentage-of-revenue, cents-per-mile, and flat-per-load
- Load lifecycle tracking
- Expense capture and document attachment
- CSV/XLSX import with preview and validation
- Basic driver settlements (calculate, adjust, review, finalize)
- Invoice tracking and payment recording
- Profitability views at load, truck, and simple fleet level
- Predicted-vs-actual comparison
- Attention Inbox with initial leakage findings
- Simple cash outlook (7/14/30-day)
- Tenant isolation, audit trail on material financial fields, data export
- Manual entry + rate-confirmation upload with extraction review

### 1.2 Explicitly out of scope for MVP
- Proprietary load board or marketplace
- Full TMS dispatch, optimization, or driver mobile app
- Payroll tax processing, tax filings, or regulated payroll
- Automated banking, payments execution, factoring, lending, insurance, or fuel-card products
- IFTA filing or compliance automation
- Advanced maintenance management
- Email ingestion
- Third-party API integrations (ELD, fuel card, TMS, accounting)
- Reload / destination predictions
- AI agents that accept loads or execute financially consequential actions
- Multi-carrier agency mode
- Cross-customer benchmarks

---

## 2. Definitions

| Term | Definition |
|------|------------|
| Load Contribution | Expected Load Revenue − Included Trip Costs. Never labeled “net profit” unless fixed operating costs are explicitly included and labeled. |
| Included Trip Costs | Fuel + Driver compensation + Tolls + Trip-specific expenses + other explicitly configured variable costs for that evaluation or trip. |
| Contribution per Total Mile | Load Contribution ÷ (Loaded Miles + Deadhead Miles) |
| Estimated | Calculation based on user-entered or extracted inputs and carrier assumptions before trip execution. |
| Provisional | Calculation that includes some actuals but is not fully reconciled. |
| Reconciled Actual | Calculation after delivery, settlement finalization, and payment recording with all material costs and revenue attributed. |
| Attention Item | A prioritized finding that states what happened, why it matters financially, the evidence, and the next practical action. |
| Tenant | A single carrier organization. All data is isolated by tenant. |

---

## 3. Core Principles (Implementation Constraints)

1. A new user shall obtain a useful Load Check result before any account creation, fleet setup, or integration.
2. Email, PDF, image, spreadsheet, and manual entry are first-class inputs. No integration is required for core value.
3. Data shall be captured as a by-product of useful work (rate confirmation → load record, settlement → cost update, etc.).
4. Every material recommendation shall display: decision, estimated impact, assumptions used, missing information, confidence/reliability indicator, and next action.
5. Estimated, provisional, and reconciled actual results shall always be visually and programmatically distinct. Estimates shall never be silently substituted for actuals.
6. All financial calculations shall be deterministic for a given input set and calculation version.
7. Source documents shall be preserved. Material changes to rates, costs, assignments, or settlements shall be audited.
8. When external services are unavailable, previously saved records and locally available calculations shall remain accessible.
9. The system shall not invent precise figures when critical inputs are missing.

---

## 4. User Roles and Permissions (MVP)

| Role | Permissions |
|------|-------------|
| Owner / Administrator | Full access including settings, cost assumptions, user management, settlement finalization, data export, billing |
| Operations / Dispatcher | Create/evaluate loads, track loads, attach documents, record expenses, view profitability |
| Finance / Back Office | Expenses, settlements (review/finalize), invoices, payments, cash outlook |
| (Driver) | Out of scope for MVP |

Permissions shall be enforced on both UI and server-side operations. Initial customers may have a single user; the model shall still support multiple users.

---

## 5. Data Model (Canonical Entities)

All entities are tenant-scoped. Material financial fields require audit history (who, when, old value, new value, reason if provided).

### 5.1 Carrier
- id, name, address, timezone, currency (default USD)
- fuel_price_assumption (optional default)
- mpg_assumption (optional default)
- fixed_cost_per_mile or fixed_cost_per_day (optional, user-configured)
- variable_cost_categories (configurable list)
- minimum_contribution or minimum_contribution_per_mile (optional thresholds)

### 5.2 Truck
- id, unit_number, year, make, model, status (active/inactive)
- optional current_driver_id

### 5.3 Driver
- id, name, status (active/inactive)
- pay_method (percentage_revenue | cents_per_mile | flat_per_load | hourly)
- pay_rate (numeric, interpretation depends on method)
- effective_from / effective_to dates
- support for future hybrid; MVP implements the three primary methods

### 5.4 Broker / Customer
- id, name, payment_terms_days (optional)

### 5.5 Load
- id, reference_number (user or system)
- status (see state machine below)
- broker_id, truck_id, driver_id (nullable)
- pickup_location, delivery_location (text or ZIP)
- loaded_miles, deadhead_miles
- offered_rate, accessorial_revenue (optional)
- equipment_type (optional)
- evaluation_snapshot_id (link to the calculation used for decision)
- timestamps for each status transition

**Load Status State Machine (MVP)**  
Draft → Evaluated → Accepted → Dispatched → In Transit → Delivered → Ready to Invoice → Invoiced → Paid  

Exception states: Cancelled, Disputed.  
Transitions shall be explicit. Cancelled or incomplete loads shall never be treated as completed profitable trips.

### 5.6 Trip
- May be 1:1 with Load in MVP. Supports future multi-trip.
- actual_loaded_miles, actual_deadhead_miles, actual_fuel_gallons or cost, actual_tolls

### 5.7 Cost Item / Expense
- id, amount, category (fuel, toll, repair, other, driver_pay)
- attributable_to: load_id | trip_id | truck_id | carrier (none)
- receipt_document_id (optional)
- date, description

### 5.8 Revenue Item
- amount, type (base_freight, accessorial, adjustment)
- load_id

### 5.9 Document
- id, type (rate_confirmation, bol, pod, receipt, invoice, settlement, other)
- linked_to: load_id | trip_id | invoice_id | settlement_id
- original_file stored
- extraction_status and extracted_fields (JSON) with confidence flags

### 5.10 Settlement
- id, driver_id, period_start, period_end
- status (draft, reviewed, finalized)
- calculated_amount, adjustments (list), final_amount
- linked load_ids
- calculation_basis snapshot

### 5.11 Invoice
- id, load_id, amount, issue_date, due_date, status (prepared, submitted, paid, partial, overdue, disputed)
- payment_allocations

### 5.12 Payment
- id, amount, date, invoice_id (or allocation), method

### 5.13 Economic Snapshot
- id, load_id or aggregate_id
- type (estimated | provisional | reconciled)
- inputs_hash, calculation_version
- revenue, included_costs, contribution, contribution_per_mile, break_even_rate
- missing_fields list
- created_at

### 5.14 Recommendation / Attention Item
- type, evidence_summary, estimated_impact, suggested_action
- status (open, resolved, dismissed)
- linked_entity_ids
- resolution_reason (optional)

### 5.15 Audit Event
- entity_type, entity_id, field, old_value, new_value, user_id, timestamp, reason

---

## 6. Calculation Engine Specification

All calculations are deterministic and versioned.

### 6.1 Load Contribution (Estimated or Actual)
\[
\text{Load Contribution} = \text{Expected Load Revenue} - \text{Included Trip Costs}
\]

Expected Load Revenue = offered_rate + accessorial_revenue (if provided)

Included Trip Costs =  
- Fuel cost (miles × fuel_price / mpg, or actual fuel cost if known)  
- Driver compensation (calculated per configured method and pay_rate)  
- Tolls (user-entered or actual)  
- Trip-specific expenses (user-entered)  
- Other configured variable costs explicitly marked as trip-level

Contribution per Total Mile:
\[
\text{Contribution per Total Mile} = \frac{\text{Load Contribution}}{\text{Loaded Miles} + \text{Deadhead Miles}}
\]

If loaded_miles or deadhead_miles is missing, the system shall request the value or use a disclosed estimate and mark the result as lower confidence. It shall not invent a precise number.

### 6.2 Break-even and Target Rate
Break-even rate is the rate at which Load Contribution = 0 using the included costs and the carrier’s configured economic objective.  
The system shall state whether fixed costs are included in the calculation.

### 6.3 Driver Compensation Calculation (MVP)
- percentage_revenue: pay_rate × Load Revenue
- cents_per_mile: pay_rate × (Loaded Miles + Deadhead Miles)
- flat_per_load: fixed amount
- Adjustments (bonus, advance, deduction, reimbursement) applied after base calculation with explanation.

Settlements remain draft until explicitly finalized. Finalization locks the calculation basis.

### 6.4 Predicted vs Actual
After delivery + settlement finalization + payment recording, generate a reconciled snapshot and store the variance against the original estimated snapshot.

### 6.5 Rules
- Missing critical inputs → result is returned with explicit missing-fields list and reduced confidence. No silent defaults that create false precision.
- Fixed costs are never automatically included in trip contribution unless the user has configured them as such and the UI labels the result accordingly.
- Currency is USD. Units are miles and USD.

---

## 7. Functional Requirements by Module

### 7.1 Free Load Check (Acquisition Wedge)
**FR-LC-01** A user shall be able to start a Load Check without creating an account.  
**FR-LC-02** Inputs accepted: manual fields, pasted text, or rate-confirmation PDF/image upload.  
**FR-LC-03** Document extraction shall present extracted fields for user review. Uncertain fields shall be flagged and not treated as confirmed until accepted.  
**FR-LC-04** Output shall include: recommendation (Take / Negotiate / Reject) when data supports it, estimated revenue, estimated trip costs, Load Contribution, Contribution per Total Mile, break-even rate, target rate (if objective configured), effect of deadhead, list of missing information, and short explanation.  
**FR-LC-05** Recommendation is economic only; the UI shall note that operational, safety, or contractual constraints are outside the calculation.  
**FR-LC-06** After result, the system shall offer one primary next step: “Track this load in Haulpad”. Secondary options: save result (requires account), start another check.  
**FR-LC-07** Free usage limit shall be configurable (initial suggestion 5–10 evaluations per month per browser/IP or account). Basic manual entry remains available even if extraction fails.  
**FR-LC-08** No credit card required for first useful result.

### 7.2 Load Tracking & Lifecycle
**FR-LT-01** Converting an evaluation creates a Load in Evaluated or Accepted status without re-entry of data.  
**FR-LT-02** Users shall progress loads through the defined state machine.  
**FR-LT-03** Loads can be associated with truck, driver, and broker.  
**FR-LT-04** Search and filter by reference, broker, truck, driver, status, date range, contribution range.  
**FR-LT-05** Material changes (rate, miles, assignment, costs) create audit events.

### 7.3 Expenses, Documents, and Imports
**FR-ED-01** Expenses can be attached to a load, trip, truck, or left unassigned (carrier-level).  
**FR-ED-02** Documents can be uploaded and linked; original file is retained.  
**FR-ED-03** CSV/XLSX import supports preview, column mapping, validation, rejected-row report, duplicate detection, and re-import without duplication. Original file retained.

### 7.4 Driver Settlements
**FR-DS-01** Authorized users configure driver pay method and rate with effective dates.  
**FR-DS-02** System calculates proposed settlement from completed loads and rules.  
**FR-DS-03** Users can add bonuses, advances, deductions, reimbursements with explanations.  
**FR-DS-04** Settlements require explicit review/finalization.  
**FR-DS-05** Finalized driver pay updates related load economic snapshots and creates an audit record.  
**FR-DS-06** Haulpad does not execute tax withholding or payroll filings.

### 7.5 Profit Intelligence
**FR-PI-01** Views available: Load, Truck, simple Fleet (and Lane where sufficient observations exist).  
**FR-PI-02** Every report identifies included cost categories and missing categories.  
**FR-PI-03** Reports distinguish Estimated / Provisional / Reconciled.  
**FR-PI-04** Drill-down from any aggregate to underlying loads, expenses, and documents.  
**FR-PI-05** Small samples and incomplete records are disclosed; no false precision.

### 7.6 Profit Leakage & Attention Inbox
Initial leakage categories that shall be detected when data supports them:
- Underpriced load (contribution below configured threshold)
- Excess deadhead
- Unpaid / uninvoiced accessorial
- Missing proof of delivery
- Uninvoiced completed load
- Unreconciled expense
- Settlement discrepancy
- Delayed / overdue payment
- Unexpected cost variance

**FR-PL-01** Each finding states issue, evidence, estimated impact, and suggested next action.  
**FR-PL-02** Findings ranked by materiality, urgency, and actionability.  
**FR-PL-03** Users can resolve, dismiss (with reason), or inspect source records.  
**FR-PL-04** Duplicate open findings suppressed.  
**FR-PL-05** Potential impact is never reported as realized savings.  
**FR-PL-06** Attention Inbox is the primary home experience: prioritized list of open items answering “what happened, why it matters, evidence, next action”. No wall of KPI cards.

### 7.7 Cash Intelligence
**FR-CI-01** Track outstanding invoices, expected payment dates, overdue flags.  
**FR-CI-02** Track upcoming recorded expenses and settlement obligations.  
**FR-CI-03** Simple outlook for 7, 14, and 30 days distinguishing confirmed vs estimated amounts.  
**FR-CI-04** Explain which items drive the outlook.  
**FR-CI-05** Recorded payments update balances without duplication.

### 7.8 Document-to-Cash Workflow
**FR-DC-01** Documents linked to load / trip / invoice / settlement.  
**FR-DC-02** Extraction always requires review before affecting authoritative records.  
**FR-DC-03** System can assemble a downloadable invoice packet from available verified documents and data.  
**FR-DC-04** Configurable missing-document checklist.  
**FR-DC-05** Invoice status tracking and payment reconciliation (including partial payments).  
**FR-DC-06** Actual collected revenue updates the load’s economic history.

### 7.9 Settings
Users shall be able to configure:
- Carrier profile and cost assumptions
- Driver pay methods
- Economic thresholds
- Users and roles
- Data export

---

## 8. Navigation and Primary Screens (Behavior)

Compact navigation:
1. Today (Attention Inbox)
2. Load Check
3. Loads
4. Fleet (Trucks & Drivers)
5. Money (Settlements, Expenses, Invoices, Payments)
6. Intelligence (Profitability views)
7. Settings

Home screen shall open on the Attention Inbox. Empty state shall guide the user to perform a Load Check or import data.

---

## 9. Security, Audit, and Trust

- Strict tenant isolation at data access and authorization layers. Cross-tenant access tests required.
- Sensitive documents encrypted in transit and at rest; access-controlled.
- Material financial changes audited.
- Backups and tested restore procedures before production.
- Import validation and recoverable failures.
- Failures shall be observable and shall not silently produce misleading financial results.
- Data export available to Owner.

---

## 10. Non-Functional Requirements (MVP)

- Deterministic calculations.
- Extraction failure does not block manual entry.
- Core Load Check usable without account.
- Progressive disclosure: useful result before setup.
- Auditability of economic results.
- Graceful degradation when optional services are unavailable.

---

## 11. MVP Acceptance Criteria

The product is ready only when the following are true:

**Load Check**
- Evaluation possible without full fleet configuration.
- Assumptions and included costs exposed.
- Missing critical inputs identified.
- Recommendation reproducible from saved inputs.
- Conversion to tracked load requires no re-entry.

**Document Extraction**
- Extracted values shown for review.
- Uncertain fields not silently confirmed.
- Original document retained.
- Extraction failure does not prevent manual path.

**Lifecycle & Settlement**
- Loads progress through defined statuses.
- Material changes audited.
- Pay rules produce reproducible settlements.
- Finalized compensation updates economics.

**Profitability & Leakage**
- Source records inspectable.
- Estimated vs reconciled distinguished.
- Missing costs disclosed.
- Every finding has evidence and next action.
- Potential recovery not reported as realized savings.

**Security**
- Cross-tenant access prevented.
- Authorization enforced server-side.
- Backups and restoration tested.

---

## 12. Implementation Notes for Coding Agent

- Implement the calculation engine as a pure, versioned function that accepts a structured input object and returns a structured result with missing_fields and confidence indicators.
- Persist Economic Snapshots for every evaluation and every material recalculation.
- Attention Inbox is a query over open Recommendation records ranked by a simple scoring function (impact × urgency × confidence).
- Do not build dashboards, charts, or AI features beyond what is required for the Attention Inbox and explicit calculations.
- Prefer explicit state machines and audit logs over implicit behavior.
- All monetary values stored as decimal (or integer cents) to avoid floating-point issues.
- Start with the Free Load Check + conversion path; everything else builds on the Load and Economic Snapshot records.

This PRS defines the complete MVP. Any deviation or additional feature requires an explicit change request against the product principles in Section 3.
