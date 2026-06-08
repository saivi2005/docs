**Policy Management API**

Provides creation, management, enforcement, and lifecycle control of security policies. Policies define how Authentication Keys and Post-Quantum Cryptography (PQC) Keys are associated, what operations are permitted, and what rate limits apply to cryptographic operations within the platform.

**Table of Contents**

*   General API Information
*   Authentication
*   Policy Components
*   API
    *   Get Policies
    *   Create Policy
    *   Update Policy
*   Policy Object
*   Policy Validation Rules
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   All endpoints require authentication.
*   Policies are managed per user account.
*   Policies can be linked to Authentication Keys and PQC Keys.
*   Only active Authentication Keys and PQC Keys can be assigned to a policy.
*   A unique policy must exist for each Authentication Key and PQC Key combination.
*   All policy operations are recorded in the audit logging system.

**Authentication**

All Policy Management endpoints require a valid JWT access token.

Example:

Authorization: Bearer <access\_token>

**Policy Components**

A policy may contain the following components:

| Component | Description |
| --- | --- |
| Authentication Key | Classical authentication key associated with the policy |
| PQC Key | Post-Quantum Cryptographic key associated with the policy |
| Operations | Allowed cryptographic operations |
| Rate Limit | Allowed request rate configuration |
| Status | Current policy state |

**API**

**Get Policies**

Retrieve all policies associated with the authenticated user.

**Request**

GET /api/policies

**Success Response**

\[

{

"id": "550e8400-e29b-41d4-a716-446655440000",

"name": "Production Security Policy",

"authKey": "Primary Signing Key",

"authKeyId": "a100e8400-e29b-41d4-a716-446655440001",

"pqcKey": "Production PQC Key",

"pqcKeyId": "b200e8400-e29b-41d4-a716-446655440002",

"operations": "Sign · Verify",

"rateLimit": "1000/hour",

"status": "active",

"created": "2025-06-05T10:15:30.000Z",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

\]

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| id | String (UUID) | Unique policy identifier |
| name | String | Policy name |
| authKey | String | Authentication key name |
| authKeyId | String (UUID) | Authentication key identifier |
| pqcKey | String | PQC key name |
| pqcKeyId | String (UUID) | PQC key identifier |
| operations | String | Allowed operations |
| rateLimit | String | Configured rate limit |
| status | String | Policy status |
| created | String | Creation timestamp |
| createdAt | String | Database creation timestamp |
| updatedAt | String | Last modification timestamp |

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Policies retrieved successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to retrieve policies |

**Create Policy**

Create a new policy and associate it with Authentication Keys and PQC Keys.

**Request**

POST /api/policies

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| name | String | Required | Policy name |
| authKeyId | String (UUID) | Optional | Authentication Key identifier |
| pqcKeyId | String (UUID) | Optional | PQC Key identifier |
| operations | String | Optional | Allowed operations |
| rateLimit | String | Optional | Rate limit configuration |
| status | String | Optional | Policy status |

**Example Request**

{

"name": "Production Security Policy",

"authKeyId": "a100e8400-e29b-41d4-a716-446655440001",

"pqcKeyId": "b200e8400-e29b-41d4-a716-446655440002",

"operations": "Sign · Verify",

"rateLimit": "1000/hour",

"status": "active"

}

**Success Response**

{

"id": "550e8400-e29b-41d4-a716-446655440000",

"name": "Production Security Policy",

"authKey": "Primary Signing Key",

"authKeyId": "a100e8400-e29b-41d4-a716-446655440001",

"pqcKey": "Production PQC Key",

"pqcKeyId": "b200e8400-e29b-41d4-a716-446655440002",

"operations": "Sign · Verify",

"rateLimit": "1000/hour",

"status": "active"

}

**Validation Rules**

| Rule | Description |
| --- | --- |
| Policy Name Required | Policy name cannot be empty |
| Active PQC Key Required | Linked PQC key must be active |
| Active Authentication Key Required | Linked Authentication key must be active |
| Unique Key Pair | Only one policy may exist for a specific Authentication Key and PQC Key combination |

**Success Response**

| Status Code | Description |
| --- | --- |
| 201 Created | Policy created successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Invalid PQC Key ID |
| 400 Bad Request | Invalid Authentication Key ID |
| 400 Bad Request | Linked key is not active |
| 400 Bad Request | Policy already exists for selected key combination |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to create policy |

**Update Policy**

Update an existing policy.

**Request**

PUT /api/policies/{id}

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| name | String | Optional | Updated policy name |
| authKeyId | String (UUID) | Optional | Updated Authentication Key |
| pqcKeyId | String (UUID) | Optional | Updated PQC Key |
| operations | String | Optional | Updated allowed operations |
| rateLimit | String | Optional | Updated rate limit |
| status | String | Optional | Updated status |

**Example Request**

{

"name": "Updated Production Policy",

"status": "disabled"

}

**Allowed Status Values**

| Status |
| --- |
| active |
| disabled |

**Success Response**

{

"id": "550e8400-e29b-41d4-a716-446655440000",

"name": "Updated Production Policy",

"authKey": "Primary Signing Key",

"pqcKey": "Production PQC Key",

"status": "disabled"

}

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Policy updated successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Invalid Authentication Key ID |
| 400 Bad Request | Invalid PQC Key ID |
| 400 Bad Request | Linked key is not active |
| 400 Bad Request | Duplicate Authentication Key and PQC Key combination |
| 401 Unauthorized | Invalid or missing authentication token |
| 404 Not Found | Policy not found |
| 500 Internal Server Error | Failed to update policy |

**Policy Object**

A policy is represented using the following structure:

{

"id": "uuid",

"name": "Production Security Policy",

"authKey": "Primary Signing Key",

"authKeyId": "uuid",

"pqcKey": "Production PQC Key",

"pqcKeyId": "uuid",

"operations": "Sign · Verify",

"rateLimit": "1000/hour",

"status": "active",

"createdAt": "2025-06-05T10:15:30.000Z",

"updatedAt": "2025-06-05T10:15:30.000Z"

}

**Policy Validation Rules**

The following validation checks are enforced before a policy is created or updated:

1.  Policy name must be provided.
2.  Authentication Key must belong to the authenticated user.
3.  PQC Key must belong to the authenticated user.
4.  Only active Authentication Keys can be linked.
5.  Only active PQC Keys can be linked.
6.  A unique policy must exist for every Authentication Key and PQC Key combination.
7.  Key references are automatically synchronized with linked key names.

**Security Notes**

*   Policies are isolated per user account.
*   Policies cannot reference keys owned by other users.
*   Disabled keys cannot be attached to policies.
*   Policy updates automatically validate linked key ownership.
*   Duplicate key combinations are prevented.
*   Policy creation and modification events generate audit log entries.
*   Policy references remain synchronized with Authentication Key and PQC Key names.
*   Policy status changes are recorded in audit logs.