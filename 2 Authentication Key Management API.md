**Authentication Key Management API**

Provides authentication key generation, storage, management, fingerprinting, status management, and hybrid cryptographic key support. Authentication keys are used throughout the platform for identity verification, digital signatures, policy enforcement, and cryptographic operations.

**Table of Contents**

*   General API Information
*   Authentication
*   Supported Algorithms
*   API
    *   Get Authentication Keys
    *   Create Authentication Key
    *   Update Authentication Key
*   Authentication Key Object
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   All endpoints require authentication.
*   Authentication keys are generated and managed per user account.
*   Each key is assigned a unique fingerprint calculated using SHA-256.
*   Hybrid keys combine classical and post-quantum cryptographic algorithms.
*   Private keys are encrypted before storage.

**Authentication**

All Authentication Key Management endpoints require a valid JWT access token.

Example:

Authorization: Bearer <access\_token>

**Supported Algorithms**

The Authentication Key Management API supports the following algorithms:

| Algorithm | Description |
| --- | --- |
| RSA | Rivest–Shamir–Adleman public key cryptography |
| ECDSA | Elliptic Curve Digital Signature Algorithm |
| ML-DSA | Post-Quantum Digital Signature Algorithm |
| Hybrid | Combination of ECDSA and ML-DSA |

**API**

**Get Authentication Keys**

Retrieve all authentication keys belonging to the authenticated user.

**Request**

GET /api/auth-keys

**Response**

\[

{

"id": "f8d9e1d0-7f4c-4f56-a2e4-b3e2d6f9c100",

"name": "Primary Signing Key",

"algorithm": "RSA",

"fingerprint": "d3c9c3f96c8d9d5f4a9f5c5f74e8a7f0",

"publicKey": "-----BEGIN PUBLIC KEY-----...",

"publicKeyDsa": null,

"status": "active",

"created": "2025-06-05T10:15:30.000Z",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

\]

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| id | String (UUID) | Unique authentication key identifier |
| name | String | Authentication key name |
| algorithm | String | Cryptographic algorithm used |
| fingerprint | String | SHA-256 fingerprint of the key |
| publicKey | String | Classical public key |
| publicKeyDsa | String / Null | PQC public key component |
| status | String | Current key status |
| created | String | Key creation timestamp |
| createdAt | String | Database creation timestamp |
| updatedAt | String | Last modification timestamp |

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Authentication keys retrieved successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to retrieve authentication keys |

**Create Authentication Key**

Create a new authentication key using one of the supported cryptographic algorithms.

**Request**

POST /api/auth-keys

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| name | String | Required | Authentication key name |
| algorithm | String | Required | Cryptographic algorithm |
| status | String | Optional | Initial key status |

**Example Request**

{

"name": "Primary Signing Key",

"algorithm": "RSA",

"status": "active"

}

**Example Request (Hybrid)**

{

"name": "Hybrid Enterprise Key",

"algorithm": "Hybrid"

}

**Success Response**

{

"id": "f8d9e1d0-7f4c-4f56-a2e4-b3e2d6f9c100",

"name": "Primary Signing Key",

"algorithm": "RSA",

"fingerprint": "d3c9c3f96c8d9d5f4a9f5c5f74e8a7f0",

"publicKey": "-----BEGIN PUBLIC KEY-----...",

"privateKey": "-----BEGIN PRIVATE KEY-----...",

"publicKeyDsa": null,

"privateKeyDsa": null,

"status": "active",

"created": "2025-06-05T10:15:30.000Z",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| id | String (UUID) | Unique key identifier |
| name | String | Authentication key name |
| algorithm | String | Selected cryptographic algorithm |
| fingerprint | String | SHA-256 fingerprint |
| publicKey | String | Generated classical public key |
| privateKey | String | Generated classical private key |
| publicKeyDsa | String / Null | Generated PQC public key |
| privateKeyDsa | String / Null | Generated PQC private key |
| status | String | Current key status |

**Validation Rules**

| Field | Rule |
| --- | --- |
| name | Must not be empty |
| algorithm | Must be RSA, ECDSA, ML-DSA, or Hybrid |

**Success Response**

| Status Code | Description |
| --- | --- |
| 201 Created | Authentication key created successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Missing or invalid fields |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Key generation failed |

**Update Authentication Key**

Update an existing authentication key.

**Request**

PUT /api/auth-keys/{id}

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| name | String | Optional | New authentication key name |
| status | String | Optional | Updated status |

**Example Request**

{

"name": "Updated Signing Key"

}

**Example Request (Status Change)**

{

"status": "disabled"

}

**Success Response**

{

"id": "f8d9e1d0-7f4c-4f56-a2e4-b3e2d6f9c100",

"name": "Updated Signing Key",

"algorithm": "RSA",

"fingerprint": "d3c9c3f96c8d9d5f4a9f5c5f74e8a7f0",

"publicKey": "-----BEGIN PUBLIC KEY-----...",

"publicKeyDsa": null,

"status": "disabled",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-06T11:22:45.000Z"

}

**Validation Rules**

| Field | Rule |
| --- | --- |
| name | Cannot be empty |
| status | Must be active or disabled |

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Authentication key updated successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Invalid update parameters |
| 401 Unauthorized | Invalid or missing authentication token |
| 404 Not Found | Authentication key not found |
| 500 Internal Server Error | Failed to update authentication key |

**Authentication Key Object**

Authentication keys are represented using the following structure:

{

"id": "uuid",

"name": "Primary Signing Key",

"algorithm": "RSA",

"fingerprint": "sha256\_fingerprint",

"publicKey": "public\_key\_data",

"publicKeyDsa": "pqc\_public\_key\_data",

"status": "active",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

**Security Notes**

*   Private keys are encrypted before being stored.
*   SHA-256 fingerprints are generated automatically for all keys.
*   Hybrid keys combine classical and post-quantum cryptographic algorithms.
*   Authentication keys are isolated per user account.
*   Status changes are recorded in audit logs.
*   Key creation, modification, and status updates generate audit trail entries.
*   Renaming an authentication key automatically updates linked policy references.
*   Private key material should never be shared with unauthorized users.
