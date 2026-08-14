# Repairing Module — Inward Entry Flow

```mermaid
flowchart TD
    A[Dealer] --> B[Repairing module]
    B --> C[Inventory]
    B --> D[Inward entry]
    B --> E[Outward entry]

    D --> F[Create inward entry]
    F --> G[Search licensee NDAL number]
    G --> H[Fetch licensee details]
    H --> I[Show arms held by licensee with SKU number]
    I --> J[Select arm to inward]
    J --> K{License active and not expired?}

    K -->|No| L[Cannot inward - license inactive or expired]
    K -->|Yes| M[Create inward entry record]
    M --> N[Store in inventory: dealer name, NDAL number, weapon name, weapon number]

    style C fill:#f1efe8,stroke:#5f5e5a
    style E fill:#f1efe8,stroke:#5f5e5a
    style D fill:#9fe1cb,stroke:#0f6e56
    style G fill:#9fe1cb,stroke:#0f6e56
    style H fill:#9fe1cb,stroke:#0f6e56
    style I fill:#9fe1cb,stroke:#0f6e56
    style J fill:#9fe1cb,stroke:#0f6e56
    style K fill:#fac775,stroke:#854f0b
    style L fill:#f7c1c1,stroke:#a32d2d
    style M fill:#c0dd97,stroke:#3b6d11
    style N fill:#c0dd97,stroke:#3b6d11
## Inward Entry — Conditional Scenarios Matrix

| # | Scenario | NDAL Search Result | License Status | Arm Selected | System Behavior | Data Stored? |
|---|----------|--------------------|-----------------| --------------|------------------|--------------|
| 1 | Valid NDAL, active license, arm available | Found | Active | Yes | Inward entry created | Yes |
| 2 | Valid NDAL, but license expired | Found | Expired | Yes | Reject - cannot inward | No |
| 3 | Valid NDAL, but license inactive/suspended | Found | Inactive | Yes | Reject - cannot inward | No |
| 4 | NDAL number does not exist | Not found | - | - | Show error - licensee not found | No |
| 5 | NDAL exists but licensee has zero arms held | Found | Active | No arms available | Show "No arms found for this licensee" | No |
| 6 | Licensee has arms, but selected arm already inward (duplicate SKU) | Found | Active | Yes (duplicate) | Reject - "Arm already in inventory" | No |
| 7 | Licensee has arms, dealer selects multiple arms in one go | Found | Active | Multiple | Loop/batch create entry per SKU (if bulk allowed) | Yes, per arm |
| 8 | License active but about to expire (grace period, if applicable) | Found | Active (near expiry) | Yes | Allow inward + optional warning shown | Yes |
| 9 | Dealer cancels mid-flow (before final submit) | Found | Any | Selected but not submitted | No entry created, form discarded | No |
| 10 | Network/API failure during NDAL search | Timeout/error | - | - | Show retry error, no partial data | No |
| 11 | Network/API failure after license valid, before save | Found | Active | Yes | Rollback, show error, no partial entry | No |
| 12 | Arm SKU already inward by a different dealer | Found | Active | Yes | Reject - "SKU already assigned elsewhere" | No |
| 13 | Licensee record exists but is blacklisted/flagged | Found | Blacklisted | Yes | Reject - "Licensee flagged, contact admin" | No |
| 14 | Dealer re-opens same NDAL after successful inward (re-check) | Found | Active | Remaining arms shown (already-inward ones excluded) | Normal flow continues | Yes (for new selection) |
```