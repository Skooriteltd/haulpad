# Haulpad Product Requirements Specification (PRS)

**Version:** 1.2  
**Status:** Implementation baseline — Competitive Parity with CarriersPay  
**Derived from:** HaulCentral Product Constitution & Functional Product Document v1.0 + CarriersPay feature review  
**Market:** United States  
**Primary customer:** Independent and small for-hire trucking carriers operating 3–10 power units  
**Governing rule:** Every feature must improve a material carrier decision or operating outcome, support reliable data capture, or strengthen the economic intelligence layer.  

**Competitive parity goal:** Haulpad shall include every material capability offered by CarriersPay so that a carrier comparing the two products has no functional reason to stay with (or choose) CarriersPay. Haulpad’s economic intelligence layer is the differentiator on top of full operational parity.

This PRS is written for direct implementation. It uses “shall” for mandatory behavior.

---

## 1. Scope

### 1.1 In scope (required for competitive parity and launch)

**Acquisition & Economics (Haulpad differentiation)**
- Free Load Check (no account required for first evaluation)
- Conversion of an evaluation into a tracked load
- Contribution, contribution-per-mile, break-even, target-rate calculations
- Attention Inbox with leakage findings
- Predicted-vs-actual comparison
- Simple cash outlook

**Settlements & Pay (full CarriersPay parity)**
- Four driver pay methods: percentage of revenue, cents-per-mile (loaded / empty / both selectable), hourly with optional mileage add-on, flat rate per load
- Office employee statements / pay stubs
- AI / document-assisted settlement generation from rate confirmations, fuel receipts, and mileage records
- Multiple (at least 11) branded statement templates
- PDF generation and email from carrier’s own email address (SMTP)
- Drafts, duplicate entry, adjustments (fuel, escrow, advances, deductions, reimbursements, bonuses)
- Paid vs unpaid tracking

**Dispatch & Driver Experience**
- Load dispatch from rate confirmation or manual entry directly to driver
- Driver mobile experience (iOS & Android): view assigned loads, pickup/delivery details, check-in / check-out timestamps, open navigation, upload BOL/POD/photos, view and download statements, receive push notifications, in-app messaging with carrier
- Office mobile experience for owners and staff
- Automatic attachment of dispatched loads to settlements

**Documents & Compliance**
- Permanent document library (rate confirmations, BOL, POD, receipts, invoices, settlements)
- AI extraction with review before commit
- Safety & compliance center: CDL, medical cards, MVRs, insurance, permits, DQ files, driver applications
- Expiration reminders (30-day before / after)
- Assign carrier documents to drivers for mobile access

**Financials, Reporting & Integrations**
- Financial dashboard (revenue, expenses, profit, cost per mile)
- Company reports: P&L, expenses, customer, driver (PDF and CSV)
- Invoicing (create, brand, email, mark paid)
- IFTA mileage and fuel tracking by state/jurisdiction with quarterly summary export
- 1099 contractor totals tracking and year-end export
- My Network: auto-built broker/shipper directory with contacts and performance aggregates
- QuickBooks Online manual import/export of expenses and settlements
- Truck / trailer maintenance logging and basic reports
- Automatic mileage estimation from pickup/delivery locations
- Flexible expense tagging (load-level or company-level)

**Platform**
- Tenant isolation, audit trail, data export
- Role-based access (Owner, Operations/Dispatcher, Finance, Driver)
- CSV/XLSX import with validation
- Configurable cost assumptions and economic thresholds

### 1.2 Explicitly out of scope
- Proprietary load board or freight marketplace
- Full enterprise dispatch optimization / advanced routing engines
- Actual payroll tax withholding, tax filings, or regulated payroll processing
- Automated money movement, factoring, lending, or insurance products
- Full electronic IFTA filing submission to jurisdictions
- AI agents that independently accept loads or execute financially consequential actions without explicit authorization
- Cross-customer benchmarks that expose identifiable carrier data

---

## 2. Definitions

| Term | Definition |
|------|------------|
| Load Contribution | Expected Load Revenue − Included Trip Costs. Never labeled “net profit” unless fixed operating costs are explicitly included and labeled. |
| Included Trip Costs | Fuel + Driver compensation + Tolls + Trip-specific expenses + other explicitly configured variable costs. |
| Contribution per Total Mile | Load Contribution ÷ (Loaded Miles + Deadhead Miles) |
| Estimated / Provisional / Reconciled Actual | Distinct calculation states. Estimates shall never be silently substituted for actuals. |
| Attention Item | Prioritized finding that states what happened, why it matters financially, the evidence, and the next practical action. |
| Tenant | A single carrier organization. All data is isolated by tenant. |

---

## 3. Core Principles (Implementation Constraints)

1. A new user shall obtain a useful Load Check result before any account creation, fleet setup, or integration.
2. Email, PDF, image, spreadsheet, and manual entry are first-class inputs.
3. Data shall be captured as a by-product of useful work.
4. Every material recommendation shall display decision, estimated impact, assumptions, missing information, confidence, and next action.
5. Estimated, provisional, and reconciled actual results shall remain visually and programmatically distinct.
6. All financial calculations shall be deterministic for a given input set and calculation version.
7. Source documents shall be preserved. Material financial changes shall be audited.
8. The system shall not invent precise figures when critical inputs are missing.
9. Full operational parity with CarriersPay is required; economic intelligence is the differentiator.

---

## 4. User Roles and Permissions

| Role | Permissions |
|------|-------------|
| Owner / Administrator | Full access including settings, cost assumptions, user management, settlement finalization, data export, billing, compliance, reports |
| Operations / Dispatcher | Create/evaluate loads, dispatch, track loads, attach documents, record expenses, view profitability |
| Finance / Back Office | Expenses, settlements (review/finalize), invoices, payments, cash outlook, 1099, reports |
| Driver | View assigned loads, check-in/check-out, upload documents, download statements, messaging (via invitation; no separate paid subscription) |
| Office Staff (limited) | Configurable subset of operations or finance permissions |

Permissions shall be enforced on both UI and server-side operations.

---

## 5. Data Model (Canonical Entities)

All entities are tenant-scoped. Material financial fields require audit history.

### 5.1 Carrier
- id, name, address, timezone, currency (USD)
- fuel_price_assumption, mpg_assumption
- fixed_cost configuration, variable_cost_categories
- minimum_contribution thresholds
- smtp_config, logo_url, branding settings
- statement_templates (list)

### 5.2 Truck / Trailer
- id, unit_number, year, make, model, type (truck/trailer), status
- maintenance_records, registration, inspection docs

### 5.3 Driver
- id, name, status
- pay_method (percentage_revenue | cents_per_mile | flat_per_load | hourly)
- pay_rate, pay_miles_basis (loaded | empty | both), mileage_add_on_rate
- effective dates
- invitation_code
- compliance_documents

### 5.4 Broker / Customer (My Network)
- id, name, contacts (name, email, phone)
- payment_terms_days
- aggregate_stats (loads, revenue, average_rate, last_activity)

### 5.5 Load
- id, reference_number, status
- broker_id, truck_id, driver_id
- pickup/delivery locations, dates, times, instructions
- loaded_miles, deadhead_miles
- offered_rate, accessorials
- evaluation_snapshot_id
- dispatched_at, driver_notified_at
- check_in_at, check_out_at (pickup and delivery)
- timestamps

**Load Status State Machine**  
Draft → Evaluated → Accepted → Dispatched → In Transit → Delivered → Ready to Invoice → Invoiced → Paid  
Exceptions: Cancelled, Disputed.

### 5.6–5.15 (Expense, Revenue, Document, Settlement, Invoice, Payment, Economic Snapshot, Recommendation, Audit Event)
As previously defined, with additions:
- Settlement: template_id, pdf_url, emailed_at, email_to, paid_status
- Document: permanent storage, extraction metadata
- Expense: jurisdiction for IFTA, tag (load or company)

### 5.16 IFTARecord
- jurisdiction, miles, fuel_gallons / cost, period, driver_id or truck_id

### 5.17 ComplianceDocument
- type, expiration_date, reminder_sent, file, linked_to

### 5.18 MaintenanceRecord
- truck_or_trailer_id, date, description, cost, documents

---

## 6. Calculation Engine Specification

(Unchanged core formulas for Load Contribution and Contribution per Total Mile.)

### 6.3 Driver Compensation (full parity)
- percentage_revenue
- cents_per_mile (configurable miles basis)
- flat_per_load
- hourly ± optional mileage add-on
- Adjustments: fuel, escrow, advances, deductions, reimbursements, bonuses

All calculations deterministic and versioned. Settlements produce branded PDFs.

---

## 7. Functional Requirements by Module

### 7.1 Free Load Check
(Unchanged — remains the acquisition wedge.)

### 7.2 Load Tracking, Dispatch & Driver Experience
**FR-LT-01 to FR-LT-05** (lifecycle, search, audit)  
**FR-DISP-01** Users can dispatch a load (from rate confirmation extraction or manual) to a driver.  
**FR-DISP-02** Driver receives notification and can view load details, documents, and navigation links.  
**FR-DISP-03** Driver can check in / check out at stops and upload BOL/POD.  
**FR-DISP-04** In-app messaging between office and driver, with document sharing.  
**FR-DISP-05** Dispatched loads feed into settlement drafts automatically.

### 7.3–7.4 Settlements, Expenses, Documents
**FR-DS-01 to FR-DS-07** plus:  
**FR-DS-08** Support for office employee statements.  
**FR-DS-09** Paid vs unpaid settlement tracking.  
**FR-DS-10** At least 11 selectable branded templates.  
**FR-ED-04** Permanent searchable document library with automatic filing.

### 7.5 Profit Intelligence & Attention Inbox
(Unchanged core + load-level contribution remains primary.)

### 7.6–7.8 Cash, Document-to-Cash, Invoicing
(Unchanged + branded invoice email.)

### 7.9 Compliance, IFTA, Network, Maintenance, Reports
**FR-COMP-01** Store and manage compliance documents with expiration reminders.  
**FR-COMP-02** Support driver applications and DQ file assembly.  
**FR-IFTA-01** Track miles and fuel by jurisdiction; generate quarterly summary export.  
**FR-NET-01** Auto-build broker/shipper network from rate confirmations with performance aggregates.  
**FR-MAINT-01** Log truck/trailer maintenance, store related documents, export reports.  
**FR-REP-01** Generate P&L, expense, customer, and driver reports in PDF and CSV.  
**FR-QB-01** Support manual QuickBooks Online import/export of expenses and settlements.  
**FR-MILE-01** Automatic mileage estimation from pickup/delivery locations.

### 7.10 Settings
Full configuration including SMTP, templates, branding, roles, thresholds, and data export.

---

## 8. Navigation
1. Today (Attention Inbox)  
2. Load Check  
3. Loads / Dispatch  
4. Fleet (Trucks, Drivers, Maintenance)  
5. Money (Settlements, Expenses, Invoices, Payments)  
6. Intelligence  
7. Compliance & Documents  
8. Network  
9. Settings  

---

## 9–12. Security, Non-Functional, Acceptance Criteria, Implementation Notes
(Retained from prior versions, expanded to cover new modules.)

**Additional acceptance criteria for competitive parity:**
- All four pay methods produce correct settlements.
- Dispatch + full driver mobile workflow (view, check-in, upload, statement download, messaging) functions.
- IFTA by-state data and export available.
- 1099 totals exportable.
- Compliance documents with reminders stored.
- My Network auto-populates and shows performance.
- Multiple statement templates and company-branded email work.
- Reports (P&L, etc.) exportable.
- Maintenance logging available.
- QuickBooks manual sync path exists.

---

## 13. Competitive Parity Checklist (CarriersPay → Haulpad)

| CarriersPay Capability | Haulpad Requirement | Priority |
|------------------------|---------------------|----------|
| AI settlements from rate cons / fuel / mileage | FR-DS + extraction review | P0 |
| Four pay methods + office employee statements | FR-DS-01, FR-DS-08 | P0 |
| 11+ statement templates, branded PDF, company email | FR-DS-07, FR-DS-10 | P0 |
| Driver Dispatch + Driver App (loads, check-in, chat, statements, notifications) | FR-DISP-* | P0 |
| Office mobile app | FR-DISP / role access | P0 |
| IFTA miles & fuel by state | FR-IFTA-01 | P0 |
| 1099 / W-2 totals | FR (1099 export) | P0 |
| Financial dashboard + company reports (PDF/CSV) | FR-REP-01 | P0 |
| Invoicing | Document-to-Cash + branding | P0 |
| My Network (auto broker directory + performance) | FR-NET-01 | P0 |
| Safety & Compliance + DQ files + expiration reminders | FR-COMP-* | P0 |
| Permanent document library + BOL/POD storage | FR-ED-04 | P0 |
| Truck/Trailer Maintenance | FR-MAINT-01 | P0 |
| QuickBooks Online sync | FR-QB-01 | P0 |
| Automatic mileage estimation | FR-MILE-01 | P0 |
| Paid vs unpaid tracking | FR-DS-09 | P0 |
| Drafts & duplicate entries | FR-DS | P0 |
| AI document scanning with review | FR-LC / extraction | P0 |
| Load-level profit visibility | Core contribution engine | P0 (differentiator) |
| Attention Inbox / leakage detection | Core | P0 (differentiator) |

This PRS defines the product surface required for competitive parity with CarriersPay while retaining Haulpad’s economic-intelligence advantage. Any reduction in the parity checklist requires an explicit product decision.
