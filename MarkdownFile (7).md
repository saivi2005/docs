**Contact API**

Provides a public interface for submitting contact and support requests. The API validates user information and securely forwards the request to an external support service for processing and response management.

**Table of Contents**

*   General API Information
*   Authentication
*   API
    *   Submit Contact Request
*   Contact Request Object
*   Error Responses
*   Security Notes

**General API Information**

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   Contact requests are forwarded to an external support service.
*   Contact request submissions generate audit log records.
*   Contact requests are processed immediately after validation.
*   The API acts as a secure gateway between users and the external support platform.

**Authentication**

The Contact API is publicly accessible.

Authentication is not required.

**API**

**Submit Contact Request**

Submit a contact or support request.

**Request**

POST /api/contact

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| fullName | String | Required | Full name of the requester |
| email | String | Required | Email address of the requester |
| message | String | Optional | Contact request message |
| subject | String | Optional | Request subject |
| phone | String | Optional | Contact phone number |

**Example Request**

{

"fullName": "John Doe",

"email": "john.doe@example.com",

"subject": "Technical Support Request",

"message": "I need assistance with my account.",

"phone": "+1-555-123-4567"

}

**Success Response**

{

"message": "Contact request submitted successfully!",

"data": {

"success": true,

"referenceId": "REQ-2025-001"

}

}

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| message | String | Status message |
| data | Object | Response returned from the external support service |

**Success Response**

| Status Code | Description |
| --- | --- |
| 200 OK | Contact request submitted successfully |

**Contact Request Object**

A contact request may contain the following information:

{

"fullName": "John Doe",

"email": "john.doe@example.com",

"subject": "Technical Support Request",

"message": "I need assistance with my account.",

"phone": "+1-555-123-4567"

}

**Required Fields**

| Field | Required |
| --- | --- |
| fullName | Yes |
| email | Yes |

**Optional Fields**

| Field |
| --- |
| subject |
| message |
| phone |

Additional fields may be accepted and forwarded to the external support service without modification.

**Error Responses**

**Missing Required Fields**

**Response**

{

"error": "Full Name and Email are required"

}

**Status Code**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Required fields are missing |

**External Service Failure**

**Response**

{

"error": "Failed to contact external service"

}

**Status Code**

| Status Code | Description |
| --- | --- |
| 502 Bad Gateway | External support service is unavailable |

**External API Error**

**Response**

{

"error": "External API Error",

"details": {

"message": "Invalid request"

}

}

**Status Code**

| Status Code | Description |
| --- | --- |
| 4xx / 5xx | Error returned by external support service |

**Server Configuration Error**

**Response**

{

"error": "Server configuration error (Missing API URL)"

}

**Status Code**

| Status Code | Description |
| --- | --- |
| 500 Internal Server Error | Contact service configuration is incomplete |

**Security Notes**

*   Contact requests are forwarded using secure API authentication.
*   External API communication uses API Key and Bearer Token authentication.
*   Request payloads are forwarded without modification.
*   Successful submissions generate audit log entries.
*   Sensitive configuration values are stored in environment variables.
*   Contact requests are processed through a centralized external support service.
*   Authentication is not required for submitting contact requests.