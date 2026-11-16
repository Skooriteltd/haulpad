# Haulpad Product Requirements Specification (PRS)

**Version:** 1.3  
**Status:** Implementation baseline — Full original capabilities + Competitive Parity with CarriersPay  
**Derived from:** HaulCentral Product Constitution & Functional Product Document v1.0 + CarriersPay feature review  
**Market:** United States  
**Primary customer:** Independent and small for-hire trucking carriers operating 3–10 power units  

**Governing rules:**
1. Every feature must improve a material carrier decision or operating outcome, support reliable data capture, or strengthen the economic intelligence layer.
2. Haulpad shall include every material capability offered by CarriersPay so that a carrier comparing the two products has no functional reason to choose CarriersPay.
3. Haulpad’s economic intelligence layer (contribution analysis, leakage detection, prediction-to-actual learning, Attention Inbox) is the primary differentiator on top of operational parity.

This PRS is written for direct implementation. It uses “shall” for mandatory behavior. No additional features are to be invented beyond what is specified.

---

## 1. Scope

### 1.1 In scope (required)

**Acquisition & Economic Intelligence (original Haulpad thesis)**
- Free Load Check with no account required for the first useful result
- Conversion of an evaluation into a tracked load without re-entry
- Load contribution, contribution-per-total-mile, break-even rate, and target-rate calculations
- Scenario comparison (rate, deadhead, driver pay variations)
- Negotiation guidance (rate required to meet configured economic objective)
- Decision history (recommendation, assumptions, user decision)
- Predicted-versus-actual comparison after reconciliation
- Profit Intelligence views at load, truck, lane, broker, and fleet levels
- Profit Leakage Engine with the defined categories
- Attention Inbox as the primary home experience
- Cash Intelligence (7/14/30-day outlook with confirmed vs estimated distinction)
- Document-to-cash workflow with invoice packet and missing-document checks

**Operating Modes**
- Lean Carrier mode (lightweight workspace for carriers without TMS/accounting integrations)
- Connected Carrier mode (same data model enriched by imports and later integrations)
- Ability to progress from Lean to Connected without data migration

**Settlements, Dispatch & Driver Experience (CarriersPay parity)**
- Four driver pay methods: percentage of revenue, cents-per-mile (loaded/empty/both selectable), hourly with optional mileage add-on, flat rate per load
- Office employee statements
- Document-assisted settlement generation
- At least 11 branded statement templates
- PDF generation and email from carrier SMTP
- Drafts, adjustments (fuel, escrow, advances, deductions, reimbursements, bonuses), paid vs unpaid tracking
- Load dispatch to driver
- Driver mobile experience (iOS & Android): loads, check-in/check-out, navigation, document upload, statements, messaging, notifications
- Office mobile experience

**Documents, Compliance, Financials & Reporting (CarriersPay parity)**
- Permanent document library
- AI/document extraction with mandatory review before commit
- Safety & compliance center (CDL, medical, MVR, insurance, permits, DQ files, driver applications) with expiration reminders
- IFTA miles and fuel tracking by jurisdiction with quarterly summary export
- 1099 contractor totals tracking and year-end export
- My Network (auto-built broker/shipper directory with performance aggregates)
- Financial dashboard and company reports (P&L, expenses, customer, driver) in PDF/CSV
- Invoicing with branding and email
- Truck/trailer maintenance logging
- QuickBooks Online manual import/export
- Automatic mileage estimation
- CSV/XLSX import with validation and duplicate detection

**Platform**
- Tenant isolation, full audit trail on material financial fields, data export
- Role-based access
- Configurable cost assumptions and economic thresholds

### 1.2 Explicitly out of scope
- Proprietary load board or freight marketplace
- Full enterprise dispatch optimization or advanced routing
- Actual payroll tax withholding, tax filings, or regulated payroll processing
- Automated money movement, factoring, lending, or insurance products
- Full electronic IFTA filing submission
- AI agents that independently accept loads or execute financially consequential actions without explicit user authorization
- Cross-customer benchmarks that expose identifiable carrier data
- Accurate destination reload predictions at launch (explicitly deferred until sufficient data exists)

---

## 2. Definitions

| Term | Definition |
|------|------------|
| Load Contribution | Expected Load Revenue − Included Trip Costs. Never labeled “net profit” unless fixed operating costs are explicitly included and labeled. |
| Included Trip Costs | Fuel + Driver compensation + Tolls + Trip-specific expenses + other explicitly configured variable costs. |
| Contribution per Total Mile | Load Contribution ÷ (Loaded Miles + Deadhead Miles) |
| Estimated | Calculation based on inputs and assumptions before trip execution. |
| Provisional | Calculation that includes some actuals but is not fully reconciled. |
| Reconciled Actual | Calculation after delivery, settlement finalization, and payment recording. |
| Attention Item | Prioritized finding that states what happened, why it matters financially, the evidence, and the next practical action. |
| Tenant | A single carrier organization. All data is isolated by tenant. |
| Leakage | Avoidable loss or missed revenue opportunity that can be acted upon. |

---

## 3. Core Principles (Binding Implementation Constraints)

1. A new user shall obtain a useful Load Check result before any account creation, fleet setup, or integration.
2. Email, PDF, image, spreadsheet, and manual entry are first-class inputs. Integrations are not prerequisites.
3. Data shall be captured as a by-product of useful work.
4. Every material recommendation shall display: the decision, estimated financial impact, assumptions and inputs used, missing information, confidence/reliability indicator, and the next practical action.
5. Estimated, provisional, and reconciled actual results shall always be visually and programmatically distinct. Estimates shall never be silently substituted for actuals.
6. All financial calculations shall be deterministic for a given input set and calculation version.
7. Source documents shall be preserved. Material changes to rates, costs, assignments, or settlements shall be audited with user and timestamp.
8. When external services are unavailable, previously saved records and locally available calculations shall remain accessible.
9. The system shall not invent precise figures when critical inputs are missing.
10. Full operational parity with CarriersPay is required; economic intelligence is the differentiator.
11. The product shall make the owner smarter, not more dependent on reports.

---

## 4. User Roles and Permissions

| Role | Permissions |
|------|-------------|
| Owner / Administrator | Full access including settings, cost assumptions, user management, settlement finalization, data export, billing, compliance, reports, leakage resolution |
| Operations / Dispatcher | Create/evaluate loads, dispatch, track loads, attach documents, record expenses, view profitability |
| Finance / Back Office | Expenses, settlements (review/finalize), invoices, payments, cash outlook, 1099, reports |
| Driver | View assigned loads, check-in/check-out, upload documents, download statements, messaging (via invitation) |

Permissions enforced on both UI and server-side operations.

---

## 5. Data Model (Canonical Entities)

All entities are tenant-scoped. Material financial fields require audit history (who, when, old value, new value, optional reason).

### 5.1 Carrier
- id, name, address, timezone, currency (USD)
- fuel_price_assumption, mpg_assumption
- fixed_cost configuration, variable_cost_categories
- minimum_contribution or minimum_contribution_per_mile thresholds
- smtp_config, logo_url, branding
- statement_templates

### 5.2 Truck / Trailer
- id, unit_number, year, make, model, type, status
- maintenance_records

### 5.3 Driver
- id, name, status
- pay_method (percentage_revenue | cents_per_mile | flat_per_load | hourly)
- pay_rate, pay_miles_basis (loaded | empty | both), mileage_add_on_rate
- effective dates
- invitation_code

### 5.4 Broker / Customer
- id, name, contacts, payment_terms_days
- aggregate_stats (loads, revenue, average_rate)

### 5.5 Load
- id, reference_number, status
- broker_id, truck_id, driver_id
- pickup/delivery locations, dates, times, instructions
- loaded_miles, deadhead_miles
- offered_rate, accessorial_revenue
- evaluation_snapshot_id
- dispatched_at, check_in_at, check_out_at
- timestamps for status transitions

**Load Status State Machine**  
Draft → Evaluated → Accepted → Dispatched → In Transit → Delivered → Ready to Invoice → Invoiced → Paid  
Exceptions: Cancelled, Disputed.  
Cancelled or incomplete loads shall never be treated as completed profitable trips.

### 5.6 Trip
- Supports 1:1 with Load in MVP; multi-trip later
- actual miles, actual fuel, actual tolls

### 5.7 Cost Item / Expense
- amount, category, attributable_to (load | trip | truck | carrier)
- receipt, date, description, jurisdiction (IFTA)

### 5.8 Revenue Item
- amount, type, load_id

### 5.9 Document
- type, linked_to, original_file (permanent), extraction_status, extracted_fields with confidence

### 5.10 Settlement
- driver_id, period, status (draft | reviewed | finalized)
- calculated_amount, adjustments, final_amount
- linked loads, calculation_basis snapshot
- template_id, pdf_url, emailed_at, paid_status

### 5.11 Invoice
- load_id, amount, issue_date, due_date, status, payment_allocations

### 5.12 Payment
- amount, date, invoice allocation

### 5.13 Economic Snapshot
- type (estimated | provisional | reconciled)
- inputs_hash, calculation_version
- revenue, included_costs, contribution, contribution_per_mile, break_even_rate
- missing_fields, created_at

### 5.14 Recommendation / Attention Item
- type, evidence_summary, estimated_impact, suggested_action
- status (open | resolved | dismissed)
- linked_entity_ids, resolution_reason
- outcome_recorded (potential | acted | verified)

### 5.15 Audit Event
- entity, field, old_value, new_value, user_id, timestamp, reason

### 5.16 IFTARecord
- jurisdiction, miles, fuel, period

### 5.17 ComplianceDocument
- type, expiration_date, reminder_sent, file

### 5.18 MaintenanceRecord
- truck_or_trailer_id, date, description, cost, documents

### 5.19 Decision History
- load_id, recommendation, assumptions_snapshot, user_decision, timestamp

---

## 6. Calculation Engine Specification

All calculations are deterministic and versioned.

### 6.1 Load Contribution
\[
\text{Load Contribution} = \text{Expected Load Revenue} - \text{Included Trip Costs}
\]

Expected Load Revenue = offered_rate + accessorial_revenue

Included Trip Costs = fuel + driver compensation + tolls + trip-specific expenses + configured variable costs

\[
\text{Contribution per Total Mile} = \frac{\text{Load Contribution}}{\text{Loaded Miles} + \text{Deadhead Miles}}
\]

Missing critical inputs produce a result with explicit missing-fields list and reduced confidence. No invented precise figures.

### 6.2 Break-even and Target Rate
Break-even is the rate at which contribution = 0 under the configured cost basis and economic objective. The system shall state whether fixed costs are included.

### 6.3 Driver Compensation
- percentage_revenue
- cents_per_mile (with configurable miles basis)
- flat_per_load
- hourly ± optional mileage add-on
- Adjustments applied with explanations

Settlements remain draft until explicitly finalized. Finalization locks the calculation basis and updates related economic snapshots.

### 6.4 Scenario Comparison
Users shall be able to compare alternative offered rates, deadhead assumptions, or driver compensation inputs against the same base load and see the resulting contribution and recommendation change.

### 6.5 Negotiation Guidance
The system shall calculate and display the rate required to meet the user’s configured minimum contribution or contribution-per-mile objective, and the gap to the current offer.

---

## 7. Functional Requirements by Module

### 7.1 Free Load Check
**FR-LC-01** Evaluation possible without account.  
**FR-LC-02** Inputs: manual, pasted text, or rate-confirmation PDF/image.  
**FR-LC-03** Extraction presents fields for review; uncertain fields flagged.  
**FR-LC-04** Output includes recommendation (Take / Negotiate / Reject when supported), revenue, costs, contribution, contribution-per-mile, break-even, target rate, deadhead effect, missing information, and explanation.  
**FR-LC-05** Economic recommendation only; operational constraints noted separately.  
**FR-LC-06** Primary next step: convert to tracked load.  
**FR-LC-07** Free usage limit configurable. Manual path always available.  
**FR-LC-08** No credit card required for first result.

### 7.2 Load Intelligence
**FR-LI-01** Scenario comparison of rates, deadhead, or pay inputs.  
**FR-LI-02** Negotiation guidance showing required rate and gap.  
**FR-LI-03** Decision history recorded (recommendation, assumptions, user decision).  
**FR-LI-04** Outcome comparison of predicted vs actual after reconciliation.

### 7.3 Load Tracking, Dispatch & Driver Experience
**FR-LT-01 to FR-LT-05** Lifecycle, assignment, search, audit.  
**FR-DISP-01** Dispatch load to driver from extraction or manual entry.  
**FR-DISP-02** Driver receives notification and can view details, documents, navigation.  
**FR-DISP-03** Check-in / check-out and BOL/POD upload.  
**FR-DISP-04** In-app messaging.  
**FR-DISP-05** Dispatched loads feed settlement drafts.

### 7.4 Driver Settlements
**FR-DS-01 to FR-DS-10** Full four methods, office statements, templates, PDF, email, drafts, adjustments, paid/unpaid tracking, review/finalization.  
Haulpad does not execute tax withholding or payroll filings.

### 7.5 Profit Intelligence
**FR-PI-01** Views at load, truck, lane, broker, and fleet levels.  
**FR-PI-02** Every report identifies included and missing cost categories.  
**FR-PI-03** Distinguishes Estimated / Provisional / Reconciled.  
**FR-PI-04** Drill-down to underlying records.  
**FR-PI-05** Small samples and incomplete data disclosed; no false precision.  
**FR-PI-06** Lane view shows historical performance for comparable origin-destination patterns with observation count and date range.  
**FR-PI-07** Broker view shows revenue, contribution, and payment timing when available.

### 7.6 Profit Leakage Engine & Attention Inbox
Initial leakage categories that shall be detected when data supports them:
- Underpriced load (contribution below configured threshold)
- Excess deadhead
- Unpaid / uninvoiced accessorial
- Missing proof of delivery
- Uninvoiced completed load
- Unreconciled expense
- Settlement discrepancy
- Delayed / overdue payment
- Underperforming lane
- Unexpected cost variance

**FR-PL-01** Each finding states issue, evidence, estimated impact, and suggested next action.  
**FR-PL-02** Ranked by materiality, urgency, confidence, and actionability.  
**FR-PL-03** Users can inspect source records, resolve, or dismiss with reason.  
**FR-PL-04** Duplicate open findings suppressed.  
**FR-PL-05** Potential impact never reported as realized savings.  
**FR-PL-06** Where possible, record whether the action led to recovered revenue, reduced cost, or other measurable outcome.  
**FR-PL-07** Attention Inbox is the primary home screen: prioritized list answering what happened, why it matters financially, evidence, and next action. No wall of KPI cards.

### 7.7 Cash Intelligence
**FR-CI-01** Outstanding invoices with due dates and overdue flags.  
**FR-CI-02** Upcoming recorded expenses and settlement obligations.  
**FR-CI-03** Outlook for 7, 14, and 30 days distinguishing confirmed from estimated amounts.  
**FR-CI-04** Explanation of which invoices, expenses, and timing assumptions drive the outlook.  
**FR-CI-05** Recorded payments update balances without duplication.

### 7.8 Document-to-Cash Workflow
**FR-DC-01** Documents linked to load, trip, invoice, or settlement.  
**FR-DC-02** Extraction requires review before affecting authoritative records.  
**FR-DC-03** Assemble downloadable invoice packet from verified documents and data.  
**FR-DC-04** Configurable missing-document checklist.  
**FR-DC-05** Invoice status tracking and payment reconciliation (including partial).  
**FR-DC-06** Actual collected revenue updates load economic history.

### 7.9 Compliance, IFTA, Network, Maintenance, Reports
**FR-COMP-01** Compliance documents with expiration reminders.  
**FR-COMP-02** Driver applications and DQ file support.  
**FR-IFTA-01** Miles and fuel by jurisdiction with quarterly summary export.  
**FR-NET-01** Auto-build broker network with performance aggregates.  
**FR-MAINT-01** Maintenance logging and reports.  
**FR-REP-01** P&L, expense, customer, and driver reports in PDF/CSV.  
**FR-QB-01** QuickBooks Online manual import/export.  
**FR-MILE-01** Automatic mileage estimation from locations.

### 7.10 Settings & Imports
Full configuration of assumptions, thresholds, roles, SMTP, templates, branding, and data export.  
CSV/XLSX import with preview, mapping, validation, rejected-row report, and duplicate detection.

---

## 8. Navigation
1. Today (Attention Inbox)  
2. Load Check  
3. Loads / Dispatch  
4. Fleet (Trucks, Drivers, Maintenance)  
5. Money (Settlements, Expenses, Invoices, Payments, Cash)  
6. Intelligence (Profitability, Leakage, Lane/Broker views)  
7. Compliance & Documents  
8. Network  
9. Settings  

---

## 9. Security, Audit, and Trust
- Strict tenant isolation tested.  
- Documents encrypted in transit and at rest; access-controlled.  
- Material financial changes audited.  
- Backups and tested restoration before production.  
- Failures observable and shall not silently produce misleading results.  
- Data export available to Owner.

---

## 10. Non-Functional Requirements
- Deterministic calculations.  
- Extraction failure does not block manual entry.  
- Core Load Check usable without account.  
- Progressive disclosure.  
- Auditability.  
- Graceful degradation.

---

## 11. Acceptance Criteria
The product is ready only when all of the following are true:

- Free Load Check works without account and converts to tracked load without re-entry.  
- All four pay methods produce reproducible settlements; PDFs and email work.  
- Dispatch and full driver mobile workflow function.  
- Leakage findings appear in Attention Inbox with evidence and next action; potential is not reported as realized.  
- Predicted-vs-actual comparison available after reconciliation.  
- Scenario comparison and negotiation guidance available.  
- Cash outlook (7/14/30) distinguishes confirmed vs estimated.  
- Invoice packet and missing-document checks work.  
- Lane and broker profitability views available with sample disclosure.  
- IFTA export, 1099 export, compliance reminders, My Network, maintenance logging, and reports functional.  
- Cross-tenant access prevented; backups tested.

---

## 12. Implementation Notes
- Calculation engine is a pure, versioned function.  
- Persist Economic Snapshots for every evaluation and material recalculation.  
- Attention Inbox is a ranked query over open Recommendation records.  
- Decision History and outcome comparison feed future estimate calibration.  
- Start with Free Load Check + conversion; build everything else on Load and Economic Snapshot records.  
- Monetary values stored as decimal or integer cents.

---

## 13. Competitive Parity Checklist (CarriersPay)
All items previously listed in v1.2 remain required (four pay methods, full driver/office mobile experience, IFTA, 1099, compliance, My Network, reports, maintenance, QuickBooks manual sync, templates, etc.).

---

## 14. North-Star Metric and Governance
**North-star:** Verified Carrier Economic Value — financial value attributable to Haulpad recommendations that can be supported by recorded evidence (recovered revenue, reduced avoidable cost, verified contribution improvement). Hypothetical or unacted opportunities shall not be included.

Every proposed feature must have a defined user problem, measurable outcome, data requirement, failure mode, and acceptance criteria. Features that fail the core strategic tests shall be deferred.

---

## 15. Long-Term Capabilities (Not for initial launch)
- “What changed?” explanation of period-over-period performance differences.  
- Destination and repositioning recommendations (confidence-scored, based on carrier history + reliable external data).  
- Multi-load comparison and next-best-load recommendations.  
- Next-best-action recommendations with assisted execution only after recommendation quality is demonstrated.  
- Full analysis of conversion of work into cash.

These are future capabilities, not reasons to delay the initial product.

This PRS defines the complete product surface: full original Haulpad economic intelligence plus competitive parity with CarriersPay. Any reduction requires an explicit product decision.
