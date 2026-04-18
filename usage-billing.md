# EasyBilling Usage-Based Billing

Metered/consumption-based billing. Prerequisite: Account + Contract must already exist (see subscription-billing.md steps 1-2).

## Usage Event Structure

Each event represents one unit of consumption:

```json
{
  "eventId": "evt-unique-id-001",
  "schemaName": "API-Calls",
  "eventTime": "2026-05-15T14:30:00Z",
  "accountNumber": "ACC-ACME-001",
  "attributes": [
    { "name": "endpoint", "value": "/v1/chat" },
    { "name": "tokens", "value": "1500" }
  ]
}
```

| Field | Description |
|-------|-------------|
| `eventId` | Unique ID — use for idempotency. Duplicate eventIds are rejected. |
| `schemaName` | Must match a usage event schema configured in EasyBilling |
| `eventTime` | ISO 8601 timestamp of when the usage occurred |
| `accountNumber` | The customer account consuming the resource |
| `attributes` | Name/value pairs matching the schema definition |

## Ingesting Events

```http
POST /api/usage-events
Content-Type: application/json
Authorization: Bearer <token>

[
  { "eventId": "evt-001", "schemaName": "API-Calls", "eventTime": "2026-05-15T14:30:00Z", "accountNumber": "ACC-ACME-001", "attributes": [{ "name": "tokens", "value": "1500" }] },
  { "eventId": "evt-002", "schemaName": "API-Calls", "eventTime": "2026-05-15T14:31:00Z", "accountNumber": "ACC-ACME-001", "attributes": [{ "name": "tokens", "value": "800" }] }
]
```

**Constraints:**
- **Max 50 events per request**
- **Atomic:** all succeed or all fail — no partial ingestion

**Response (200):**
```json
{
  "success": true,
  "successDetails": [{ "eventId": "evt-001" }, { "eventId": "evt-002" }],
  "errorDetails": []
}
```

On failure, `success: false` and `errorDetails` contains per-event error messages. Check `errorDetails` even on 200 responses.

## Invoices

Usage events are automatically aggregated into invoices by the billing engine based on the contract's billing cycle. Query invoices via `GET /api/invoices?accountNumber=...` (see subscription-billing.md).
