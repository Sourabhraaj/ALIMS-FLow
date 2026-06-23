# ALIMS — Citizen Onboarding Flow
## Arms License & Inventory Management System

> **Starting Actor:** Collector Office  
> **Scope:** Citizen Onboarding → Active Registration

---

## Pre-Requisites

- Citizen data will be imported from the **NDAL system** through API integration.
- NDAL will provide details such as **NDAL Number, Name, License Type, License Validity, Address,** and **Status**.
- Citizen type (**Individual, Shooter, or Organisation**) will be identified from the license details.
- Permitted Arms, Ammunition, and applicable Quotas will be assigned based on the license information.
- Only successfully onboarded and verified records will become **active** in the system.

---

## Citizen Onboarding Flow

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
    ACTIVE --> CRL[(Registered Citizens List\n→ Respective Category)]

    style START fill:#4A6CF7,color:#fff,stroke:#3451D1
    style VAL fill:#F59E0B,color:#fff,stroke:#D97706
    style ERR1 fill:#EF4444,color:#fff,stroke:#DC2626
    style ACTIVE fill:#16A34A,color:#fff,stroke:#15803D
    style CRL fill:#16A34A,color:#fff,stroke:#15803D
```
## DocTypes Used
| 
NDAL Citizen Profile 
|
|Citizen Arms Quota List|
|Citizen Ammunition Quota list |
|NDAL Citizen License List |


---

*Document: onboarding_citizen.md | System: ALIMS v1.0 | Actor: Collector Office*
