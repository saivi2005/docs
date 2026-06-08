**Audit Logs API**

Provides audit log records associated with the authenticated user. Audit logs are used to track security-related and operational activities such as key management, policy management, cryptographic operations, authentication events, and platform activity.

**Table of Contents**

*   General API Information
*   Authentication
*   API
    *   Get Audit Logs
*   Audit Log Object
*   Filtering Rules
*   Sorting Rules
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   All endpoints require authentication.
*   Audit logs are generated automatically by platform services.
*   Audit logs are returned in descending order of creation time (newest first).
*   Audit log records cannot be modified through the API.

**Authentication**

All Audit Log endpoints require a valid JWT access token.

Example:

Authorization: Bearer <access\_token>

**API**

**Get Audit Logs**

Retrieve all audit log entries associated with the authenticated user.

**Request**

GET /api/audit-logs

**Headers**

| Name | Mandatory | Description |
| --- | --- | --- |
| Authorization | Required | JWT access token |

**Parameters**

This endpoint does not require path parameters, query parameters, or request body data.

**Success Response**

\[

{

"id": 101,

"operation": "pqc\_sign",

"authKey": "Primary Signing Key",

"authKeyId": "550e8400-e29b-41d4-a716-446655440001",

"pqcKey": "Production PQC Key",

"pqcKeyId": "660e8400-e29b-41d4-a716-446655440002",

"policyId": "770e8400-e29b-41d4-a716-446655440003",

"sourceIP": "192.168.1.100",

"result": "success",

"createdAt": 1749112345678,

"timestamp": "2025-06-05T10:32:25.678Z"

}

\]

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| id | Integer | Unique audit log identifier |
| operation | String | Operation or event name |
| authKey | String | Associated Authentication Key name |
| authKeyId | String / Null | Authentication Key identifier |
| pqcKey | String | Associated PQC Key name |
| pqcKeyId | String / Null | PQC Key identifier |
| policyId | String / Null | Associated Policy identifier |
| sourceIP | String | Source IP address |
| result | String | Operation result (success or failure) |
| createdAt | Integer | Unix timestamp in milliseconds |
| timestamp | String | ISO-8601 formatted timestamp |

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Audit logs retrieved successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to retrieve audit logs |

**Audit Log Object**

An audit log entry is represented using the following structure:

{

"id": 101,

"operation": "pqc\_sign",

"authKey": "Primary Signing Key",

"authKeyId": "uuid",

"pqcKey": "Production PQC Key",

"pqcKeyId": "uuid",

"policyId": "uuid",

"sourceIP": "192.168.1.100",

"result": "success",

"createdAt": 1749112345678,

"timestamp": "2025-06-05T10:32:25.678Z"

}

**Filtering Rules**

The Audit Logs API applies filtering before returning records.

**Returned Records**

The following records are always returned:

*   Successful audit log entries.
*   Manual audit log entries.
*   Standard platform activity logs.
*   Key management events.
*   Policy management events.
*   Cryptographic operation events.

**Failure Log Filtering**

Failure logs beginning with:

FAILED:

are only returned when related to the following platform modules:

*   /api/pqc-keys
*   /api/auth-keys
*   /api/policies
*   /api/crypto

Failure logs outside these modules are excluded from the response.

**Sorting Rules**

Audit logs are sorted by creation date in descending order.

Order:

Newest → Oldest

Example:

2025-06-05T10:32:25.678Z

2025-06-05T10:14:05.678Z

2025-06-05T09:58:17.000Z

**Security Notes**

*   Audit logs are isolated per user account.
*   Users can only access their own audit records.
*   Audit records are immutable through the API.
*   Authentication Key operations generate audit records.
*   PQC Key operations generate audit records.
*   Policy management events generate audit records.
*   Cryptographic operations generate audit records.
*   IP whitelist enforcement events may generate audit records.
*   MTLS validation events may generate audit records.
*   Audit logs support security monitoring, compliance reporting, and forensic investigations.