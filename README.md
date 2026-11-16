# Haulpad

**Carrier operating intelligence and freight economics for small trucking companies.**

Haulpad helps independent and small for-hire carriers (primarily 3–10 trucks) evaluate load economics, track actual outcomes, identify profit leakage, and make better operating decisions — without forcing a full TMS migration or creating additional workload.

## Core Promise

> Run the operation. Know the economics. Make better moves.

A carrier can evaluate a load (via Free Load Check) and get a clear Take / Negotiate / Reject recommendation based on their own costs before creating an account or connecting any systems.

## Status

**PRS v1.4 complete.** Full original Haulpad economic intelligence, complete competitive parity with CarriersPay, and binding low-friction adoption constraints for Excel/WhatsApp carriers.

## Documentation

- **[Product Requirements Specification (PRS) v1.4](docs/Haulpad_Product_Requirements_Specification_v1.0.md)**  
  Complete, implementation-ready specification. Includes:
  - Original Haulpad intelligence layer (contribution engine, leakage categories, Attention Inbox, lane/broker views, scenario comparison, negotiation guidance, decision history, cash intelligence, document-to-cash)
  - Full CarriersPay operational parity (settlements, dispatch, driver/office mobile experience, IFTA, compliance, My Network, reports, maintenance, etc.)
  - Binding adoption constraints so the product never feels like additional workload for carriers who currently use Excel, notes, and WhatsApp
  - Data model, calculation formulas, functional requirements, acceptance criteria, and north-star metric

## Target Customer

US independent and small for-hire trucking carriers operating approximately 3–10 power units that currently run on spreadsheets, email, load boards, WhatsApp, and manual settlements.

## Key Principles

1. Useful before setup — value from the first Load Check.
2. Existing workflow is an integration — PDF, email, WhatsApp, spreadsheet, and manual entry are first-class.
3. Estimates and actuals remain distinct.
4. Recommendations are explainable.
5. Data captured as a by-product of work.
6. Full operational parity with category competitors; economic intelligence is the differentiator.
7. The product shall reduce or equal the carrier’s current administrative effort. It shall never increase it.

## Repository Structure

```
/
├── README.md
├── docs/
│   └── Haulpad_Product_Requirements_Specification_v1.0.md
└── (implementation to follow)
```

## Next Steps

1. Review and approve the PRS.
2. Begin implementation starting with Free Load Check + WhatsApp/email ingestion + evaluation-to-load conversion.
3. Build the calculation engine as a pure, deterministic, versioned function.

---

*Haulpad is developed by Skoorite Ltd.*
