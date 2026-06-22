# API Authentication Guide
**Document Type:** Technical Documentation
**Version:** 2.3.1
**Last Updated:** 2024-01-15

## Overview
TechDocs API uses OAuth 2.0 with JWT tokens for authentication. All API requests must include a valid Bearer token in the Authorization header.

## Authentication Flow

### Step 1: Obtain Access Token
Send a POST request to the token endpoint:
```
POST /auth/token
Content-Type: application/json

{
  "client_id": "your_client_id",
  "client_secret": "your_client_secret",
  "grant_type": "client_credentials"
}
```

### Step 2: Use Token in Requests
```
GET /api/v2/documents
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Error Codes
| Code | Message | Resolution |
|------|---------|------------|
| ERR_AUTH_001 | Invalid credentials | Check client_id and client_secret |
| ERR_AUTH_002 | Token expired | Request a new token using refresh flow |
| ERR_AUTH_003 | Insufficient scope | Request additional permissions |
| ERR_AUTH_004 | Rate limit exceeded | Wait 60 seconds before retry |

## Token Expiry
Access tokens expire after 3600 seconds (1 hour). Refresh tokens are valid for 30 days.

## Security Best Practices
- Never store tokens in client-side code or version control
- Rotate client secrets every 90 days
- Use environment variables for credentials
- Always use HTTPS endpoints
