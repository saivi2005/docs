**Cryptographic Operations API**

Provides secure cryptographic operations using Authentication Keys, Post-Quantum Cryptography (PQC) Keys, Policies, IP Whitelisting, Mutual TLS (mTLS), and Rate Limiting controls. All cryptographic operations are validated against active policies before execution.

**Table of Contents**

*   General API Information
*   Authentication
*   Security Controls
*   Supported Operations
*   Supported Algorithms
*   API
    *   Sign Data
    *   Verify Signature
    *   Encapsulate Secret
    *   Decapsulate Secret
    *   Encrypt Data
    *   Decrypt Data
*   Policy Enforcement
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   All cryptographic operations require authentication.
*   All operations are validated against active policies.
*   Authentication Keys and PQC Keys must both be active.
*   Operations are audited and recorded in Audit Logs.
*   IP Whitelisting restrictions are enforced before operation execution.
*   Mutual TLS (mTLS) validation is enforced when enabled.
*   Crypto endpoints are protected by rate limiting.

**Authentication**

All Crypto endpoints require:

Authorization: Bearer <access\_token>

x-pqc-key-id: <pqc\_key\_id>

x-auth-key-id: <auth\_key\_id>

**Security Controls**

Before any cryptographic operation is executed, the platform validates:

1.  User authentication.
2.  Active policy exists.
3.  PQC key is active.
4.  Authentication key is active.
5.  Requested operation is permitted.
6.  IP address is whitelisted (if enabled).
7.  mTLS certificate requirements are satisfied.
8.  Rate limits have not been exceeded.

**Supported Operations**

| Operation | Description |
| --- | --- |
| Sign | Generate digital signatures |
| Verify | Verify digital signatures |
| Encapsulate | Generate shared secrets using KEM |
| Decapsulate | Recover shared secrets |
| Encrypt | Symmetric encryption |
| Decrypt | Symmetric decryption |

**Supported Algorithms**

| Algorithm | Supported Operations |
| --- | --- |
| ML-DSA | Sign, Verify |
| ECDSA | Sign, Verify |
| Hybrid-DSA | Sign, Verify |
| ML-KEM | Encapsulate, Decapsulate |
| Hybrid-KEM | Encapsulate, Decapsulate |
| AES-256 | Encrypt, Decrypt |

**API**

**Sign Data**

Generate a digital signature using the configured PQC or Hybrid signing key.

**Request**

POST /api/crypto/sign

**Headers**

| Header | Required |
| --- | --- |
| Authorization | Yes |
| x-pqc-key-id | Yes |
| x-auth-key-id | Yes |

**Request Body**

{

"data": "Hello World"

}

**Success Response**

{

"signature": "base64\_signature\_data"

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Signature generated successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Missing parameters |
| 403 Forbidden | Policy restriction |
| 403 Forbidden | IP whitelist violation |
| 403 Forbidden | MTLS requirement failed |
| 500 Internal Server Error | Signature generation failed |

**Verify Signature**

Verify a digital signature.

**Request**

POST /api/crypto/verify

**Request Body**

{

"data": "Hello World",

"signature": "base64\_signature\_data"

}

**Success Response**

{

"isValid": true

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Signature verification completed |

**Encapsulate Secret**

Generate a shared secret using a Key Encapsulation Mechanism (KEM).

**Request**

POST /api/crypto/encapsulate

**Success Response (ML-KEM)**

{

"ciphertext": "encapsulated\_ciphertext",

"sharedSecret": "shared\_secret"

}

**Success Response (Hybrid-KEM)**

{

"ciphertext": {

"pqc": "pqc\_ciphertext",

"classical": "classical\_ciphertext"

},

"sharedSecret": {

"pqc": "pqc\_secret",

"classical": "classical\_secret"

}

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Shared secret generated successfully |

**Decapsulate Secret**

Recover a shared secret from encapsulated ciphertext.

**Request**

POST /api/crypto/decapsulate

**Request Body**

{

"ciphertext": "encapsulated\_ciphertext"

}

**Success Response**

{

"sharedSecret": "shared\_secret"

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Shared secret recovered successfully |

**Encrypt Data**

Encrypt data using an AES-256 PQC key.

**Request**

POST /api/crypto/encrypt

**Request Body**

{

"data": "Sensitive Information"

}

**Success Response**

{

"ciphertext": "encrypted\_data",

"iv": "initialization\_vector",

"authTag": "authentication\_tag"

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Data encrypted successfully |

**Decrypt Data**

Decrypt previously encrypted data.

**Request**

POST /api/crypto/decrypt

**Request Body**

{

"ciphertext": "encrypted\_data",

"iv": "initialization\_vector",

"authTag": "authentication\_tag"

}

**Success Response**

{

"data": "Sensitive Information"

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Data decrypted successfully |

**Policy Enforcement**

Every cryptographic operation is validated against an active policy.

A policy must satisfy:

*   Authentication Key is active.
*   PQC Key is active.
*   Requested operation is listed in policy operations.
*   Key algorithm supports requested operation.

Examples:

| Operation | Valid Algorithms |
| --- | --- |
| Sign | ML-DSA, ECDSA, Hybrid-DSA |
| Verify | ML-DSA, ECDSA, Hybrid-DSA |
| Encapsulate | ML-KEM, Hybrid-KEM |
| Decapsulate | ML-KEM, Hybrid-KEM |
| Encrypt | AES-256 |
| Decrypt | AES-256 |

Operations attempted against unsupported algorithms are rejected.

**Security Notes**

*   All cryptographic operations generate audit log entries.
*   IP whitelist restrictions are enforced before execution.
*   MTLS certificate validation is enforced when enabled.
*   Policy validation occurs before cryptographic processing.
*   Disabled keys cannot be used.
*   Rotated keys cannot be used unless referenced by an active policy.
*   Hybrid algorithms provide both classical and post-quantum security.
*   Shared secrets generated through KEM operations should be treated as highly sensitive.
*   AES-256 encryption uses authenticated encryption mechanisms.
*   Rate limiting protects cryptographic endpoints from abuse and denial-of-service attacks.
