# Privacy-Preserving Settlement Extension

## Status

Draft extension for AGENTROPOLIS Pay Protocol `0.1.x`.

## Purpose

This extension defines how a governed payment may use a privacy-preserving settlement rail without turning privacy into unaccountable agent authority.

Privacy protects principals, counterparties, commercially sensitive activity, and operational metadata. It does not waive identity, mandate, policy evaluation, approval, revocation, sanctions or legal obligations, or the requirement to produce auditable evidence.

## Core rule

> Private settlement may hide transaction details from the public. It must not hide authority from the authorized auditor.

An agent may request a privacy-preserving rail only when the active mandate explicitly permits that rail or privacy profile.

## Roles

- **Privacy policy boundary**: decides whether private settlement is permitted and defines disclosure, retention, and review rules.
- **Settlement adapter**: translates an approved payment intent into a provider-specific or network-specific operation.
- **Evidence custodian**: stores encrypted settlement evidence outside public protocol objects.
- **Authorized auditor**: may verify the evidence under the disclosure policy.

These roles SHOULD remain separable. An agent MUST NOT act as its own approver, evidence custodian, and auditor.

## Required controls

A conforming privacy-preserving settlement flow MUST:

1. reference an active, versioned mandate;
2. identify the requesting agent and governing privacy policy;
3. bind approval to the exact intent digest;
4. re-check revocation immediately before submission;
5. use an idempotency key and replay protection;
6. keep spend keys, seed phrases, wallet credentials, RPC credentials, and raw signing material outside protocol objects;
7. produce a public-safe terminal receipt;
8. produce or reference encrypted private evidence sufficient for an authorized review;
9. record every disclosure of private evidence;
10. fail closed when policy, evidence, or authorization data is missing.

## Public receipt and private evidence

A privacy-preserving transaction SHOULD produce two evidence surfaces.

### Public-safe receipt

The public-safe receipt MAY include:

- receipt identifier;
- intent identifier and digest;
- mandate identifier and version;
- agent identifier;
- privacy profile identifier;
- settlement rail identifier;
- terminal status;
- timestamps;
- evidence commitment or digest;
- encrypted evidence reference.

It SHOULD NOT expose a destination address, private transaction metadata, view credentials, transaction keys, wallet identifiers, or commercially sensitive descriptions unless the disclosure policy explicitly permits them.

### Private evidence envelope

The private evidence envelope MAY contain encrypted or access-controlled references to:

- destination evidence;
- settlement reference;
- confirmation evidence;
- amount and asset evidence;
- provider or network response;
- view-only verification material;
- refund or reversal evidence;
- disclosure history.

The envelope MUST NOT contain raw spend keys, seed phrases, or unrestricted signing credentials.

## Selective disclosure

The disclosure policy MUST define:

- who may request access;
- the purpose for which access is allowed;
- the minimum evidence fields disclosed;
- approval requirements;
- retention and deletion rules;
- how the disclosure itself is receipted.

Implementations SHOULD disclose the minimum evidence required for the stated review purpose.

## Monero profile

A Monero-compatible implementation uses the rail identifier `monero` and asset identifier `XMR`.

The public-safe receipt SHOULD avoid publishing the destination, wallet account, transaction key, view key, or other correlation-sensitive metadata. Verification material SHOULD remain inside the encrypted private evidence boundary and be disclosed only under policy.

A Monero adapter MUST:

- operate through an external wallet or signing boundary;
- reject seed phrases, spend keys, and raw wallet credentials in agent requests;
- support dry-run mode before live settlement is enabled;
- bind the settlement request to the approved intent digest and idempotency key;
- monitor confirmation state without exposing sensitive wallet metadata to the requesting agent;
- emit terminal receipts for success, failure, denial, expiry, reversal, and refund where applicable.

This specification does not claim that privacy technology removes legal, tax, accounting, sanctions, consumer-protection, or reporting obligations.

## Hush54 relationship

Within Agentropolis, Hush54 may implement the restricted privacy policy boundary described by this extension. Hush54 is not the settlement rail and MUST NOT hold unrestricted agent signing authority. A Monero adapter may execute approved XMR settlement beneath the Hush54 policy boundary.

```text
Identity + Mandate
        -> Payment Intent
        -> Hush54 privacy policy decision
        -> Human or delegated approval when required
        -> Monero adapter
        -> Public-safe receipt + encrypted private evidence
        -> Authorized audit or selective disclosure
```

## Conformance

An implementation conforms to this extension when it preserves the base protocol lifecycle, denies unmandated private settlement, keeps signing secrets outside agent-visible objects, produces both public-safe and authorized private evidence surfaces, and records selective disclosures.