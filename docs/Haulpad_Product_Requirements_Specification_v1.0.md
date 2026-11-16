# Haulpad Product Requirements Specification (PRS)

**Version:** 1.4  
**Status:** Implementation baseline — Full original capabilities + Competitive Parity + Low-Friction Adoption Constraints  
**Derived from:** HaulCentral Product Constitution & Functional Product Document v1.0 + CarriersPay feature review + small-carrier workflow analysis  
**Market:** United States  
**Primary customer:** Independent and small for-hire trucking carriers operating 3–10 power units that currently rely on Excel, notes, email, and WhatsApp  

**Governing rules:**
1. Every feature must improve a material carrier decision or operating outcome, support reliable data capture, or strengthen the economic intelligence layer.
2. Haulpad shall include every material capability offered by CarriersPay so that a carrier comparing the two products has no functional reason to choose CarriersPay.
3. Haulpad’s economic intelligence layer is the primary differentiator on top of operational parity.
4. **The product shall never feel like additional workload.** A carrier who currently uses Excel, notes, and WhatsApp must be able to complete their core daily loop (evaluate → accept → deliver → settle) with less or equal effort than their current method.

This PRS is written for direct implementation. It uses “shall” for mandatory behavior.

---

## 1. Scope

### 1.1 In scope (required)

**Acquisition & Economic Intelligence**
- Free Load Check with no account required
- Conversion of an evaluation into a tracked load without re-entry
- Contribution, contribution-per-mile, break-even, target-rate calculations
- Scenario comparison and negotiation guidance
- Decision history and predicted-versus-actual comparison
- Profit Intelligence at load, truck, lane, broker, and fleet levels
- Profit Leakage Engine with defined categories
- Attention Inbox as primary home experience
- Cash Intelligence (7/14/30-day)
- Document-to-cash workflow

**Operating Modes**
- Lean Carrier mode and Connected Carrier mode on the same data model

**Settlements, Dispatch & Driver Experience (CarriersPay parity)**
- Four driver pay methods + office employee statements
- Document-assisted settlement generation
- At least 11 branded statement templates, PDF, and carrier SMTP email
- Drafts, adjustments, paid vs unpaid tracking
- Load dispatch to driver
- Driver mobile experience (iOS & Android) with check-in/check-out, document upload, statements, messaging
- Office mobile experience

**Documents, Compliance, Financials & Reporting (CarriersPay parity)**
- Permanent document library with extraction review
- Safety & compliance center with expiration reminders
- IFTA tracking and quarterly export
- 1099 totals and year-end export
- My Network (auto-built broker directory)
- Financial dashboard and company reports (PDF/CSV)
- Invoicing, maintenance logging, QuickBooks manual sync
- Automatic mileage estimation
- CSV/XLSX import

**Low-Friction Adoption Mechanisms (critical)**
- WhatsApp, SMS, and email forwarding as first-class input methods
- Zero double-entry after initial document capture
- Lightweight driver completion path (≤3 taps or WhatsApp reply)
- Offline-capable core driver and owner actions
- Progressive disclosure: advanced features never block the daily loop

### 1.2 Explicitly out of scope
- Proprietary load board or marketplace
- Full enterprise dispatch optimization
- Actual payroll tax processing or regulated payroll
- Automated money movement, factoring, or insurance products
- Full electronic IFTA filing
- AI agents that independently execute financially consequential actions
- Cross-customer identifiable benchmarks
- Destination reload predictions at launch

---

## 2. Definitions

| Term | Definition |
|------|------------|
| Load Contribution | Expected Load Revenue − Included Trip Costs. Never labeled “net profit” unless fixed costs are explicitly included and labeled. |
| Included Trip Costs | Fuel + Driver compensation + Tolls + Trip-specific expenses + configured variable costs. |
| Contribution per Total Mile | Load Contribution ÷ (Loaded Miles + Deadhead Miles) |
| Estimated / Provisional / Reconciled Actual | Distinct calculation states. Estimates shall never be silently substituted for actuals. |
| Attention Item | Prioritized finding stating what happened, why it matters financially, the evidence, and the next practical action. |
| Daily Loop | The minimum set of actions a carrier must complete for a load: evaluate → accept → deliver → capture POD → settle. |

---

## 3. Core Principles (Binding)

1. Useful before setup — first Load Check requires zero account, fleet, or cost configuration.
2. Existing workflow is an integration — PDF, photo, WhatsApp, email, spreadsheet, and manual entry are first-class.
3. Data captured as a by-product of useful work.
4. Every material recommendation is explainable (decision, impact, assumptions, missing info, confidence, next action).
5. Estimated, provisional, and reconciled results remain distinct.
6. Calculations are deterministic and versioned.
7. Source documents are preserved; material changes are audited.
8. No invented precise figures when critical inputs are missing.
9. Full operational parity with CarriersPay; economic intelligence is the differentiator.
10. **The product shall reduce, not increase, the carrier’s daily administrative effort.**

---

## 3A. Adoption Constraints for Excel / WhatsApp Carriers (Binding)

These constraints override feature completeness when they conflict. They exist to prevent the product from being perceived as additional workload.

**AC-01 Zero Setup for First Value**  
A carrier shall obtain a useful Load Check result with only the information present on a rate confirmation (photo, PDF, or pasted text). No account, truck list, driver list, or cost assumptions are required.

**AC-02 Document Forwarding as Primary Input**  
The system shall accept a rate confirmation, POD, or receipt via:
- Photo upload
- Email forward to a dedicated address
- WhatsApp forward (or WhatsApp Business API where available)
- SMS/MMS forward

The forwarded item shall create or update a load or expense with extraction review. Manual re-entry of the same data is prohibited.

**AC-03 Zero Double Entry After Capture**  
Once a rate confirmation or document is accepted, every subsequent step (dispatch, settlement draft, invoice, profitability update) shall reuse that data automatically. The carrier shall not re-type rate, miles, broker, or locations.

**AC-04 Lightweight Driver Completion Path**  
A driver shall be able to complete the critical path for a load (view details → mark arrived/delivered → send POD photo) with at most three taps in the mobile experience, or by replying to a WhatsApp/SMS message with a photo. Full app login shall not be required for this path.

**AC-05 Attention Inbox Replaces Manual Checking**  
The Attention Inbox shall surface items that carriers currently track in WhatsApp threads, notes, or spreadsheets, including:
- POD not received
- Driver has not confirmed arrival
- Load ready to invoice but not invoiced
- Settlement ready for review
- Broker payment overdue

If the Inbox does not reduce the need to scroll WhatsApp or open Excel for status, it has failed.

**AC-06 Progressive Disclosure**  
Compliance documents, IFTA details, maintenance records, advanced reports, and cost-assumption configuration shall never block the Daily Loop. These features shall appear only after the carrier has completed at least one successful load cycle or explicitly requests them.

**AC-07 Optional Historical Import**  
CSV/XLSX import of historical loads or expenses is a convenience. The system shall deliver full ongoing value even if the carrier never imports past data.

**AC-08 Offline Tolerance**  
Core driver actions (view assigned load, mark delivered, capture and queue POD photo) and owner actions (view Attention Inbox, review settlement draft) shall function offline and sync when connectivity returns.

**AC-09 Minimum Daily Loop**  
The product is usable when a carrier can complete this sequence with less effort than their current Excel + WhatsApp method:
1. Receive rate confirmation (WhatsApp/email/photo)
2. Get economic evaluation
3. Accept and dispatch
4. Driver marks delivered and sends POD
5. Settlement is generated and sent

Any feature that increases the number of steps in this loop without removing a larger existing step is out of scope for the initial experience.

---

## 4. User Roles and Permissions

| Role | Permissions |
|------|-------------|
| Owner / Administrator | Full access |
| Operations / Dispatcher | Loads, dispatch, documents, expenses, profitability |
| Finance / Back Office | Settlements, invoices, payments, cash, reports |
| Driver | Assigned loads, check-in/check-out, document upload, statements, messaging (invitation-based; lightweight path available) |

---

## 5. Data Model

(Unchanged from v1.3. All entities remain tenant-scoped with audit on material financial fields.)

Additional fields required by adoption constraints:
- Load: source_channel (whatsapp | email | photo | manual | import)
- Document: ingestion_channel, original_message_id (optional)
- Driver: lightweight_access_token (for no-login POD path)

---

## 6. Calculation Engine

(Unchanged from v1.3.)

---

## 7. Functional Requirements

### 7.1 Free Load Check
(FR-LC-01 to FR-LC-08 unchanged.)  
**FR-LC-09** Rate confirmation received via WhatsApp, email forward, or photo shall be sufficient input.

### 7.2–7.10
(All prior functional requirements remain.)

**Additional requirements from adoption constraints:**
**FR-ADOPT-01** Email, WhatsApp, and SMS forwarding addresses/numbers shall create loads or documents.  
**FR-ADOPT-02** Driver POD capture via three-tap mobile flow or WhatsApp photo reply.  
**FR-ADOPT-03** Attention Inbox items shall be generated from the same signals carriers currently monitor manually.  
**FR-ADOPT-04** Advanced modules (compliance, IFTA, maintenance, detailed reports) shall be hidden or deferred until after first completed load cycle.

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

## 9–15
(Sections 9–15 from v1.3 remain unchanged.)

---

## 16. Adoption Success Criteria

The product meets the low-workload requirement only when:
- A carrier can complete their first Load Check from a WhatsApp photo or email in under 60 seconds with no account.
- The Daily Loop (evaluate → accept → POD → settlement) requires fewer manual steps than the carrier’s current Excel + WhatsApp process.
- Drivers can send a POD without installing or logging into a full app.
- The Attention Inbox surfaces at least three types of items the carrier previously tracked outside the system.
- Advanced features can be ignored indefinitely without blocking core use.

Any implementation that satisfies feature completeness but fails these criteria is incomplete.

This PRS defines the complete product surface: original economic intelligence, full CarriersPay parity, and binding low-friction adoption constraints for Excel/WhatsApp carriers. Any reduction in the adoption constraints requires an explicit product decision.
