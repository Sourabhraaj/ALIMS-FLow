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
    C -->|Valid| E{Select Inward\nEntry Type}

    E -->|Complete Inward| F[All items received\nat once]
    E -->|Partial Inward| G[Partial stock received\nReceiving Status: Partial]

    F --> H{Select Entry\nMethod}
    G --> H

    H -->|Manual Entry| I[Enter Received Qty\nper Item\nFill SKU Number]
    H -->|Excel Upload| J[Download Format\nFill & Upload Sheet]

    J --> K[Review & Edit\nUploaded Data]
    K --> L{Data Valid?}
    L -->|Errors found| K
    L -->|Valid| M[Confirm Entry]
    I --> M

    M --> N[Dealer Inventory Updated]
    N --> O{Entry Type?}
    O -->|Complete| P[Receiving Status:\nCompleted]
    O -->|Partial| Q[Receiving Status:\nPartial\nRemaining items pending]
    Q --> R[Dealer can do\nanother Inward Entry\nagainst same NOC]
    R --> C

    style A fill:#4A6CF7,color:#fff,stroke:#3451D1
    style C fill:#F59E0B,color:#fff,stroke:#D97706
    style D fill:#EF4444,color:#fff,stroke:#DC2626
    style E fill:#F59E0B,color:#fff,stroke:#D97706
    style H fill:#F59E0B,color:#fff,stroke:#D97706
    style L fill:#F59E0B,color:#fff,stroke:#D97706
    style O fill:#F59E0B,color:#fff,stroke:#D97706
    style M fill:#16A34A,color:#fff,stroke:#15803D
    style N fill:#16A34A,color:#fff,stroke:#15803D
    style P fill:#16A34A,color:#fff,stroke:#15803D
    style Q fill:#D97706,color:#fff,stroke:#B45309
```

---

## Receiving Status Summary

| Scenario | Receiving Status |
|---|---|
| All items received in one go | Completed |
| Partial stock received | Partial |
| NOC expired before completion | Expired — no further entry |

---

*Document: ALIMS_inward_entry_flow.md | System: ALIMS v1.0 | Actor: Dealer*
