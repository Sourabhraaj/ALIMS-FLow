# ALIMS — Sales Flow
## Actor: Dealer

---

## Pre-Requisites

- Dealer must be logged into the ALIMS Portal.
- Dealer must have sufficient stock available in inventory.
- Citizen profile must be onboarded and available in the system.
- Citizen details — Name, Address, Mobile, License Details, License Status, and Entity Type (Individual, Shooter, or Organisation) — must be available for verification.
- Citizen quota details including permitted Arms, Ammunition, allotted quantity, and remaining quota must be available.
- Only items permitted under the citizen's license and within available quota can be sold.
- Sales entry can be created only after successful profile verification and quota validation.

---

## Sales Flow

```mermaid
flowchart TD
    A([Dealer logs in]) --> B[Create New Sale Entry]
    B --> C[Enter NDAL Number]
    C --> D{Citizen Found\nin System?}

    D -->|Not Found| E[❌ Invalid NDAL Number\nCitizen not onboarded]
    D -->|Found| F[Display Citizen Profile\nName · Address · Mobile\nLicense Details · License Status\nEntity Type]

    F --> G{License\nStatus Valid?}
    G -->|Invalid / Expired| H[❌ Sale not allowed\nLicense inactive]
    G -->|Valid| I[Display Citizen Quota\nAllotted Arms · Allotted Ammunition\nOne-Time Quota · Yearly Quota\nRemaining Quota]

    I --> J[Dealer selects items to sell]

    J --> K{Item Category}

    K -->|Arms| L[Select Arm from\nCitizen's Allotted Arms\nEnter Qty · Enter SKU Number\nper unit sold]

    K -->|Ammunition| M[Select Ammunition\nbased on Arm Type\nEnter Qty\nQty ≤ One-Time Quota\nYearly Remaining Quota]

    L --> N{Within\nAllowed Quota?}
    M --> N

    N -->|Exceeds quota| O[❌ Validation Error\nReduce Quantity]
    O --> J

    N -->|Within quota| P{Dealer has\nsufficient stock?}
    P -->|No| Q[❌ Insufficient Stock\nCannot proceed]
    P -->|Yes| R[Confirm Sale Entry]

    R --> S[Sale Recorded]
    S --> T[Dealer Inventory Updated\nSold items reduced from stock]
    S --> U[Citizen Quota Updated\nRemaining quota reduced]

    style A fill:#4A6CF7,color:#fff,stroke:#3451D1
    style D fill:#F59E0B,color:#fff,stroke:#D97706
    style G fill:#F59E0B,color:#fff,stroke:#D97706
    style K fill:#F59E0B,color:#fff,stroke:#D97706
    style N fill:#F59E0B,color:#fff,stroke:#D97706
    style P fill:#F59E0B,color:#fff,stroke:#D97706
    style E fill:#EF4444,color:#fff,stroke:#DC2626
    style H fill:#EF4444,color:#fff,stroke:#DC2626
    style O fill:#EF4444,color:#fff,stroke:#DC2626
    style Q fill:#EF4444,color:#fff,stroke:#DC2626
    style R fill:#16A34A,color:#fff,stroke:#15803D
    style S fill:#16A34A,color:#fff,stroke:#15803D
    style T fill:#16A34A,color:#fff,stroke:#15803D
    style U fill:#16A34A,color:#fff,stroke:#15803D
```

---

## Quota Validation Rules

| Entity Type | Arms | Ammunition |
|---|---|---|
| Individual | As per allotted arms list | One-time qty limit + yearly limit |
| Shooter | As per allotted arms list | One-time qty limit + yearly limit |
| Organisation | As per allotted arms list | One-time qty limit + yearly limit |

> Quota limits differ per entity type — system validates against the citizen's specific configured quota.

---

## Post-Sale Updates

```mermaid
flowchart LR
    A[Sale Confirmed] --> B[Dealer Inventory\nStock reduced by sold qty\nSKU recorded per arm]
    A --> C[Citizen Quota\nRemaining quota updated\nOne-time & yearly]

    style A fill:#4A6CF7,color:#fff,stroke:#3451D1
    style B fill:#16A34A,color:#fff,stroke:#15803D
    style C fill:#16A34A,color:#fff,stroke:#15803D
```

---

*Document: ALIMS_sales_flow.md | System: ALIMS v1.0 | Actor: Dealer*
