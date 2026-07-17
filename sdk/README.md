# SDK Boundary

The public SDK should provide:

- schema validation
- canonical serialization and digest helpers
- lifecycle transition validation
- adapter interfaces
- receipt verification
- conformance test fixtures

The SDK must not contain production signing keys, vault access, private fraud logic, merchant credentials, custody code, or internal payment-routing policy.

Planned packages:

- `@agentropolis/pay-protocol`
- `@agentropolis/pay-adapter-kit`
- `@agentropolis/pay-conformance`
