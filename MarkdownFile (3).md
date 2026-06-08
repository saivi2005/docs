**Post-Quantum Cryptography (PQC) Key Management API**

Provides generation, management, storage, rotation, and lifecycle management of Post-Quantum Cryptographic (PQC) keys. The API supports post-quantum, classical, hybrid, and symmetric cryptographic algorithms for secure signing, verification, encryption, decryption, encapsulation, and decapsulation operations.

**Table of Contents**

*   General API Information
*   Authentication
*   Supported Algorithms
*   Supported Operations
*   API
    *   Get PQC Keys
    *   Create PQC Key
    *   Update PQC Key
    *   Rotate PQC Key
*   PQC Key Object
*   Versioning
*   Key Rotation
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   All endpoints require authentication.
*   PQC keys are managed per user account.
*   Private key material is encrypted before storage.
*   Hybrid algorithms combine classical and post-quantum cryptography.
*   Key rotation is supported through a dedicated endpoint.
*   Every PQC key operation is recorded in the audit log system.

**Authentication**

All PQC Key Management endpoints require a valid JWT access token.

Example:

Authorization: Bearer <access\_token>

**Supported Algorithms**

| Algorithm | Description |
| --- | --- |
| ML-DSA | Post-Quantum Digital Signature Algorithm |
| ML-KEM | Post-Quantum Key Encapsulation Mechanism |
| ECDSA | Classical Elliptic Curve Digital Signature Algorithm |
| Hybrid-DSA | ML-DSA + ECDSA Hybrid Signature Algorithm |
| Hybrid-KEM | ML-KEM + X25519 Hybrid Key Exchange |
| AES-256 | Symmetric Encryption Key |

**Supported Operations**

Operations are automatically assigned based on the selected algorithm.

| Algorithm | Supported Operations |
| --- | --- |
| ML-DSA | Sign, Verify |
| ECDSA | Sign, Verify |
| Hybrid-DSA | Sign, Verify |
| ML-KEM | Encapsulate, Decapsulate |
| Hybrid-KEM | Encapsulate, Decapsulate |
| AES-256 | Encrypt, Decrypt |

**API**

**Get PQC Keys**

Retrieve all PQC keys associated with the authenticated user.

**Request**

GET /api/pqc-keys

**Success Response**

\[

{

"id": "550e8400-e29b-41d4-a716-446655440000",

"name": "Production PQC Key",

"algorithm": "ML-DSA",

"parameters": "ML-DSA-65",

"operations": "Sign · Verify",

"operationsObj": {

"sign": true,

"verify": true,

"encrypt": false,

"decrypt": false,

"encapsulate": false,

"decapsulate": false

},

"environment": "Production",

"status": "active",

"version": 1,

"publicKeyClassical": null,

"symmetricKey": null,

"created": "2025-06-05T10:15:30.000Z",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

\]

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | PQC keys retrieved successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to retrieve PQC keys |

**Create PQC Key**

Generate and create a new PQC key.

**Request**

POST /api/pqc-keys

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| name | String | Required | PQC key name |
| algorithm | String | Required | Cryptographic algorithm |
| environment | String | Optional | Development or Production |

**Example Request**

{

"name": "Production PQC Key",

"algorithm": "ML-DSA",

"environment": "Production"

}

**Example Request (Hybrid)**

{

"name": "Hybrid Signing Key",

"algorithm": "Hybrid-DSA",

"environment": "Production"

}

**Example Request (AES-256)**

{

"name": "Data Encryption Key",

"algorithm": "AES-256"

}

**Success Response**

{

"id": "550e8400-e29b-41d4-a716-446655440000",

"name": "Production PQC Key",

"algorithm": "ML-DSA",

"parameters": "ML-DSA-65",

"operations": "Sign · Verify",

"environment": "Production",

"status": "active",

"version": 1

}

**Validation Rules**

| Field | Rule |
| --- | --- |
| name | Must not be empty |
| algorithm | Must be one of the supported algorithms |

**Success Response**

| Status Code | Description |
| --- | --- |
| 201 Created | PQC key created successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Invalid algorithm or duplicate active key name |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to generate PQC key |

**Update PQC Key**

Update an existing PQC key.

**Request**

PUT /api/pqc-keys/{id}

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| name | String | Optional | Updated key name |
| status | String | Optional | Updated key status |

**Example Request**

{

"name": "Updated Production Key"

}

**Example Request (Status Update)**

{

"status": "disabled"

}

**Allowed Status Values**

| Status |
| --- |
| active |
| rotated |
| disabled |

**Success Response**

{

"id": "550e8400-e29b-41d4-a716-446655440000",

"name": "Updated Production Key",

"algorithm": "ML-DSA",

"status": "disabled",

"version": 1

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | PQC key updated successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Invalid update parameters |
| 401 Unauthorized | Invalid or missing authentication token |
| 404 Not Found | PQC key not found |
| 500 Internal Server Error | Failed to update PQC key |

**Rotate PQC Key**

Rotate an existing PQC key by creating a new version while preserving the same configuration.

**Request**

POST /api/pqc-keys/{id}/rotate

**Description**

Key rotation performs the following actions:

1.  Marks the existing key as rotated.
2.  Generates a new cryptographic key pair.
3.  Creates a new active key version.
4.  Updates associated policies.
5.  Records an audit log entry.

**Success Response**

{

"id": "660e8400-e29b-41d4-a716-446655440001",

"name": "Production PQC Key",

"algorithm": "ML-DSA",

"status": "active",

"version": 2

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | PQC key rotated successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 401 Unauthorized | Invalid or missing authentication token |
| 404 Not Found | PQC key not found |
| 500 Internal Server Error | Failed to rotate PQC key |

**PQC Key Object**

A PQC key is represented using the following structure:

{

"id": "uuid",

"name": "Production PQC Key",

"algorithm": "ML-DSA",

"parameters": "ML-DSA-65",

"operations": "Sign · Verify",

"environment": "Production",

"status": "active",

"version": 1,

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

**Versioning**

Each PQC key contains a version number.

| Version | Description |
| --- | --- |
| 1 | Original key |
| 2+ | Rotated key versions |

Every successful rotation increments the version number automatically.

**Key Rotation**

Key rotation enables cryptographic agility and long-term security.

During rotation:

*   Existing keys are marked as rotated.
*   New key material is generated.
*   Associated policy references are updated automatically.
*   Audit log records are created.
*   Previous versions remain available for historical reference.

**Security Notes**

*   Private keys are encrypted before storage.
*   Operations are automatically assigned based on algorithm selection.
*   Users cannot manually modify algorithm-specific operations.
*   Duplicate active key names are prevented.
*   Hybrid algorithms provide classical and post-quantum protection simultaneously.
*   AES-256 keys are generated for symmetric encryption operations.
*   Key rotation supports cryptographic lifecycle management.
*   All key creation, update, and rotation events are logged.
*   Policy references are automatically synchronized during key updates and rotations.