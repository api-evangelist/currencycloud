---
name: Onboard a sub-account and contact
description: Create a white-label sub-account and its primary contact under a Currencycloud house account.
api: openapi/currencycloud-openapi-original.yml
operations: [Login, CreateAccount, CreateContact]
---

# Onboard a sub-account and contact

Currencycloud lets a house account register sub-accounts to offer white-label money-transfer services to end customers.

## Steps

1. **Authenticate** — `Login` (`POST /authenticate/api`); send the returned `auth_token` as `X-Auth-Token`.
2. **Create the sub-account** — `CreateAccount` (`POST /accounts/create`) with the customer's legal/company details. The response returns the new `account` id.
3. **Create the primary contact** — `CreateContact` (`POST /contacts/create`) with `account_id` set to the sub-account id, plus the contact's name, email and login details.
4. Thereafter, act on behalf of the sub-account by passing `on_behalf_of` (the sub-account contact id) on payment/conversion/transfer operations.

## Conventions & error handling
- Validation failures return HTTP 400 with per-field reasons in `error_messages`. See `errors/currencycloud-error-codes.yml`.
- Onboarding events can trigger push notifications. See `asyncapi/currencycloud-webhooks.yml`.
