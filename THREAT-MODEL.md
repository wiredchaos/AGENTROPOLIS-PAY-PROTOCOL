# Threat Model

## Protected properties

- no agent may self-authorize
- no payment may exceed its mandate
- approvals must be attributable and revocable
- execution must be idempotent
- provider events must be authenticated
- every terminal outcome must produce a receipt

## Primary threats

1. **Prompt or tool injection** attempting to create unauthorized payment intent.
2. **Mandate escalation** through altered limits, merchants, assets, or expiry.
3. **Replay and duplication** of intents, approvals, webhooks, or settlement calls.
4. **Confused deputy** behavior where an agent uses another principal's authority.
5. **Webhook forgery** or out-of-order provider events.
6. **Receipt tampering** after authorization or execution.
7. **Revocation race** between cancellation and provider submission.
8. **Adapter compromise** that misreports amount, destination, or final state.
9. **Key or secret exposure** through logs, examples, telemetry, or source control.
10. **Unbounded autonomy** caused by vague, perpetual, or recursively delegated mandates.

## Required controls

- canonical object serialization before hashing or signing
- globally unique IDs and idempotency keys
- explicit issuer, subject, scope, limits, expiry, and revocation state
- separation of proposal, approval, execution, and settlement roles
- fail-closed policy evaluation
- signed provider callbacks or equivalent authenticated transport
- monotonic state transitions
- immutable receipt hash chaining or equivalent tamper evidence
- human approval at configured thresholds
- immediate revocation checks before execution
- synthetic fixtures only in public repositories

## Out of scope

The protocol does not secure a compromised operating system, malicious payment processor, stolen signing key, insolvent custodian, or intentionally fraudulent merchant. Implementations must add operational security, regulatory controls, monitoring, and incident response appropriate to their environment.
