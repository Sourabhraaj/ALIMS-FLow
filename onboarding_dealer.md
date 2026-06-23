# ALIMS — Dealer Onboarding Flow
## Arms License & Inventory Management System

> **Starting Actor:** Collector Office  
> **Scope:** Dealer Onboarding → Initial Stock Entry → Active Registration

---

## Pre-Requisites

- Dealer data will be imported from the **NDAL system** through API integration.
- NDAL will provide details such as **NDAL Number, Name, License Type, License Validity, Address,** and **Status**.
- Permitted Arms, Ammunition, and applicable Quotas will be assigned based on the license information.
- Only successfully onboarded and verified records will become **active** in the system.
- Initial Stock Entry done during onboarding will remain **editable until the dealer makes their first transaction**.

---

## Dealer Onboarding Flow

```mermaid
flowchart TD
    START([Collector logs in]) --> DP[Create Dealer Profile\nName, Shop, License No, Address, Contact]
    DP --> DA[Assign:\n• Allowed Arms\n• Allowed Ammunition\n• Purchase Quota\n• Sale Quota per Citizen Type]
    DA --> VAL{Profile\nValidation}

    VAL -->|Missing Fields| ERR1[❌ Validation Error\nHighlight missing fields]
    ERR1 --> DP

    VAL -->|Valid| DSAVE[Profile Saved\nStatus: Onboarding Incomplete ⚠️]
    DSAVE --> ISE[Initial Stock Entry\nUpdate Arms & Ammo Quantity in Stock]
    ISE --> SVAL{Stock\nValidation}

    SVAL -->|Invalid| ERR2[❌ Stock Validation Error]
    ERR2 --> ISE

    SVAL -->|Valid| DCOMP[✅ Inventory Updated\nStatus: Profile Completed]
    DCOMP --> DRL[(Registered Dealers List\nStatus: Active)]

    style START fill:#4A6CF7,color:#fff,stroke:#3451D1
    style VAL fill:#F59E0B,color:#fff,stroke:#D97706
    style SVAL fill:#F59E0B,color:#fff,stroke:#D97706
    style ERR1 fill:#EF4444,color:#fff,stroke:#DC2626
    style ERR2 fill:#EF4444,color:#fff,stroke:#DC2626
    style DSAVE fill:#D97706,color:#fff,stroke:#B45309
    style DCOMP fill:#16A34A,color:#fff,stroke:#15803D
    style DRL fill:#16A34A,color:#fff,stroke:#15803D
```

---

*Document: onboarding_dealer.md | System: ALIMS v1.0 | Actor: Collector Office*
