# AGENTROPOLIS PAY PROTOCOL

> Governed economic authority for autonomous agents.

AGENTROPOLIS Pay Protocol is a provider-neutral specification for creating, approving, executing, revoking, and auditing agent-initiated payments across fiat and onchain rails.

It defines the public trust surface for agentic commerce: identity, mandates, payment intents, approval decisions, execution states, and portable receipts. It does **not** expose production vaults, private signing infrastructure, fraud models, routing intelligence, credentials, or internal enforcement machinery.

## Status

**Version:** `0.1.0-draft`  
**Maturity:** Experimental specification  
**License:** Apache-2.0

This repository is not a payment processor, bank, wallet, escrow service, money transmitter, or financial institution. Implementers remain responsible for regulatory compliance, payment-provider obligations, sanctions screening, consumer protection, privacy, security, tax treatment, and jurisdiction-specific requirements.

## Core principle

**An agent may propose a transaction. It may not invent its own authority.**

Every execution should be traceable through the corridor:

```text
Identity -> Mandate -> Payment Intent -> Policy Evaluation
         -> Approval -> Provider Execution -> Receipt -> Audit
```

## Protocol objects

| Object | Purpose |
|---|---|
| `Mandate` | Defines who granted authority, to which agent, under what limits, for how long, and with what revocation rules. |
| `PaymentIntent` | Describes the proposed economic action before funds move. |
| `Approval` | Records a policy or human decision, including conditions and expiration. |
| `Receipt` | Records what actually happened, through which provider, under which authority, and with what result. |

## Non-negotiable controls

A conforming implementation should support:

- explicit identity and agent attribution
- scoped, expiring mandates
- amount, currency, merchant, category, rail, and frequency limits
- human approval thresholds
- revocation before execution
- idempotency and duplicate-payment resistance
- verified provider callbacks or settlement evidence
- immutable or tamper-evident receipts
- separation between proposal, authorization, and execution
- no secrets or raw payment credentials inside protocol objects

## Repository map

```text
.
├── README.md
├── LICENSE
├── SECURITY.md
├── CONTRIBUTING.md
├── GOVERNANCE.md
├── THREAT-MODEL.md
├── specification/
│   ├── protocol.md
│   ├── authorization-flow.md
│   └── lifecycle.md
├── schemas/
│   ├── mandate.schema.json
│   ├── payment-intent.schema.json
│   ├── approval.schema.json
│   └── receipt.schema.json
├── adapters/
│   └── README.md
├── sdk/
│   └── README.md
└── examples/
    └── governed-payment-flow.json
```

## Boundary of this public repository

### Included

- interoperable data contracts
- lifecycle and authorization rules
- adapter interfaces
- example flows
- governance and threat-model documentation
- implementation guidance

### Excluded

- production signing keys or wallet custody
- processor credentials and webhook secrets
- proprietary policy scoring
- internal fraud rules
- private model-routing logic
- production deployment configuration
- merchant underwriting or compliance systems
- autonomous custody or unrestricted agent spending

## Design position

AGENTROPOLIS Pay Protocol does not process payments. It governs the authority under which agents request and execute economic actions across payment rails.

Payment processors move value. Wallets hold or sign value. Agents propose actions. This protocol binds those actions to identity, policy, permission, evidence, and revocation.

## Example

See [`examples/governed-payment-flow.json`](examples/governed-payment-flow.json) for a complete mandate-to-receipt flow.

## Security

Do not report vulnerabilities in public issues. Follow [`SECURITY.md`](SECURITY.md).

## Contributing

Protocol changes should preserve provider neutrality, deny implicit authority, avoid embedding secrets, and include testable acceptance criteria. See [`CONTRIBUTING.md`](CONTRIBUTING.md) and [`GOVERNANCE.md`](GOVERNANCE.md).

## License

Copyright 2026 Wired Chaos.

Licensed under the Apache License, Version 2.0. See [`LICENSE`](LICENSE).

---

**Authority is not a prompt. It is a runtime constraint.**
