# ALIMS — Purchase Request Approval Flow
## Actor: Collector Office

---

## Pre-Requisites

- Dealer must have submitted a Purchase Requisition within assigned Arms and Ammunition quota limits.
- System must have generated a unique **Request ID** upon successful submission.
- Submitted request must be available in the **Collector's Pending Request List**.
- Collector must be able to view all requested items with item-wise quantities.
- Requested quantities must not exceed the dealer's configured quota limits.

---

## Approval Flow

```mermaid
flowchart TD
    A([Collector logs in]) --> B[Open Pending Request List]
    B --> C[Select Request\nStatus: Pending]
    C --> D[View Request Details\nDealer Info · Request ID · Items]

    D --> E[Review Items\nItem-wise Table]

    E --> F{"For each item:\nMax Requested Qty\nvs\nAllowed Qty"}

    F -->|Adjust| G[Collector updates\nAllowed Qty\nAllowed Qty ≤ Requested Qty]
    G --> F

    F -->|All items reviewed| H[Approve Request]

    H --> I[System generates NOC]

    I --> J[Request Status → Approved]
    I --> K[NOC visible on\nCollector's approved request]
    I --> L[NOC reflected in\nDealer's Request View]

    L --> M[Dealer can proceed\nwith Inward Entry]

    style A fill:#4A6CF7,color:#fff,stroke:#3451D1
    style F fill:#F59E0B,color:#fff,stroke:#D97706
    style H fill:#16A34A,color:#fff,stroke:#15803D
    style I fill:#16A34A,color:#fff,stroke:#15803D
    style J fill:#16A34A,color:#fff,stroke:#15803D
    style K fill:#16A34A,color:#fff,stroke:#15803D
    style L fill:#16A34A,color:#fff,stroke:#15803D
    style M fill:#4A6CF7,color:#fff,stroke:#3451D1
```

---

## Item Review Table — What Collector Sees

| Field | Description |
|---|---|
| Item Name | Arm / Ammunition name |
| Category | Arms or Ammunition |
| Max Qty | Maximum allowed quantity for specific item |
| Requested Qty | Quantity requested by dealer |
| Allowed Qty | Editable — Collector sets this (≤ Requested Qty) |

---

## Status & NOC Flow

```mermaid
flowchart LR
    A[Request Submitted\nStatus: Pending] --> B[Collector Reviews\n& Adjusts Qty]
    B --> C[Collector Approves\nStatus: Approved]
    C --> D[NOC Generated]
    D --> E[NOC on Collector View]
    D --> F[NOC on Dealer View]
    F --> G[Dealer proceeds\nwith Inward Entry]

    style A fill:#6B7280,color:#fff,stroke:#4B5563
    style C fill:#16A34A,color:#fff,stroke:#15803D
    style D fill:#4A6CF7,color:#fff,stroke:#3451D1
    style G fill:#16A34A,color:#fff,stroke:#15803D
```
## DocTypes Used
|  DM USER |
| Dealer Procurement Request |
| Dealer NOC |

---

*Document: ALIMS_purchase_approval_flow.md | System: ALIMS v1.0 | Actor: Collector Office*
