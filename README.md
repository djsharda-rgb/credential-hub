## What this diagram communicates
It shows four things immediately:

- the business completes KYB once
- providers run checks
- a credential gets issued
- a relying party verifies it with trust and revocation controls


## System Overview

```mermaid
flowchart LR
    B[Business Applicant] --> W[KYB Wallet]
    W --> E[Evidence Store]
    W --> P[Provider Connectors]

    P --> R1[Business Registry Check]
    P --> R2[Sanctions Screening]
    P --> R3[Bank Account Verification]
    P --> R4[UBO / Adverse Media]

    W --> I[Credential Issuer]
    I --> C[Signed KYB Credential]

    B --> S[Consent and Sharing Service]
    S --> T[Scoped Share Token]

    T --> RP[Relying Party<br/>Payout Platform / Bank / Marketplace]
    RP --> V[Verification Service]

    V --> IR[Issuer Registry]
    V --> RS[Revocation Status Endpoint]
    V --> C

    V --> D{Decision}
    D -->|Accept| A[Fast-track Onboarding]
    D -->|Review| M[Manual Review]
    D -->|Reject| X[Reject]


```markdown
This architecture separates **identity verification**, **credential issuance**, and **credential verification** so that a business can reuse verified KYB status across multiple financial platforms without repeatedly submitting the same documents.


Credential Hub
Reference implementation for reusable KYB credentials.
