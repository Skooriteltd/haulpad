# Haulpad

**Carrier operating intelligence and freight economics for small trucking companies.**

Haulpad helps independent and small for-hire carriers (primarily 3–10 trucks) evaluate load economics, track actual outcomes, identify profit leakage, and make better operating decisions — without forcing a full TMS migration.

## Core Promise

> Run the operation. Know the economics. Make better moves.

A carrier can evaluate a load (via Free Load Check) and get a clear Take / Negotiate / Reject recommendation based on their own costs before creating an account or connecting any systems.

## Status

**MVP Specification complete.** Implementation baseline defined in the Product Requirements Specification.

## Documentation

- **[Product Requirements Specification (PRS) v1.0](docs/Haulpad_Product_Requirements_Specification_v1.0.md)**  
  Complete, implementation-ready specification for the coding agent. Includes data model, calculation engine, functional requirements, state machines, acceptance criteria, and explicit in/out of scope.

## Target Customer

US independent and small for-hire trucking carriers operating approximately 3–10 power units that currently run on spreadsheets, email, load boards, and manual settlements.

## Key Principles

1. Useful before setup — value from the first Load Check.
2. Existing workflow is an integration — PDF, email, spreadsheet, and manual entry are first-class.
3. Estimates and actuals remain distinct.
4. Recommendations are explainable.
5. Data captured as a by-product of work.

## Repository Structure (planned)

```
/
├── README.md
├── docs/
│   └── Haulpad_Product_Requirements_Specification_v1.0.md
└── (implementation to follow)
```

## Next Steps

1. Review and approve the PRS.
2. Begin implementation starting with Free Load Check + evaluation-to-load conversion.
3. Build the calculation engine as a pure, deterministic, versioned function.

---

*Haulpad is developed by Skoorite Ltd.*
