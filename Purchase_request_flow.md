# ALIMS — System Process Flow
## Arms License & Inventory Management System

> **Starting Actor:** Collector Office  
> **Scope:** Citizen Onboarding → Dealer Onboarding → Initial Stock Entry → Active Registration

---

## Pre-Requisites

> The following conditions must be met before the onboarding process begins in ALIMS.

- Citizen and Dealer data will be imported from the **NDAL system** through API integration.
- NDAL will provide details such as **NDAL Number, Name, License Type, License Validity, Address,** and **Status**.
- Imported data will be used to onboard Citizens and Dealers in ALIMS.
- Citizen type (**Individual, Shooter, or Organisation**) will be identified from the license details.
- Permitted Arms, Ammunition, and applicable Quotas will be assigned based on the license information.
- Only successfully onboarded and verified records will become **active** in the system.

---

## Overview

```mermaid
graph TD
    COL([🏛️ Collector Office]) --> CO[Citizen Onboarding]
    COL --> DO[Dealer Onboarding]

    CO --> CRL[(Registered Citizens List)]
    DO --> ISE[Initial Stock Entry]
    ISE --> DRL[(Registered Dealers List)]

    style COL fill:#4A6CF7,color:#fff,stroke:#3451D1
    style CRL fill:#16A34A,color:#fff,stroke:#15803D
    style DRL fill:#16A34A,color:#fff,stroke:#15803D
    style ISE fill:#D97706,color:#fff,stroke:#B45309
```

---

## 1. Citizen Onboarding Flow

```mermaid
flowchart TD
    START([Collector logs in]) --> CP[Create Citizen Profile\nName, Address, License No, Contact]
    CP --> CT{Select Citizen Type}

    CT -->|Individual| IND[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• One-Time Quota\n• Yearly Ammo Quota]
    CT -->|Shooter| SHT[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• One-Time Quota\n• Yearly Ammo Quota]
    CT -->|Organisation| ORG[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• One-Time Quota\n• Yearly Ammo Quota]

    IND --> VAL{Profile\nValidation}
    SHT --> VAL
    ORG --> VAL

    VAL -->|Missing Fields| ERR1[❌ Validation Error\nHighlight missing fields]
    ERR1 --> CP

    VAL -->|Valid| ACTIVE[✅ Profile Saved\nStatus: Active]
    ACTIVE --> CRL_IND[(Registered Citizens List\n→ Respective Category)]

    style START fill:#4A6CF7,color:#fff,stroke:#3451D1
    style VAL fill:#F59E0B,color:#fff,stroke:#D97706
    style ERR1 fill:#EF4444,color:#fff,stroke:#DC2626
    style ACTIVE fill:#16A34A,color:#fff,stroke:#15803D
    style CRL_IND fill:#16A34A,color:#fff,stroke:#15803D
```

---

## 2. Dealer Onboarding Flow

```mermaid
flowchart TD
    START2([Collector logs in]) --> DP[Create Dealer Profile\nName, Shop, License No, Address, Contact]
    DP --> DA[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• Purchase Quota\n• Sale Quota per Citizen Type]
    DA --> VAL2{Profile\nValidation}

    VAL2 -->|Missing Fields| ERR2[❌ Validation Error\nHighlight missing fields]
    ERR2 --> DP

    VAL2 -->|Valid| DSAVE[Profile Saved\nStatus: Onboarding Incomplete ⚠️]
    DSAVE --> ISE[Initial Stock Entry\nUpdate Arms & Ammo Quantity in Stock]
    ISE --> SVAL{Stock\nValidation}

    SVAL -->|Invalid| ERR3[❌ Stock Validation Error]
    ERR3 --> ISE

    SVAL -->|Valid| DCOMP[✅ Inventory Updated\nStatus: Profile Completed]
    DCOMP --> DRL[(Registered Dealers List\nStatus: Active)]

    style START2 fill:#4A6CF7,color:#fff,stroke:#3451D1
    style VAL2 fill:#F59E0B,color:#fff,stroke:#D97706
    style SVAL fill:#F59E0B,color:#fff,stroke:#D97706
    style ERR2 fill:#EF4444,color:#fff,stroke:#DC2626
    style ERR3 fill:#EF4444,color:#fff,stroke:#DC2626
    style DSAVE fill:#D97706,color:#fff,stroke:#B45309
    style DCOMP fill:#16A34A,color:#fff,stroke:#15803D
    style DRL fill:#16A34A,color:#fff,stroke:#15803D
```

---

## 3. End-to-End Summary

```mermaid
flowchart TD
    COL([Collector Office]) --> CB1 & CB2

    subgraph CB1 [Citizen Onboarding]
        direction TB
        C1S[Create Profile] --> C2S[Select Type\nIndividual / Shooter / Organisation]
        C2S --> C3S[Assign Arms + Ammo + Quotas]
        C3S --> C4S[✅ Profile Active]
    end

    subgraph CB2 [Dealer Onboarding]
        direction TB
        D1S[Create Profile] --> D2S[Assign Allowed Arms + Ammo + Quotas]
        D2S --> D3S[⚠️ Onboarding Incomplete]
        D3S --> D4S[Initial Stock Entry\nUpdate Quantity in Stock]
        D4S --> D5S[✅ Profile Completed]
    end

    C4S -->|Auto registration| CLIST[(Registered Citizens List\nby Category)]
    D5S -->|Auto registration| DLIST[(Registered Dealers List\nActive)]

    style COL fill:#4A6CF7,color:#fff,stroke:#3451D1
    style CLIST fill:#16A34A,color:#fff,stroke:#15803D
    style DLIST fill:#16A34A,color:#fff,stroke:#15803D
    style D3S fill:#D97706,color:#fff,stroke:#B45309
```

---

*Document: ALIMS_flow.md | System: ALIMS v1.0 | Actor: Collector Office*

---

## 4. Dealer Purchase Requisition Flow

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

---

*Document: ALIMS_flow.md | System: ALIMS v1.0 | Actor: Collector Office / Dealer*
