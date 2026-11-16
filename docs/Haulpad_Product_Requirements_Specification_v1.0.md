# Haulpad Product Requirements Specification (PRS)

**Version:** 1.4  
**Status:** Implementation baseline — Full original capabilities + Competitive Parity + Low-Friction Adoption Constraints  
**Derived from:** HaulCentral Product Constitution & Functional Product Document v1.0 + CarriersPay feature review + small-carrier workflow analysis  
**Market:** United States  
**Primary customer:** Independent and small for-hire trucking carriers operating 3–10 power units that currently rely on Excel, notes, email, and WhatsApp  

**Governing rules:**
1. Every feature must improve a material carrier decision or operating outcome, support reliable data capture, or strengthen the economic intelligence layer.
2. Haulpad shall include every material capability offered by CarriersPay so that a carrier comparing the two products has no functional reason to choose CarriersPay.
3. Haulpad’s economic intelligence layer (contribution analysis, leakage detection, prediction-to-actual learning, Attention Inbox) is the primary differentiator on top of operational parity.
4. **The product shall never feel like additional workload.** A carrier who currently uses Excel, notes, and WhatsApp must be able to complete their core daily loop with less or equal effort than their current method.

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
- Lean Carrier mode (lightweight workspace)
- Connected Carrier mode (same data model enriched by imports and later integrations)
- Ability to progress from Lean to Connected without data migration

**Settlements, Dispatch & Driver Experience (CarriersPay parity)**
- Four driver pay methods: percentage of revenue, cents-per-mile (loaded/empty/both), hourly with optional mileage add-on, flat rate per load
- Office employee statements
- Document-assisted settlement generation
- At least 11 branded statement templates
- PDF generation and email from carrier SMTP
- Drafts, adjustments, paid vs unpaid tracking
- Load dispatch to driver
- Driver mobile experience (iOS & Android): loads, check-in/check-out, navigation, document upload, statements, messaging, notifications
- Office mobile experience

**Documents, Compliance, Financials & Reporting (CarriersPay parity)**
- Permanent document library
- AI/document extraction with mandatory review before commit
- Safety & compliance center with expiration reminders
- IFTA miles and fuel tracking by jurisdiction with quarterly summary export
- 1099 contractor totals tracking and year-end export
- My Network (auto-built broker/shipper directory with performance aggregates)
- Financial dashboard and company reports (P&L, expenses, customer, driver) in PDF/CSV
- Invoicing with branding and email
- Truck/trailer maintenance logging
- QuickBooks Online manual import/export
- Automatic mileage estimation
- CSV/XLSX import with validation and duplicate detection

**Low-Friction Adoption Mechanisms**
- WhatsApp, SMS, and email forwarding as first-class inputs
- Zero double-entry after document capture
- Lightweight driver completion path (≤3 taps or WhatsApp reply)
- Offline-capable core actions
- Progressive disclosure of advanced features

### 1.2 Explicitly out of scope
- Proprietary load board or freight marketplace
- Full enterprise dispatch optimization or advanced routing
- Actual payroll tax withholding, tax filings, or regulated payroll processing
- Automated money movement, factoring, lending, or insurance products
- Full electronic IFTA filing submission
- AI agents that independently accept loads or execute financially consequential actions without explicit user authorization
- Cross-customer benchmarks that expose identifiable carrier data
- Accurate destination reload predictions at launch

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
| Daily Loop | Evaluate → accept → deliver → capture POD → settle. |

---

## 3. Core Principles (Binding Implementation Constraints)

1. A new user shall obtain a useful Load Check result before any account creation, fleet setup, or integration.
2. Email, PDF, image, spreadsheet, WhatsApp, and manual entry are first-class inputs.
3. Data shall be captured as a by-product of useful work.
4. Every material recommendation shall display: the decision, estimated financial impact, assumptions and inputs used, missing information, confidence/reliability indicator, and the next practical action.
5. Estimated, provisional, and reconciled actual results shall always be visually and programmatically distinct. Estimates shall never be silently substituted for actuals.
6. All financial calculations shall be deterministic for a given input set and calculation version.
7. Source documents shall be preserved. Material changes shall be audited.
8. When external services are unavailable, previously saved records and locally available calculations shall remain accessible.
9. The system shall not invent precise figures when critical inputs are missing.
10. Full operational parity with CarriersPay is required; economic intelligence is the differentiator.
11. The product shall make the owner smarter, not more dependent on reports.
12. **The product shall reduce or equal the carrier’s current administrative effort. It shall never increase it.**

---

## 3A. Adoption Constraints for Excel / WhatsApp Carriers (Binding)

These constraints override feature completeness when they conflict.

**AC-01 Zero Setup for First Value**  
A carrier shall obtain a useful Load Check result with only the information present on a rate confirmation (photo, PDF, or pasted text). No account, truck list, driver list, or cost assumptions are required.

**AC-02 Document Forwarding as Primary Input**  
The system shall accept a rate confirmation, POD, or receipt via photo upload, email forward, WhatsApp forward, or SMS/MMS. The forwarded item shall create or update a load or expense with extraction review. Manual re-entry of the same data is prohibited.

**AC-03 Zero Double Entry After Capture**  
Once a rate confirmation or document is accepted, every subsequent step (dispatch, settlement draft, invoice, profitability update) shall reuse that data automatically.

**AC-04 Lightweight Driver Completion Path**  
A driver shall complete the critical path (view details → mark arrived/delivered → send POD photo) with at most three taps or by replying to a WhatsApp/SMS message with a photo. Full app login shall not be required for this path.

**AC-05 Attention Inbox Replaces Manual Checking**  
The Attention Inbox shall surface items carriers currently track in WhatsApp, notes, or spreadsheets (POD not received, driver not confirmed, load ready to invoice, settlement ready, broker payment overdue). If the Inbox does not reduce external checking, it has failed.

**AC-06 Progressive Disclosure**  
Compliance, IFTA, maintenance, advanced reports, and detailed cost configuration shall never block the Daily Loop. These features appear only after at least one successful load cycle or explicit request.

**AC-07 Optional Historical Import**  
CSV/XLSX import is a convenience. Full ongoing value must exist even if historical data is never imported.

**AC-08 Offline Tolerance**  
Core driver actions (view load, mark delivered, queue POD) and owner actions (view Inbox, review settlement) shall function offline and sync later.

**AC-09 Minimum Daily Loop**  
The product is usable when a carrier can complete evaluate → accept → POD → settlement with fewer manual steps than their current Excel + WhatsApp method. Any feature that increases steps in this loop without removing a larger existing step is out of scope for the initial experience.

---

## 4. User Roles and Permissions

| Role | Permissions |
|------|-------------|
| Owner / Administrator | Full access including settings, cost assumptions, user management, settlement finalization, data export, billing, compliance, reports, leakage resolution |
| Operations / Dispatcher | Create/evaluate loads, dispatch, track loads, attach documents, record expenses, view profitability |
| Finance / Back Office | Expenses, settlements (review/finalize), invoices, payments, cash outlook, 1099, reports |
| Driver | View assigned loads, check-in/check-out, upload documents, download statements, messaging (via invitation). Lightweight no-login path available for POD. |

Permissions enforced on both UI and server-side operations.

---

## 5. Data Model (Canonical Entities)

All entities are tenant-scoped. Material financial fields require audit history.

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
- lightweight_access_token

### 5.4 Broker / Customer
- id, name, contacts, payment_terms_days
- aggregate_stats

### 5.5 Load
- id, reference_number, status
- broker_id, truck_id, driver_id
- pickup/delivery locations, dates, times, instructions
- loaded_miles, deadhead_miles
- offered_rate, accessorial_revenue
- evaluation_snapshot_id
- source_channel (whatsapp | email | photo | manual | import)
- dispatched_at, check_in_at, check_out_at
- timestamps

**Load Status State Machine**  
Draft → Evaluated → Accepted → Dispatched → In Transit → Delivered → Ready to Invoice → Invoiced → Paid  
Exceptions: Cancelled, Disputed.

### 5.6–5.19
(Expense, Revenue, Document, Settlement, Invoice, Payment, Economic Snapshot, Recommendation, Audit Event, IFTARecord, ComplianceDocument, MaintenanceRecord, Decision History remain as defined in v1.3.)

Document adds: ingestion_channel, original_message_id (optional).

---

## 6. Calculation Engine Specification

All calculations are deterministic and versioned.

### 6.1 Load Contribution
\[
\text{Load Contribution} = \text{Expected Load Revenue} - \text{Included Trip Costs}
\]

\[
\text{Contribution per Total Mile} = \frac{\text{Load Contribution}}{\text{Loaded Miles} + \text{Deadhead Miles}}
\]

Missing critical inputs produce explicit missing-fields list and reduced confidence. No invented precise figures.

### 6.2–6.5
Break-even and target rate, full four driver compensation methods, scenario comparison, and negotiation guidance remain as defined in v1.3.

---

## 7. Functional Requirements by Module

### 7.1 Free Load Check
**FR-LC-01** Evaluation possible without account.  
**FR-LC-02** Inputs: manual, pasted text, rate-confirmation PDF/image, WhatsApp forward, or email forward.  
**FR-LC-03** Extraction presents fields for review; uncertain fields flagged.  
**FR-LC-04** Output includes recommendation (Take / Negotiate / Reject when supported), revenue, costs, contribution, contribution-per-mile, break-even, target rate, deadhead effect, missing information, and explanation.  
**FR-LC-05** Economic recommendation only.  
**FR-LC-06** Primary next step: convert to tracked load.  
**FR-LC-07** Free usage limit configurable. Manual path always available.  
**FR-LC-08** No credit card required for first result.  
**FR-LC-09** WhatsApp or email forward of a rate confirmation is sufficient.

### 7.2 Load Intelligence
**FR-LI-01 to FR-LI-04** Scenario comparison, negotiation guidance, decision history, and outcome comparison remain required.

### 7.3 Load Tracking, Dispatch & Driver Experience
**FR-LT-01 to FR-LT-05** and **FR-DISP-01 to FR-DISP-05** remain.  
**FR-ADOPT-02** Driver POD capture via three-tap flow or WhatsApp photo reply.

### 7.4 Driver Settlements
**FR-DS-01 to FR-DS-10** Full parity requirements remain.

### 7.5 Profit Intelligence
**FR-PI-01 to FR-PI-07** Load, truck, lane, broker, and fleet views with disclosure requirements remain.

### 7.6 Profit Leakage Engine & Attention Inbox
All prior leakage categories and **FR-PL-01 to FR-PL-07** remain.  
**FR-ADOPT-03** Inbox items shall include signals currently tracked outside the system (POD missing, driver unconfirmed, ready to invoice, settlement ready, payment overdue).

### 7.7–7.10
Cash Intelligence, Document-to-Cash, Compliance/IFTA/Network/Maintenance/Reports, and Settings requirements from v1.3 remain.  
**FR-ADOPT-01** Email, WhatsApp, and SMS forwarding shall create loads or documents.  
**FR-ADOPT-04** Advanced modules shall be hidden or deferred until after the first completed load cycle.

---

## 8. Navigation
1. Today (Attention Inbox)  
2. Load Check  
3. Loads / Dispatch  
4. Money  
5. Intelligence  
6. Fleet  
7. Compliance & Documents  
8. Network  
9. Settings  

Compliance, Network, and advanced Fleet features shall not appear in primary navigation until the carrier has completed at least one Daily Loop.

---

## 9. Security, Audit, and Trust
- Strict tenant isolation.  
- Documents encrypted; access-controlled.  
- Material financial changes audited.  
- Backups and tested restoration required.  
- Failures shall not silently produce misleading results.  
- Data export available to Owner.

---

## 10. Non-Functional Requirements
- Deterministic calculations.  
- Extraction failure does not block manual entry.  
- Core Load Check usable without account.  
- Progressive disclosure.  
- Offline tolerance for core actions.  
- Auditability.  
- Graceful degradation.

---

## 11. Acceptance Criteria
The product is ready only when all of the following are true:

- Free Load Check works without account (including from WhatsApp/email/photo) and converts to tracked load without re-entry.  
- All four pay methods produce reproducible settlements; PDFs and email work.  
- Dispatch and full driver mobile workflow function.  
- Lightweight driver POD path (≤3 taps or WhatsApp) works.  
- Leakage findings appear in Attention Inbox with evidence and next action.  
- Predicted-vs-actual, scenario comparison, and negotiation guidance available.  
- Cash outlook (7/14/30) distinguishes confirmed vs estimated.  
- Invoice packet and missing-document checks work.  
- Lane and broker views available with sample disclosure.  
- IFTA, 1099, compliance, My Network, maintenance, and reports functional.  
- Daily Loop requires fewer or equal steps versus Excel + WhatsApp.  
- Advanced features can be ignored without blocking core use.  
- Cross-tenant access prevented; backups tested.

---

## 12. Implementation Notes
- Calculation engine is a pure, versioned function.  
- Persist Economic Snapshots for every evaluation and material recalculation.  
- Attention Inbox is a ranked query over open Recommendation records.  
- Decision History feeds future estimate calibration.  
- Start with Free Load Check + WhatsApp/email ingestion + conversion.  
- Monetary values stored as decimal or integer cents.  
- Lightweight driver path uses short-lived tokens, not full accounts.

---

## 13. Competitive Parity Checklist (CarriersPay)
All items from v1.2/v1.3 remain required.

---

## 14. North-Star Metric and Governance
**North-star:** Verified Carrier Economic Value — financial value attributable to Haulpad recommendations supported by recorded evidence.

Every proposed feature must have a defined user problem, measurable outcome, data requirement, failure mode, and acceptance criteria. Features that increase administrative effort without removing a larger existing step shall be deferred.

---

## 15. Long-Term Capabilities (Not for initial launch)
- “What changed?” explanations.  
- Destination and repositioning recommendations.  
- Multi-load comparison and next-best-load recommendations.  
- Next-best-action with assisted execution only after quality is demonstrated.  
- Full conversion-of-work-into-cash analysis.

---

## 16. Adoption Success Criteria

The product meets the low-workload requirement only when:
- A carrier can complete their first Load Check from a WhatsApp photo or email in under 60 seconds with no account.
- The Daily Loop requires fewer manual steps than the carrier’s current Excel + WhatsApp process.
- Drivers can send a POD without installing or logging into a full app.
- The Attention Inbox surfaces at least three types of items the carrier previously tracked outside the system.
- Advanced features can be ignored indefinitely without blocking core use.

Any implementation that satisfies feature completeness but fails these criteria is incomplete.

This PRS defines the complete product surface: full original Haulpad economic intelligence, competitive parity with CarriersPay, and binding low-friction adoption constraints for Excel/WhatsApp carriers. Any reduction in the adoption constraints requires an explicit product decision.
