**Dashboard API**

Provides dashboard statistics and summary metrics associated with the authenticated user. The Dashboard API aggregates information from Authentication Keys, Post-Quantum Cryptography (PQC) Keys, Policies, and Audit Logs to provide a high-level overview of platform activity and resource utilization.

**Table of Contents**

*   General API Information
*   Authentication
*   Dashboard Metrics
*   API
    *   Get Dashboard Statistics
*   Dashboard Statistics Object
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   All endpoints require authentication.
*   Dashboard statistics are calculated dynamically.
*   Statistics are generated using user-specific resources and audit records.
*   Dashboard data provides an overview of cryptographic assets and platform activity.

**Authentication**

All Dashboard API endpoints require a valid JWT access token.

Example:

Authorization: Bearer <access\_token>

**Dashboard Metrics**

The Dashboard API aggregates the following information:

| Metric | Description |
| --- | --- |
| PQC Keys | Total number of PQC Keys owned by the user |
| Authentication Keys | Total number of Authentication Keys owned by the user |
| Policies | Total number of Policies owned by the user |
| Total Operations | Total number of audit log entries recorded |
| Successful Events | Total number of successful audit log events |

**API**

**Get Dashboard Statistics**

Retrieve aggregated dashboard statistics for the authenticated user.

**Request**

GET /api/dashboard/stats

**Headers**

| Name | Mandatory | Description |
| --- | --- | --- |
| Authorization | Required | JWT access token |

**Parameters**

This endpoint does not require path parameters, query parameters, or request body data.

**Success Response**

{

"pqcKeys": 8,

"authKeys": 4,

"policies": 6,

"totalOperations": 245,

"successfulEvents": 231

}

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| pqcKeys | Integer | Total number of PQC Keys |
| authKeys | Integer | Total number of Authentication Keys |
| policies | Integer | Total number of Policies |
| totalOperations | Integer | Total number of recorded audit log events |
| successfulEvents | Integer | Total number of successful audit log events |

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Dashboard statistics retrieved successfully |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 401 Unauthorized | Invalid or missing authentication token |
| 500 Internal Server Error | Failed to retrieve dashboard statistics |

**Dashboard Statistics Object**

A dashboard statistics record is represented using the following structure:

{

"pqcKeys": 8,

"authKeys": 4,

"policies": 6,

"totalOperations": 245,

"successfulEvents": 231

}

**Field Definitions**

| Field | Description |
| --- | --- |
| pqcKeys | Count of PQC Keys associated with the authenticated user |
| authKeys | Count of Authentication Keys associated with the authenticated user |
| policies | Count of Policies associated with the authenticated user |
| totalOperations | Count of audit log records associated with the authenticated user |
| successfulEvents | Count of audit log records with a success result |

**Security Notes**

*   Dashboard statistics are isolated per user account.
*   Users can only access their own dashboard data.
*   Statistics are generated from live platform records.
*   Dashboard data includes counts only and does not expose private cryptographic material.
*   Authentication is required for all dashboard operations.
*   Dashboard statistics may be used for monitoring platform usage and activity trends.
*   Audit log records contribute directly to operational statistics.
*   All calculations are performed server-side before being returned to the client.
