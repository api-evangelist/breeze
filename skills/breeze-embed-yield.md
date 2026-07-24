---
name: breeze-embed-yield
description: Embed automated Solana yield into an app — inspect a strategy, deposit a user's funds, and track their position and earned yield through the Breeze REST API.
api: openapi/breeze-openapi-original.json
operations:
- get_strategy
- get_strategy_apy
- process_for_deposit_with_transaction
- get_user_balances
- get_user_yield
generated: '2026-07-18'
method: generated
---

# Embed Breeze yield

Use the Breeze API (`https://api.breeze.baby`) to give a user automated, non-custodial yield on a supported Solana asset (USDC, USDT, USDS, SOL, JitoSOL, mSOL, JupSOL, JLP).

## Auth
Send your API key on every request: header `x-api-key: <BREEZE_API_KEY>` (from portal.breeze.baby). Organization/admin endpoints instead use a JWT `Authorization: Bearer` token.

## Steps
1. **Inspect the strategy** — call `get_strategy` (`GET /strategy/{strategy_id}`) and `get_strategy_apy` (`GET /strategy/apy`) to show supported assets and current APY before the user commits.
2. **Build the deposit** — call `process_for_deposit_with_transaction` (`POST /deposit/tx`) with the user pubkey, strategy/fund, mint, and amount. Amounts are in raw base units (USDC = 6 decimals, SOL = 9 decimals). The response is a base64 versioned unsigned transaction.
3. **Sign & submit** — deserialize the base64 transaction, have the user's wallet sign it, and submit to Solana. (For advanced control use `/deposit/ix` to get raw instructions instead.)
4. **Track the position** — call `get_user_balances` (`GET /user-balances/{fund_user}`) and `get_user_yield` (`GET /user-yield/{fund_user}`) to display position value and yield earned. Both support `page`/`limit` pagination; read the `meta` block (`has_more`).

## Rules
- Never invent token amounts or decimals — read them from the strategy/supported-assets response.
- On error, the body is `{"message": "..."}`; branch on HTTP status (400 bad request, 403 forbidden/auth, 404 not found, 500 server).
- See `conventions/breeze-conventions.yml` for pagination and amount conventions; `errors/breeze-problem-types.yml` for the error envelope.
