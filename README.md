# AGENTROPOLIS PAY PROTOCOL

> Governed economic authority for autonomous agents.

AGENTROPOLIS Pay Protocol is a provider-neutral specification for creating, approving, executing, revoking, and auditing agent-initiated payments across fiat, public onchain, and privacy-preserving settlement rails.

It defines the public trust surface for agentic commerce: identity, mandates, payment intents, approval decisions, execution states, portable receipts, and selective-disclosure evidence boundaries. It does **not** expose production vaults, private signing infrastructure, fraud models, routing intelligence, credentials, or internal enforcement machinery.

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

Privacy-preserving settlement does not bypass this corridor. It changes who may see transaction evidence, not whether authority must exist.

## Protocol objects

| Object | Purpose |
|---|---|
| `Mandate` | Defines who granted authority, to which agent, under what limits, for how long, and with what revocation rules. |
| `PaymentIntent` | Describes the proposed economic action before funds move. |
| `Approval` | Records a policy or human decision, including conditions and expiration. |
| `Receipt` | Records what actually happened, through which provider, under which authority, and with what result. |
| `PrivacySettlementEnvelope` | Links a public-safe receipt to encrypted evidence governed by selective-disclosure and retention policy. |

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
- minimum-necessary disclosure for privacy-preserving settlement
- receipted access to protected settlement evidence

## Privacy-preserving settlement

The privacy extension separates a public-safe receipt from encrypted private evidence. A private rail may conceal destination and transaction metadata from public observers, but it must not conceal the mandate, approval basis, or evidence commitment from authorized governance.

The initial network profile documents Monero/XMR as a privacy-preserving settlement option beneath a separate policy boundary such as Hush54. The protocol remains provider-neutral and does not embed wallet custody or signing code.

See [`specification/privacy-settlement.md`](specification/privacy-settlement.md) and [`schemas/privacy-settlement-envelope.schema.json`](schemas/privacy-settlement-envelope.schema.json).

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
│   ├── lifecycle.md
│   └── privacy-settlement.md
├── schemas/
│   ├── mandate.schema.json
│   ├── payment-intent.schema.json
│   ├── approval.schema.json
│   ├── receipt.schema.json
│   └── privacy-settlement-envelope.schema.json
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
- selective-disclosure and evidence-envelope contracts

### Excluded

- production signing keys or wallet custody
- processor, node, wallet-RPC, or webhook secrets
- spend keys, seed phrases, or raw view credentials
- proprietary policy scoring
- internal fraud rules
- private model-routing logic
- production deployment configuration
- merchant underwriting or compliance systems
- autonomous custody or unrestricted agent spending

## Design position

AGENTROPOLIS Pay Protocol does not process payments. It governs the authority under which agents request and execute economic actions across payment rails.

Payment processors and networks move value. Wallets hold or sign value. Agents propose actions. This protocol binds those actions to identity, policy, permission, evidence, revocation, and controlled disclosure.

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