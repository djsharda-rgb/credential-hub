# Test Vectors

## Overview

This document provides test vectors for implementers to validate their KYB credential verification logic.

## Test Vector 1: Valid Credential (Accept)

**Input:** `sample-credential.json`

**Expected Result:** `accepted`

| Check | Expected |
|---|---|
| Presentation Signature | passed |
| Credential Signature | passed |
| Issuer Trust | passed (issuer in registry) |
| Temporal Validity | passed (within 365-day window) |
| Revocation Status | passed (bit 42 = 0) |
| Required Fields | passed |

---

## Test Vector 2: Expired Credential (Reject)

**Input:** Modify `sample-credential.json`:
```json
{
  "issuanceDate": "2023-01-01T00:00:00Z",
  "expirationDate": "2024-01-01T00:00:00Z"
}
```

**Expected Result:** `rejected`

| Check | Expected |
|---|---|
| Credential Signature | passed |
| Temporal Validity | **failed** — `VER_004` (expired) |

---

## Test Vector 3: Revoked Credential (Reject)

**Input:** Use `sample-credential.json` but simulate bit 42 set to `1` in the status list.

**Expected Result:** `rejected`

| Check | Expected |
|---|---|
| Credential Signature | passed |
| Issuer Trust | passed |
| Revocation Status | **failed** — `VER_005` (revoked) |

---

## Test Vector 4: Untrusted Issuer (Reject)

**Input:** Modify `sample-credential.json`:
```json
{
  "issuer": "did:web:unknown-issuer.example"
}
```

**Expected Result:** `rejected`

| Check | Expected |
|---|---|
| Credential Signature | passed (signature valid but issuer not trusted) |
| Issuer Trust | **failed** — `VER_003` (untrusted issuer) |

---

## Test Vector 5: Missing Required Fields (Reject)

**Input:** Remove `registrationNumber` from `credentialSubject`.

**Expected Result:** `rejected`

| Check | Expected |
|---|---|
| Credential Signature | passed |
| Required Fields | **failed** — `VER_006` (missing: registrationNumber) |

---

## Test Vector 6: Unsupported Signature Suite (Reject)

**Input:** Modify proof type:
```json
{
  "proof": {
    "type": "RsaSignature2018"
  }
}
```

**Expected Result:** `rejected`

| Check | Expected |
|---|---|
| Credential Signature | **failed** — `VER_008` (unsupported suite) |

---

## Test Vector 7: Credential Too Old (Reject)

**Input:** Modify dates so credential was issued 400 days ago but not yet expired:
```json
{
  "issuanceDate": "2024-01-15T00:00:00Z",
  "expirationDate": "2026-01-15T00:00:00Z"
}
```

**Policy:** `maxCredentialAgeDays: 365`

**Expected Result:** `rejected`

| Check | Expected |
|---|---|
| Temporal Validity | passed (not expired) |
| Credential Age | **failed** — `VER_007` (age 400 days > 365 max) |
