# Haulpad Product Requirements Specification (PRS)

**Version:** 1.1  
**Status:** Implementation baseline (MVP) + Migration Parity  
**Derived from:** HaulCentral Product Constitution & Functional Product Document v1.0  
**Market:** United States  
**Primary customer:** Independent and small for-hire trucking carriers operating 3–10 power units  
**Governing rule:** Every feature must improve a material carrier decision or operating outcome, support reliable data capture, or strengthen the economic intelligence layer. Features that fail this test are out of scope for MVP.

This PRS is written for direct implementation. It uses “shall” for mandatory behavior. Where the source document is silent, the PRS defines the minimal unambiguous rule. No additional features, screens, or intelligence are to be invented.

**Migration goal:** A carrier currently using CarriersPay shall be able to move to Haulpad without losing any core operational capability they rely on for settlements, dispatch, documents, basic compliance tracking, and financial visibility.

---

## 1. Scope

### 1.1 In scope for MVP (P0) — including migration-critical items
- Free Load Check (no account required for first evaluation)
- Conversion of an evaluation into a tracked load
- Basic carrier, truck, and driver records
- Configurable cost assumptions (fuel, variable, basic fixed)
- Driver pay configuration for percentage-of-revenue, cents-per-mile, flat-per-load, **and hourly (with optional mileage add-on)**
- Load lifecycle tracking
- Expense capture and document attachment
- CSV/XLSX import with preview and validation
- Basic driver settlements (calculate, adjust, review, finalize) with PDF export and email
- Invoice tracking and payment recording
- Profitability views at load, truck, and simple fleet level
- Predicted-vs-actual comparison
- Attention Inbox with initial leakage findings
- Simple cash outlook (7/14/30-day)
- Tenant isolation, audit trail on material financial fields, data export
- Manual entry + rate-confirmation upload with extraction review
- **Migration parity additions (must-have for seamless switch):**
  - Four driver pay methods (percentage, per-mile with loaded/empty choice, hourly ± mileage, flat per load)
  - Driver statements as branded PDFs with multiple templates
  - Basic dispatch of loads to drivers (notification + document share)
  - Driver-facing view of assigned loads, statements, and document upload (web or lightweight mobile)
  - IFTA mileage and fuel tracking by state (data capture and reporting; not full filing automation)
  - 1099 total tracking and year-end summary export
  - Broker/customer network with performance history
  - Document library with permanent retention of rate cons, BOL, POD
  - Basic safety/compliance document storage with expiration reminders
  - Office employee simple statements (optional, lightweight)

### 1.2 Explicitly out of scope for MVP (still)
- Proprietary load board or marketplace
- Full enterprise TMS dispatch optimization or advanced routing
- Payroll tax processing, tax filings, or regulated payroll (including full 1099/W-2 filing)
- Automated banking, payments execution, factoring, lending, insurance, or fuel-card products
- Full IFTA filing automation and submission
- Advanced maintenance management (beyond basic repair logging)
- Email ingestion (later)
- Deep third-party API integrations (ELD, fuel card, full accounting write-back) beyond basic CSV and optional QuickBooks manual sync
- Reload / destination predictions
- AI agents that accept loads or execute financially consequential actions
- Multi-carrier agency mode
- Cross-customer benchmarks

---

## 13. CarriersPay Migration Parity Requirements

The following capabilities exist in CarriersPay and are required so that a carrier can migrate without functional regression. Items are classified by priority.

### 13.1 Must-have for MVP (parity blockers)
| Capability | Requirement | Notes |
|------------|-------------|-------|
| Four pay methods | Support percentage of revenue, cents-per-mile (configurable loaded/empty/both), hourly (optional per-mile add-on), flat per load | Stored on driver profile; applied automatically |
| Fast statement generation | Upload rate confirmation → extract → calculate settlement in one flow; produce PDF | Aligns with Free Load Check + conversion |
| Statement templates & branding | At least 5–11 selectable templates; company logo/name; PDF download and email from company address (SMTP) | |
| Driver dispatch | Assign load to driver; push notification or in-app visibility; attach documents | Can start with web + email/SMS notifications if native apps deferred |
| Driver access | Driver can view assigned loads, download statements, upload BOL/POD photos, basic messaging | Invitation code or account link; no separate paid driver subscription |
| IFTA data | Capture miles and fuel by state/jurisdiction; generate quarterly summary report | Data collection and export; not electronic filing |
| 1099 tracking | Accumulate contractor payments; year-end export of totals | Does not file forms |
| Document permanence | Rate cons, BOL, POD stored indefinitely; searchable library | |
| Broker network | Auto-populate brokers from rate cons; track volume, revenue, average rate | |
| Basic compliance storage | Store CDL, medical, MVR, insurance, permits; expiration reminders (30-day) | |
| Load-level profit | Show contribution per load after variable costs; allow expense attachment | Core to Haulpad economics |

### 13.2 Near-term (P1) — high value for migration
- Native iOS/Android Driver app and Office app
- QuickBooks Online manual import/export of expenses and settlements
- Truck/trailer maintenance logging and basic reports
- Driver application + DQ file builder
- Multiple statement design options (vertical/horizontal)
- Paid vs unpaid tracking dashboard
- AI credit / usage transparency if document extraction is metered

### 13.3 Later / optional (P2+)
- Full IFTA filing automation
- Advanced maintenance scheduling
- Deep ELD/fuel-card integrations
- AI assistant that answers questions over carrier data
- Office employee full payroll

### 13.4 Updated Functional Requirements for Parity

**FR-MIG-01** Driver pay methods shall include percentage, per-mile (with loaded/empty/both option), hourly with optional mileage add-on, and flat per load. Method and rates are stored on the driver record and applied automatically to settlements.

**FR-MIG-02** Settlements shall generate a professional PDF using a selectable template and support email delivery from the carrier’s own email address.

**FR-MIG-03** A load can be dispatched to a driver. The driver shall receive notification and be able to view load details, documents, and later the related settlement.

**FR-MIG-04** Drivers shall have a lightweight interface (web or mobile) to view assigned loads, upload documents, and download statements via invitation.

**FR-MIG-05** The system shall track loaded and deadhead miles plus fuel purchases by state for IFTA summary reporting and export.

**FR-MIG-06** Year-end 1099 contractor totals shall be calculable and exportable.

**FR-MIG-07** Broker records shall automatically capture contacts and aggregate performance (loads, revenue, average rate) from processed rate confirmations.

**FR-MIG-08** Compliance documents (CDL, medical card, insurance, etc.) can be stored with expiration dates and generate reminders.

**FR-MIG-09** All source documents (rate confirmation, BOL, POD, receipts) shall be retained permanently and linked to the relevant load/settlement.

These requirements extend but do not replace the original economic intelligence requirements. The Attention Inbox and contribution calculations remain primary.

---

## 5. Data Model (updates for parity)

Add / extend:
- Driver: support hourly method + optional mileage_add_on_rate; pay_miles_basis (loaded | empty | both)
- Settlement: template_id, pdf_url, emailed_at, email_to
- Load: dispatched_at, driver_notified_at, check_in_at, check_out_at (pickup/delivery)
- IFTARecord: jurisdiction, miles, fuel_gallons, period
- ComplianceDocument: type, expiration_date, reminder_sent
- Broker: auto-extracted contacts, aggregate stats

---

(The remainder of the original PRS sections 2–12 remain in force, with the additions above taking precedence for migration-critical paths.)

This PRS now supports both Haulpad’s original economic-intelligence thesis and a practical migration path from CarriersPay.
