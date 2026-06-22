# ALIMS — System Process Flow
## Arms License & Inventory Management System

> **Starting Actor:** Collector Office  
> **Scope:** Citizen Onboarding → Dealer Onboarding → Initial Stock Entry → Active Registration

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
    CT -->|Shooter| SHT[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• One-Time Quota\n• Yearly Ammo Quota\n• Shooting Range Details]
    CT -->|Organisation| ORG[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• One-Time Quota\n• Yearly Ammo Quota\n• Organisation License Details]

    IND --> VAL{Profile\nValidation}
    SHT --> VAL
    ORG --> VAL

    VAL -->|Incomplete / Missing Fields| ERR1[❌ Validation Error\nHighlight missing fields]
    ERR1 --> CP

    VAL -->|Valid| SAVE[Save Citizen Profile\nStatus: Draft]
    SAVE --> ACT[Collector Activates Profile\nStatus: Active]
    ACT --> AUTO1{Auto Registration\nTrigger}

    AUTO1 --> CRL_IND[(Registered Citizens List\n→ Individual Category)]
    AUTO1 --> CRL_SHT[(Registered Citizens List\n→ Shooter Category)]
    AUTO1 --> CRL_ORG[(Registered Citizens List\n→ Organisation Category)]

    style START fill:#4A6CF7,color:#fff,stroke:#3451D1
    style VAL fill:#F59E0B,color:#fff,stroke:#D97706
    style ERR1 fill:#EF4444,color:#fff,stroke:#DC2626
    style ACT fill:#16A34A,color:#fff,stroke:#15803D
    style AUTO1 fill:#16A34A,color:#fff,stroke:#15803D
    style CRL_IND fill:#16A34A,color:#fff,stroke:#15803D
    style CRL_SHT fill:#16A34A,color:#fff,stroke:#15803D
    style CRL_ORG fill:#16A34A,color:#fff,stroke:#15803D
```

### Citizen Type — Quota Assignment Rules

| Field | Individual | Shooter | Organisation |
|---|---|---|---|
| Allowed Arms | ✅ | ✅ | ✅ |
| Allowed Ammunition | ✅ | ✅ | ✅ |
| One-Time Quota | ✅ | ✅ | ✅ |
| Yearly Ammo Quota | ✅ | ✅ | ✅ |
| Shooting Range Details | ❌ | ✅ | ❌ |
| Organisation License | ❌ | ❌ | ✅ |

### Citizen Profile — Status Transitions

```mermaid
stateDiagram-v2
    [*] --> Draft : Collector creates profile
    Draft --> Active : Collector activates
    Active --> Suspended : Collector suspends
    Suspended --> Active : Collector reactivates
    Active --> [*] : Profile deactivated

    note right of Active
        Auto-appears in
        Registered Citizens List
    end note
```

---

## 2. Dealer Onboarding Flow

```mermaid
flowchart TD
    START2([Collector logs in]) --> DP[Create Dealer Profile\nName, Shop, License No, Address, Contact]
    DP --> DA[Assign Dealer Quotas & Permissions:\n• Allowed Arms\n• Allowed Ammunition\n• Purchase Quota\n• Sale Quota per Citizen Type]
    DA --> VAL2{Profile\nValidation}

    VAL2 -->|Missing Fields| ERR2[❌ Validation Error\nHighlight missing fields]
    ERR2 --> DP

    VAL2 -->|Valid| DSAVE[Save Dealer Profile\nStatus: Onboarding Incomplete ⚠️]

    DSAVE --> WARN[⚠️ System Warning:\nProfile incomplete until\nInitial Stock Entry is done]
    WARN --> ISE_START

    subgraph ISE_START [Initial Stock Entry]
        ISE1[Open Stock Entry Form\nfor this Dealer]
        ISE1 --> ISE2[Add Arms Inventory:\n• Arm Type / Model\n• Quantity in Stock\n• Serial / Chassis Numbers]
        ISE2 --> ISE3[Add Ammunition Inventory:\n• Ammo Type / Calibre\n• Quantity in Stock\n• Batch / Lot Details]
        ISE3 --> ISE4[Add Item-level Details:\n• Unit Price\n• Source of Purchase\n• Date of Receipt]
    end

    ISE4 --> SVAL{Stock\nValidation}

    SVAL -->|Invalid quantities\nor missing item details| ERR3[❌ Stock Validation Error\nDealer status unchanged]
    ERR3 --> ISE1

    SVAL -->|All entries valid| SUPD[✅ Inventory Updated\nStock Ledger Created]
    SUPD --> DCOMP[Dealer Profile Status:\nProfile Completed ✅]
    DCOMP --> AUTO2{Auto Registration\nTrigger}
    AUTO2 --> DRL[(Registered Dealers List\nStatus: Active Dealer)]

    style START2 fill:#4A6CF7,color:#fff,stroke:#3451D1
    style VAL2 fill:#F59E0B,color:#fff,stroke:#D97706
    style SVAL fill:#F59E0B,color:#fff,stroke:#D97706
    style ERR2 fill:#EF4444,color:#fff,stroke:#DC2626
    style ERR3 fill:#EF4444,color:#fff,stroke:#DC2626
    style WARN fill:#D97706,color:#fff,stroke:#B45309
    style DSAVE fill:#D97706,color:#fff,stroke:#B45309
    style SUPD fill:#16A34A,color:#fff,stroke:#15803D
    style DCOMP fill:#16A34A,color:#fff,stroke:#15803D
    style AUTO2 fill:#16A34A,color:#fff,stroke:#15803D
    style DRL fill:#16A34A,color:#fff,stroke:#15803D
```

### Dealer Profile — Status Transitions

```mermaid
stateDiagram-v2
    [*] --> OnboardingIncomplete : Collector creates + assigns quotas
    OnboardingIncomplete --> ProfileCompleted : Initial Stock Entry validated
    ProfileCompleted --> Active : Auto-registered
    Active --> Suspended : Collector action
    Suspended --> Active : Collector reactivates

    note right of OnboardingIncomplete
        NOT visible in
        Registered Dealers List
    end note

    note right of Active
        Auto-appears in
        Registered Dealers List
    end note
```

---

## 3. Initial Stock Entry — Detail Flow

```mermaid
flowchart LR
    A[Select Dealer\nfor Stock Entry] --> B[Arms Tab]
    A --> C[Ammunition Tab]

    B --> B1[Arm Type]
    B --> B2[Model / Make]
    B --> B3[Quantity]
    B --> B4[Serial / Chassis No.]
    B --> B5[Source of Purchase]

    C --> C1[Ammo Type / Calibre]
    C --> C2[Quantity in Units]
    C --> C3[Batch / Lot No.]
    C --> C4[Source of Purchase]
    C --> C5[Date of Receipt]

    B1 & B2 & B3 & B4 & B5 --> VSTK{Validate\nStock Entry}
    C1 & C2 & C3 & C4 & C5 --> VSTK

    VSTK -->|Pass| COMMIT[Commit to\nStock Ledger]
    VSTK -->|Fail| RETRY[Return errors\nto Collector]
    COMMIT --> STATUS[Profile Completed\n→ Dealer goes Active]

    style VSTK fill:#F59E0B,color:#fff,stroke:#D97706
    style COMMIT fill:#16A34A,color:#fff,stroke:#15803D
    style STATUS fill:#16A34A,color:#fff,stroke:#15803D
    style RETRY fill:#EF4444,color:#fff,stroke:#DC2626
```

---

## 4. End-to-End Summary Flow

```mermaid
flowchart TD
    COL([Collector Office]) --> CB1 & CB2

    subgraph CB1 [Citizen Onboarding]
        direction TB
        C1S[Create Profile] --> C2S[Select Type\nIndividual / Shooter / Organisation]
        C2S --> C3S[Assign Arms +\nAmmo + Quotas]
        C3S --> C4S[Activate Profile]
    end

    subgraph CB2 [Dealer Onboarding]
        direction TB
        D1S[Create Profile] --> D2S[Assign Allowed\nArms + Ammo + Quotas]
        D2S --> D3S[Status: Onboarding Incomplete]
        D3S --> D4S[Initial Stock Entry\nArms + Ammo + Item Details]
        D4S --> D5S[Stock Validation]
        D5S --> D6S[Status: Profile Completed]
    end

    C4S -->|Auto registration| CLIST[(Registered Citizens List\nby Category)]
    D6S -->|Auto registration| DLIST[(Registered Dealers List\nActive)]

    style COL fill:#4A6CF7,color:#fff,stroke:#3451D1
    style CLIST fill:#16A34A,color:#fff,stroke:#15803D
    style DLIST fill:#16A34A,color:#fff,stroke:#15803D
    style D3S fill:#D97706,color:#fff,stroke:#B45309
```

---

## 5. Key Business Rules

| # | Rule |
|---|---|
| BR-01 | Citizen profile becomes visible in Registered Citizens List **only after** Collector activates the profile. |
| BR-02 | Citizen type selection is **mandatory** before quota assignment. Quotas differ per type. |
| BR-03 | Dealer profile saved after quota assignment is marked **Onboarding Incomplete** — not visible in Registered Dealers List. |
| BR-04 | Dealer registration is **not complete** until Initial Stock Entry is successfully validated. |
| BR-05 | Initial Stock Entry must cover **both Arms and Ammunition** inventory. Partial entry fails validation. |
| BR-06 | On successful stock validation, dealer status auto-transitions to **Profile Completed** and dealer appears in Registered Dealers List as **Active**. |
| BR-07 | Stock Ledger is initialised from the Initial Stock Entry — all future purchases and sales are tracked against this base. |
| BR-08 | Citizen auto-appears under their **respective category** (Individual / Shooter / Organisation) in the list — not a single combined list. |

---

*Document: ALIMS_flow.md | System: ALIMS v1.0 | Actor: Collector Office*
