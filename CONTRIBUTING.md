# Contributing

Contributions are welcome when they strengthen interoperability, auditability, revocation, and least-authority execution.

## Before opening a pull request

1. Open an issue describing the protocol problem.
2. Identify the affected object, state transition, or adapter contract.
3. Explain the security and compatibility impact.
4. Include testable acceptance criteria.
5. Use synthetic examples only. Never include production credentials or customer data.

## Design requirements

Changes must:

- preserve separation between proposal, authorization, and execution
- deny implicit or self-created agent authority
- support revocation and expiration
- remain provider-neutral unless explicitly scoped as an adapter example
- avoid secrets, raw payment credentials, and personal data
- define failure states, not only success paths
- preserve idempotency and replay resistance
- produce sufficient evidence for an auditable receipt

## Pull request checklist

- [ ] The change has a linked issue or clear rationale.
- [ ] Schemas remain valid JSON Schema Draft 2020-12.
- [ ] New fields document required, optional, and prohibited behavior.
- [ ] Backward compatibility is explained.
- [ ] Threat-model consequences are addressed.
- [ ] Examples use fictional identities, merchants, and transaction references.
- [ ] No secret, credential, private key, or production endpoint is included.

## Compatibility

Breaking changes require a new major protocol version. Additive optional fields may be introduced in minor versions. Clarifications and non-normative examples may be introduced in patch versions.

## Language

The words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, and **MAY** are to be interpreted as normative requirements when capitalized.

## License

By contributing, you agree that your contribution is licensed under Apache License 2.0.
