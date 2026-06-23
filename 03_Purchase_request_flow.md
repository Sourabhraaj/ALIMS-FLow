# ALIMS — System Process Flow
## Arms License & Inventory Management System

> **Starting Actor:** Dealer

## . Dealer Purchase Requisition Flow

### Pre-Requisites

- Dealer must be logged into the ALIMS Portal with an active profile.
- Dealer quotas for Arms and Ammunition must be configured.
- Current Stock and Available Stock must be updated in the system.
- Permitted Arms and Ammunition must be mapped to the dealer profile.

---

### Purchase Requisition Flow

```mermaid
flowchart TD
    A([Dealer logs in]) --> B[Create Purchase Requisition]
    B --> C{Select Source\nof Purchase}

    C -->|Dealer within MP| D[Select from\nRegistered Dealers List\nDropdown]
    C -->|Dealer outside MP /\nManufacturer| E{Vendor\nalready added?}

    E -->|No| F[Go to Manage Vendor\nAdd Vendor Details]
    F --> G[Vendor saved\nin Dealer's Vendor List]
    G --> H[Select Vendor\nfrom Vendor List]
    E -->|Yes| H

    D --> I[Add Items]
    H --> I

    I --> J{Item Category}

    J -->|Arms| K[Select Arm\nQty ≤ Allowed Limit]
    J -->|Ammunition| L[Select Arm Type →\nSelect Ammunition\nQty ≤ Allowed Limit]

    K --> M{Qty within\nallowed limit?}
    L --> M

    M -->|Exceeds limit| N[❌ Validation Error\nReduce Quantity]
    N --> K & L

    M -->|Within limit| O[Additional Details\nOptional]
    O --> P[Submit Request\nSaved as Draft]
    P --> Q[Dealer Reviews Draft]
    Q --> R[Submit to Collector]
    R --> S[(Collector Inbox\nPending Approval)]

    style A fill:#4A6CF7,color:#fff,stroke:#3451D1
    style C fill:#F59E0B,color:#fff,stroke:#D97706
    style E fill:#F59E0B,color:#fff,stroke:#D97706
    style J fill:#F59E0B,color:#fff,stroke:#D97706
    style M fill:#F59E0B,color:#fff,stroke:#D97706
    style N fill:#EF4444,color:#fff,stroke:#DC2626
    style F fill:#D97706,color:#fff,stroke:#B45309
    style P fill:#6B7280,color:#fff,stroke:#4B5563
    style R fill:#16A34A,color:#fff,stroke:#15803D
    style S fill:#16A34A,color:#fff,stroke:#15803D
```
## DocTypes Used

- NDAL Dealer Profile
- Vendor Master
- Procurement Arms List
- Procurement Ammunition List
- Dealer NOC
---

*Document: ALIMS_flow.md | System: ALIMS v1.0 | Actor: Collector Office / Dealer*
