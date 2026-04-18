# EasyBilling Payment Collection

PaymentHub integration for collecting payments. Works independently of billing — link payments to billing via contract actions with `payImmediately: true`.

## Supported Gateways

| Gateway | Type value | Notes |
|---------|-----------|-------|
| Stripe | `stripe-connect` | `customerIdentifier` must be email. Redirect URLs must be HTTPS. |
| Alipay | `alipay-page` | `paymentUrl` opens Alipay payment page directly. |
| WeChat Pay | `wechat-pay-native` | `paymentUrl` is a QR code link — render as QR for user to scan. |

## Setting Up a Payment Method

```http
POST /api/payment-methods
Content-Type: application/json
Authorization: Bearer <token>

{
  "customerIdentifier": "billing@acme.com",
  "paymentGatewayType": "stripe-connect",
  "redirectUrl": "https://app.acme.com/payment/success",
  "cancelUrl": "https://app.acme.com/payment/cancel"
}
```

## Creating a Payment Transaction

```http
POST /api/transactions
Content-Type: application/json
Authorization: Bearer <token>

{
  "transactionId": "order-12345",
  "description": "Monthly subscription",
  "amount": "9900",
  "currency": "USD",
  "date": "2026-05-01",
  "customerIdentifier": "billing@acme.com",
  "paymentGatewayType": "stripe-connect",
  "completePayRedirectUrl": "https://app.acme.com/payment/complete",
  "cancelPayRedirectUrl": "https://app.acme.com/payment/cancel",
  "onlineScenario": true
}
```

Response:
```json
{
  "paymentUrl": "https://checkout.stripe.com/...",
  "paymentId": "pay-uuid",
  "expireTime": 1800,
  "status": "waiting"
}
```

Redirect the user to `paymentUrl` to complete payment. For WeChat, render `paymentUrl` as a QR code.

## Contract Actions with Immediate Payment

Add `payImmediately` and `paymentInfo` to any contract action:

```json
{
  "accountNumber": "ACC-ACME-001",
  "actions": [{
    "type": "create-contract-with-plan",
    "createContractWithPlan": [{ "planId": "...", "effectiveDate": "...", "expirationDate": "...", "currency": ["USD"] }],
    "payImmediately": true,
    "paymentInfo": {
      "paymentGatewayType": "stripe-connect",
      "paymentSuccessUrl": "https://app.acme.com/success",
      "paymentCancelUrl": "https://app.acme.com/cancel"
    }
  }]
}
```

## Checking Payment Status

```http
GET /transactions/<transactionId>/status
```

Statuses: `pending` → `processing` → `succeeded` / `failed` / `expired` / `closed`

To force a status refresh from the gateway:
```http
POST /api/transactions/<transactionId>/status
```

## Refunds

```http
POST /api/refund
Content-Type: application/json
Authorization: Bearer <token>

{
  "transactionId": "order-12345",
  "refundTransactionId": "refund-order-12345"
}
```

Query refund status: `GET /api/refund?transactionId=order-12345`

Refund statuses: `created` → `refunding` → `succeeded` / `failed` / `canceled` / `closed`

## Payment History

```http
POST /api/data-query
Content-Type: application/json
Authorization: Bearer <token>

{
  "startDate": "2026-01-01",
  "endDate": "2026-06-01",
  "queryObject": "payment-query-list",
  "filters": [
    { "fieldName": "paymentGatewayType", "fieldValues": ["stripe-connect"] }
  ]
}
```

Filterable fields: `transactionId`, `customerIdentifier`, `paymentGatewayType`, `status`
