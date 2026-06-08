**Authentication & Security API**

Provides authentication, user profile management, Two-Factor Authentication (2FA), IP whitelist management, and Mutual TLS (mTLS) certificate operations.

**Table of Contents**

*   General API Information
*   Authentication
*   Rate Limits
*   API
    *   Google OAuth Login
    *   Verify Two-Factor Authentication Login
    *   Get User Profile
    *   Setup Two-Factor Authentication
    *   Verify Two-Factor Authentication Setup
    *   Get Two-Factor Authentication Status
    *   Disable Two-Factor Authentication
    *   Get IP Whitelist Settings
    *   Update IP Whitelist Settings
    *   Get MTLS Settings
    *   Update MTLS Settings
    *   Issue Certificate
    *   Get Active Certificates
    *   Get Revoked Certificates
    *   Revoke Certificate
    *   Download Certificates
    *   Verify Certificate

[**General API Information**]

*   The base endpoint depends on the application deployment environment.
*   All endpoints return JSON responses.
*   Authentication is handled using JWT Bearer Tokens.
*   Google OAuth is used as the primary login provider.
*   Two-Factor Authentication (2FA) is supported using TOTP-compatible authenticator applications.
*   IP Whitelisting can be configured to restrict account access.
*   Mutual TLS (mTLS) certificates can be issued, managed, revoked, downloaded, and verified through the API.

**Authentication**

Protected endpoints require a valid JWT access token.

Example:

Authorization: Bearer <access\_token>

Public Endpoints:

POST /api/auth/google

POST /api/auth/2fa/verify

All other endpoints require authentication.

**Rate Limits**

Authentication endpoints may be subject to rate limiting to prevent abuse, brute-force attacks, and unauthorized access attempts.

**API**

**Google OAuth Login**

Authenticate a user using a Google OAuth authorization code.

**Request**

POST /api/auth/google

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| code | String | Required | Google OAuth authorization code |

**Example Request**

{

"code": "4/0AX4XfWhExampleGoogleAuthorizationCode"

}

**Success Response**

{

"requires2FA": false,

"token": "jwt\_token",

"user": {

"name": "John Doe",

"email": "john@example.com",

"avatar": "https://example.com/avatar.jpg",

"id": "109876543210"

}

}

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| requires2FA | Boolean | Indicates whether 2FA verification is required |
| token | String | JWT access token |
| user | Object | User profile information |

**Error Responses**

| Status Code | Description |
| --- | --- |
| 400 Bad Request | Authorization code is missing |
| 401 Unauthorized | Invalid Google authorization code |
| 500 Internal Server Error | Unexpected server error |

**Verify Two-Factor Authentication Login**

Verify a TOTP code during login when 2FA is enabled.

**Request**

POST /api/auth/2fa/verify

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| tempToken | String | Required | Temporary authentication token |
| code | String | Required | Six-digit authenticator code |

**Example Request**

{

"tempToken": "temporary\_token",

"code": "123456"

}

**Success Response**

{

"token": "jwt\_token",

"user": {

"name": "John Doe",

"email": "john@example.com",

"avatar": "https://example.com/avatar.jpg",

"id": "109876543210"

}

}

**Get User Profile**

Retrieve profile information of the currently authenticated user.

**Request**

GET /api/auth/me

**Success Response**

{

"name": "John Doe",

"email": "john@example.com",

"avatar": "https://example.com/avatar.jpg",

"id": "109876543210",

"twoFactorEnabled": true

}

**Setup Two-Factor Authentication**

Generate a TOTP secret and QR code information for enabling 2FA.

**Request**

POST /api/auth/2fa/setup

**Success Response**

{

"secret": "JBSWY3DPEHPK3PXP",

"otpauthUri": "otpauth://totp/...",

"qrData": "otpauth://totp/..."

}

**Verify Two-Factor Authentication Setup**

Verify the generated secret and enable 2FA.

**Request**

POST /api/auth/2fa/verify-setup

**Parameters**

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| code | String | Required | Six-digit authenticator code |
| secret | String | Required | Generated secret |

**Success Response**

{

"enabled": true

}

**Get Two-Factor Authentication Status**

**Request**

GET /api/auth/2fa/status

**Success Response**

{

"enabled": true

}

**Disable Two-Factor Authentication**

**Request**

DELETE /api/auth/2fa

**Success Response**

{

"enabled": false

}

**Get IP Whitelist Settings**

**Request**

GET /api/auth/settings/ip-whitelist

**Success Response**

{

"mode": "selected",

"whitelist": \[

"192.168.1.10",

"10.0.0.0/24"

\]

}

**Update IP Whitelist Settings**

**Request**

PUT /api/auth/settings/ip-whitelist

**Success Response**

{

"mode": "selected",

"whitelist": \[

"192.168.1.10",

"10.0.0.0/24"

\]

}

**Get MTLS Settings**

**Request**

GET /api/auth/mtls/settings

**Success Response**

{

"mtlsMode": "mtls",

"mtlsEnabled": true,

"certificateCount": 2,

"certificates": \[\]

}

**Update MTLS Settings**

**Request**

PUT /api/auth/mtls/settings

**Success Response**

{

"mtlsMode": "mtls",

"mtlsEnabled": true

}

**Issue Certificate**

**Request**

POST /api/auth/mtls/issue

**Success Response**

{

"success": true,

"certificate": {

"id": 1,

"certificateName": "Production Client",

"serialNumber": "0xA1B2C3D4",

"fingerprint": "SHA256\_HASH"

}

}

**Get Active Certificates**

**Request**

GET /api/auth/mtls/certificates

**Success Response**

{

"certificates": \[\],

"count": 0

}

**Get Revoked Certificates**

**Request**

GET /api/auth/mtls/certificates/revoked

**Success Response**

{

"certificates": \[\],

"count": 0

}

**Revoke Certificate**

**Request**

DELETE /api/auth/mtls/certificates/{certificateId}

**Success Response**

{

"success": true,

"message": "Certificate revoked successfully"

}

**Download Certificates**

**Request**

GET /api/auth/mtls/download/{certificateId}

**Success Response**

Returns the certificate bundle, including:

*   Client Certificate
*   CA Certificate
*   Server Certificate

**Verify Certificate**

**Request**

POST /api/auth/mtls/verify

**Success Response**

{

"valid": true,

"userId": 1,

"certificateId": 1,

"certificateName": "Production Client"

}

**Failed Verification Response**

{

"valid": false,

"error": "Certificate has been revoked"

}
