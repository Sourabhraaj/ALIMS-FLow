flowchart TD
    A[Dealer Login] --> B[Click Repairing Module]
    B --> C{Sub Options}
    C --> D[Inventory]
    C --> E[Inward Entry]
    C --> F[Outward Entry]

    E --> G[Click Create Inward Entry]
    G --> H[Search Licensee by NDAL Number]
    H --> I[Fetch Licensee Details]
    I --> J[Show Arms Held by Licensee<br/>with SKU Number]
    J --> K[User Selects Arm to Inward]

    K --> L{License Status Check}
    L -->|Inactive or Expired| M[Block Inward Entry<br/>Show Error Message]
    M --> K

    L -->|Active and Valid| N[Create Inward Entry]
    N --> O[Store in Inventory:<br/>Dealer Name, NDAL Number,<br/>Weapon Name, Weapon Number]
    O --> D