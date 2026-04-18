# EasyBilling Prepaid Credits

Bucket-based credit management. Prerequisite: Account must exist (see subscription-billing.md step 1).

## Creating a Credit Bucket

Use contract actions with type `create-contract-with-plan-item`:

```http
POST /api/contract-actions
Content-Type: application/json
Authorization: Bearer <token>

{
  "accountNumber": "ACC-ACME-001",
  "actions": [
    {
      "type": "create-contract-with-plan-item",
      "createContractWithPlanItem": [
        {
          "planItemId": "<plan-item-uuid>",
          "effectiveDate": "2026-05-01",
          "currency": ["USD"]
        }
      ]
    }
  ]
}
```

**Important:** Use `create-contract-with-plan-item`, NOT `create-contract-with-plan`. The plan item type determines the bucket type.

## Bucket Types

| Type | UoM | Use case |
|------|-----|----------|
| `resource-based-credit` | Custom unit (e.g., "Token") | API calls, compute hours, storage units |
| `currency-based-credit` | Currency code (e.g., "USD") | Dollar-denominated credits |

## Querying Buckets

```http
GET /api/buckets?accountNumber=ACC-ACME-001
Authorization: Bearer <token>
```

Response:
```json
{
  "id": "196c4a3c-...",
  "accountId": "cebe6630-...",
  "totalBalance": 20000,
  "remainingBalance": 17500,
  "uom": "Token",
  "cost": 200,
  "validFrom": "2026-05-01",
  "validTo": "2026-06-01",
  "type": "resource-based-credit",
  "status": "active"
}
```

Key fields: `totalBalance` (original), `remainingBalance` (current), `validFrom`/`validTo` (expiry window), `cost` (monetary value of the credits).
