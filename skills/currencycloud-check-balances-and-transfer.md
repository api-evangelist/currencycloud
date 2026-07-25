---
name: Check balances and transfer between accounts
description: Authenticate, review multi-currency balances, and move funds between Currencycloud accounts.
api: openapi/currencycloud-openapi-original.yml
operations: [Login, FindBalances, GetBalance, CreateTransfer]
---

# Check balances and transfer between accounts

## Steps

1. **Authenticate** — `Login` (`POST /authenticate/api`); send `auth_token` as `X-Auth-Token`.
2. **List balances** — `FindBalances` (`GET /balances/find`) to page through the currencies you hold, or `GetBalance` (`GET /balances/{currency}`) for a single currency.
3. **Transfer** — `CreateTransfer` (`POST /transfers/create`) to move funds between your house account and a sub-account (or between accounts you control). Pass `unique_request_id` for idempotency so a retried transfer is not duplicated.

## Conventions & error handling
- Paginate with `page` / `per_page` / `order`; the `pagination` object carries `total_pages` and `next_page`. See `conventions/currencycloud-conventions.yml`.
- Errors use the `{ error_code, error_messages }` envelope. See `errors/currencycloud-problem-types.yml`.
- Transfer status changes emit push notifications. See `asyncapi/currencycloud-webhooks.yml`.
