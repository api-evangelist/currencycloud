---
name: Convert currency and pay a beneficiary
description: Authenticate, check a rate, convert funds, create a beneficiary, and send a cross-border payment with Currencycloud.
api: openapi/currencycloud-openapi-original.yml
operations: [Login, GetDetailedRates, CreateConversion, CreateBeneficiary, CreatePayment, GetPaymentTrackingInfo]
---

# Convert currency and pay a beneficiary

Use the Currencycloud API v2 to turn a balance in one currency into a payment in another.

## Environment
- Demo: `https://devapi.currencycloud.com/v2` (does not execute real payments)
- Production: `https://api.currencycloud.com/v2`

## Steps

1. **Authenticate** — `Login` (`POST /authenticate/api`) with form-data `login_id` and `api_key`. Read `auth_token` from the response and send it as the `X-Auth-Token` header on every subsequent call. Tokens expire after 30 minutes of inactivity (HTTP 401 `auth_failed` → re-authenticate).
2. **Check the rate** — `GetDetailedRates` (`GET /rates/detailed`) for the currency pair and amount so you know the cost before committing.
3. **Convert** — `CreateConversion` (`POST /conversions/create`) to buy the sell currency. Pass a client-generated `unique_request_id` so a retry never double-converts (idempotency).
4. **Create the beneficiary** — `CreateBeneficiary` (`POST /beneficiaries/create`) for the recipient. Optionally pre-check with `ValidateBeneficiary` (`POST /beneficiaries/validate`).
5. **Pay** — `CreatePayment` (`POST /payments/create`) referencing the beneficiary and the converted funds. Again pass `unique_request_id` for idempotency. Optionally dry-run with `ValidatePayment` first.
6. **Track** — `GetPaymentTrackingInfo` (`GET /payments/{id}/tracking_info`) to follow the payment; subscribe to Payment push notifications for status changes.

## Conventions & error handling
- Errors use `{ error_code, error_messages: { <param>: [ { code, message, params } ] } }`. See `errors/currencycloud-error-codes.yml`.
- Handle HTTP 429 `too_many_requests` with exponential backoff + jitter, then retry after 60s. See `rate-limits/currencycloud-rate-limits.yml`.
- House accounts can act for sub-accounts via `on_behalf_of`.
