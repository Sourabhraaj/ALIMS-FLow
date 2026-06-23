# ALIMS — Inward Entry Flow
## Actor: Dealer

---

## Pre-Requisites

- Purchase Requisition must be approved by the Collector.
- A valid NOC must be generated against the approved request.
- NOC is valid for **30 days** from the date of issuance.
- All item receipts and stock entries must be completed within NOC validity.
- Once NOC expires, status changes to **Expired** — no further transactions allowed.

---

## Inward Entry Flow

```mermaid
flowchart TD
    A([Dealer logs in]) --> B[Open Approved Request\nwith valid NOC]

    B --> C{NOC Status}
    C -->|Expired| D[❌ No transaction allowed\nNOC Expired]
    C -->|Valid| E{Select Entry\nMethod}

    E -->|Manual Entry| F[Enter Received Qty\nper Item · Fill SKU Number]
    E -->|Excel Upload| G[Download Format\nFill & Upload Sheet]

    G --> H[Review & Edit\nUploaded Data]
    H --> I{Data Valid?}
    I -->|Errors found| H
    I -->|Valid| J[Confirm Entry]
    F --> J

    J --> K[Dealer Inventory Updated]
    K --> L{Received Qty vs\nAllowed Qty}

    L -->|Received = Allowed| M[✅ Receiving Status:\nCompleted]
    L -->|Received < Allowed| N[⚠️ Receiving Status:\nPartial\nRemaining qty still pending]

    N --> O[Dealer can raise\nanother Inward Entry\nagainst same NOC\nwithin 30 days]
    O --> C

    style A fill:#4A6CF7,color:#fff,stroke:#3451D1
    style C fill:#F59E0B,color:#fff,stroke:#D97706
    style D fill:#EF4444,color:#fff,stroke:#DC2626
    style E fill:#F59E0B,color:#fff,stroke:#D97706
    style I fill:#F59E0B,color:#fff,stroke:#D97706
    style L fill:#F59E0B,color:#fff,stroke:#D97706
    style J fill:#16A34A,color:#fff,stroke:#15803D
    style K fill:#16A34A,color:#fff,stroke:#15803D
    style M fill:#16A34A,color:#fff,stroke:#15803D
    style N fill:#D97706,color:#fff,stroke:#B45309
```

---

## Receiving Status Logic

| Condition | Receiving Status |
|---|---|
| Received Qty = Allowed Qty | Completed |
| Received Qty < Allowed Qty | Partial — further inward allowed within 30 days |
| NOC expired before completion | Expired — no further entry |

## DocTypes Used
| Dealer Stock Ledger Entry |
| NDAL Dealer Profile |
| Dealer NOC |
| Stock Item List |

---

*Document: ALIMS_inward_entry_flow.md | System: ALIMS v1.0 | Actor: Dealer*
