# Security Policy

AGENTROPOLIS Pay Protocol defines authorization and receipt formats for agentic commerce. Security reports must be handled privately because public disclosure may expose implementers before mitigations are available.

## Supported versions

| Version | Supported |
|---|---|
| `main` / current draft | Yes |
| Older drafts | No guaranteed support |

## Reporting a vulnerability

Do not open a public issue containing exploit details, secrets, payment credentials, personal data, private keys, signing material, webhook secrets, or production endpoints.

Use GitHub private vulnerability reporting when enabled for this repository. If private reporting is unavailable, contact the repository owner through a verified private channel and include only the minimum information necessary to establish contact.

A useful report includes:

- affected protocol object, schema, or lifecycle state
- impact and realistic attack path
- reproduction steps using test data only
- proposed mitigation when available
- whether the issue is already public

## Security boundaries

This repository must never contain:

- private keys, seed phrases, API keys, or session tokens
- raw card or bank-account data
- production webhook secrets
- proprietary fraud thresholds or allowlists
- production signing or custody infrastructure
- customer personal data

## High-priority vulnerability classes

Reports involving the following are treated as critical design concerns:

- mandate forgery or unauthorized delegation
- approval replay or substitution
- payment-intent tampering
- idempotency bypass and duplicate execution
- revocation ignored after authorization
- receipt forgery or evidence mismatch
- confused-deputy attacks between agents and providers
- cross-tenant data leakage
- signature confusion or algorithm downgrade
- webhook spoofing or settlement-state forgery

## Safe-harbor intent

Good-faith research using test environments, synthetic data, and non-destructive methods is welcome. Do not access funds, accounts, or data belonging to others; degrade services; retain sensitive data; or publicly disclose unresolved vulnerabilities.
