# Protocol Specification

## Purpose

AGENTROPOLIS Pay Protocol defines interoperable objects and state transitions for governed agentic payments. It separates economic intent from execution so no model, agent, adapter, or payment provider can silently expand its own authority.

## Roles

- **Principal**: human or organization granting authority.
- **Agent**: software actor proposing a payment.
- **Policy engine**: evaluates the proposal against the active mandate.
- **Approver**: human or delegated authority issuing a decision.
- **Executor**: submits an approved intent to a payment rail.
- **Adapter**: translates protocol objects into provider-specific operations.
- **Auditor**: verifies decisions, events, and receipts.

## Core objects

1. **Mandate**: bounded authority granted by a principal.
2. **Payment Intent**: immutable proposal describing what the agent wants to pay.
3. **Approval**: attributable decision tied to one intent and one mandate version.
4. **Receipt**: tamper-evident record of execution and final outcome.

## Normative rules

- Agents MUST NOT approve their own payment intents.
- Every intent MUST reference an active mandate.
- Every approval MUST reference the exact intent digest and mandate version evaluated.
- Implementations MUST fail closed when required policy data is missing or invalid.
- Execution MUST use an idempotency key.
- Revocation MUST be checked immediately before provider submission.
- Amount, currency or asset, destination, merchant, and purpose MUST NOT change after approval.
- Provider callbacks MUST be authenticated and correlated to the original execution.
- Every terminal state MUST produce a receipt, including failure, denial, expiry, reversal, and refund.

## Conformance

An implementation is conformant when it validates the published schemas, enforces the lifecycle rules, rejects invalid state transitions, and preserves sufficient evidence to independently reconstruct why a transaction was permitted or denied.
