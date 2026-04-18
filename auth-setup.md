# EasyBilling Auth Setup

## Environment Configuration

The customer's app needs these environment variables:

```
EASYBILLING_BASE_URL=https://<tenant>.easybilling.cloud
EASYBILLING_USERNAME=user@easybilling.cloud
EASYBILLING_PASSWORD=<password>
```

Store credentials in a secret manager or `.env` file (never commit to git).

## Login

```http
POST /api/authenticate/login
Content-Type: application/json

{
  "username": "user@easybilling.cloud",
  "password": "12345678"
}
```

Response (200):
```json
{
  "message": "Authentication successful",
  "token": "eyJhbGciOiJSUzI1NiJ9..."
}
```

Store the `token` value. Include it on **all subsequent requests**:

```
Authorization: Bearer <token>
```

## Optional Headers

| Header | Purpose |
|--------|---------|
| `trace-id` | Debugging — correlate requests in EasyBilling logs |

## Token Refresh Strategy

EasyBilling uses JWT tokens. When you receive a `401 Unauthorized` response:
1. Call the login endpoint again to get a fresh token
2. Retry the failed request with the new token

Implement this as middleware/interceptor in your HTTP client.

## Logout

```http
POST /api/authenticate/logout
Authorization: Bearer <token>
```

Call on application shutdown or session end.
