# Payment Rail Adapter Contract

Adapters translate approved protocol intents into provider-specific operations. They are execution interfaces, not sources of authority.

## Required operations

- `validateConfiguration()`
- `prepare(intent, approval)`
- `execute(prepared, idempotencyKey)`
- `verifyEvent(headers, body)`
- `getStatus(providerReference)`
- `refund(providerReference, amount?)`

## Rules

- Reject intents without a valid approval.
- Recheck revocation immediately before submission.
- Preserve the protocol idempotency key.
- Never mutate amount, asset, destination, merchant, or purpose.
- Authenticate provider callbacks.
- Return normalized evidence for receipt generation.
- Never log credentials, private keys, payment tokens, or full sensitive account data.

Linq, Stripe, Apple Pay, USDC, Base, DOGE, and future providers may be implemented as separate adapters without changing the authority protocol.
