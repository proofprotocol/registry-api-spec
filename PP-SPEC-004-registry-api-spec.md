# PP-SPEC-004 · ProofRegister™ API Specification

**Document ID:** PP-SPEC-004  
**Version:** 0.1 - Draft  
**Status:** Draft  
**License:** CC BY 4.0  
**Maintained by:** Proof Economy™ Standards Alliance (PESA)  
**Repository:** https://github.com/proofprotocol/registry-api-spec  
**Published:** 2026-07-13  

---

## Abstract

This specification defines the ProofRegister™ API: the interface by which ProofBundles are submitted to ProofRegister™, queried by third parties, and verified by agents performing PP-A2P™ trust handshakes.

ProofRegister™ (proofregister.com) is the canonical public append-only ledger of ProofBundle™ records operated by HACKERverse. This specification defines the API surface that any conformant ProofRegister™ implementation must expose.

---

## Status of This Document

Draft. Subject to change before v1.0.

---

## Table of Contents

1. [Motivation](#1-motivation)
2. [Base URL](#2-base-url)
3. [Endpoints](#3-endpoints)
4. [ProofRecord Object](#4-proofrecord-object)
5. [Submission](#5-submission)
6. [Query](#6-query)
7. [Revocation](#7-revocation)
8. [ProofServer Integration](#8-proofserver-integration)
9. [Conformance](#9-conformance)
10. [Authors](#10-authors)

---

## 1. Motivation

A proof record is only as useful as the registry that holds it. The registry must be queryable by anyone, submittable by authorized parties, and permanently append-only. No record may be deleted. Revocation is a status flag, not a deletion.

This API is designed to be simple enough for any agent to query in real time during a PP-A2P™ trust handshake.

---

## 2. Base URL

```
https://proofregister.com/api/v1
```

All endpoints are HTTPS. No authentication required for queries. Submission requires an API key issued by HACKERverse.

---

## 3. Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | /records | Submit a ProofBundle™ |
| GET | /records/:id | Get a ProofRecord by ID |
| GET | /records | List records with filters |
| GET | /records/:id/verify | Verify a ProofRecord root hash |
| POST | /records/:id/revoke | Revoke a ProofRecord |

---

## 4. ProofRecord Object

```json
{
  "proof_record_id": "PR-2026-00001",
  "product_name": "string",
  "product_version": "string",
  "vendor": "string",
  "submitted_at": "RFC 3339 UTC",
  "anchor_timestamp": "RFC 3339 UTC",
  "root_hash": "sha256:hex",
  "nist_pulse_index": 0,
  "nist_output_value": "hex",
  "pes_score": 0.0,
  "proofstamp_id": "PS-2026-00001 | null",
  "status": "active | revoked",
  "revoked_at": "RFC 3339 UTC | null",
  "revocation_reason": "string | null"
}
```

---

## 5. Submission

```
POST /records
Authorization: Bearer <api_key>
Content-Type: multipart/form-data

bundle: <ProofBundle™ zip>
```

Returns:

```json
{
  "proof_record_id": "PR-2026-NNNNN",
  "anchor_timestamp": "RFC 3339 UTC",
  "root_hash": "sha256:hex",
  "nist_pulse_index": 0,
  "proofregister_uri": "https://proofregister.com/record/PR-2026-NNNNN"
}
```

---

## 6. Query

```
GET /records/PR-2026-00001
```

Returns the full ProofRecord object. No authentication required.

```
GET /records?product=Pipelock&version=3.0.0
```

Returns a list of ProofRecords matching the filter. Supports `product`, `version`, `vendor`, `status`, `from`, `to` query parameters.

---

## 7. Revocation

```
POST /records/PR-2026-00001/revoke
Authorization: Bearer <api_key>

{
  "reason": "string"
}
```

Sets `status` to `revoked`. Record is never deleted. Revocation is permanent and public.

---

## 8. ProofServer Integration

Enterprise deployments running ProofServer may submit bundles to ProofRegister™ via this API. ProofServer instances are issued a dedicated API key scoped to their organization.

Internal ProofServer bundles that are submitted to ProofRegister™ become part of the public record and are queryable by any third party. Organizations that require private anchoring must contact HACKERverse for enterprise terms.

---

## 9. Conformance

A ProofRegister™ implementation is conformant with this specification if it exposes all five endpoints, never deletes records, treats revocation as a status flag, and requires no authentication for queries.

---

## 10. Authors

Craig Ellrod, Founder & CEO, Nebulonium, Inc. (d/b/a HACKERverse)  
Castle Rock, Colorado  
2026-07-13

---

*CC BY 4.0 — Attribution to Craig Ellrod / Nebulonium, Inc. required.*
