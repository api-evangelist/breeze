---
name: breeze-withdraw-yield
description: Withdraw a user's funds from a Breeze Solana yield strategy and reconcile their remaining balance and earned yield.
api: openapi/breeze-openapi-original.json
operations:
- get_user_balances
- process_for_withdraw_with_transaction
- get_user_yield
generated: '2026-07-18'
method: generated
---

# Withdraw Breeze yield

Redeem a user's position from a Breeze fund and confirm the resulting balance.

## Auth
Header `x-api-key: <BREEZE_API_KEY>` on every request.

## Steps
1. **Read the current position** — call `get_user_balances` (`GET /user-balances/{fund_user}`) to get the withdrawable position value (raw base units) and `get_user_yield` (`GET /user-yield/{fund_user}`) for earned yield.
2. **Build the withdrawal** — call `process_for_withdraw_with_transaction` (`POST /withdraw/tx`) with the user pubkey, strategy/fund, mint, and amount (or a full-withdraw flag per the request schema). The response is a base64 versioned unsigned transaction. Use `/withdraw/ix` for raw instructions if you need to compose the transaction yourself.
3. **Sign & submit** — have the user's wallet sign the base64 transaction and submit it to Solana.
4. **Reconcile** — re-call `get_user_balances` to confirm the reduced/closed position.

## Rules
- Amounts are raw base units — do not apply decimal scaling twice.
- Errors return `{"message": "..."}` with the class carried by HTTP status.
- Cross-reference `conventions/breeze-conventions.yml` and `errors/breeze-problem-types.yml`.
