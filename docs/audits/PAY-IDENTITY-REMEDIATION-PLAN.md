# PAY-PROTOCOL Identity Boundary Remediation Plan

**Repository:** `wiredchaos/AGENTROPOLIS-PAY-PROTOCOL`  
**Commit reviewed:** `e253818a6a6bc39d16130e6cd16ef7baa583cc62`  
**Companion evidence:** `docs/audits/PAY-IDENTITY-BOUNDARY-AUDIT.md`  
**Machine record:** `governance/pay-identity-evidence.yaml`  
**Proposed registry patch:** `docs/audits/PAY-IDENTITY-REGISTRY-PATCH.proposed.yaml`

**Scope rule:** This plan remediates **identity-boundary honesty and payment-protocol readiness**. It does **not** authorize production payment behavior changes, moving funds, rotating secrets, applying registry patches automatically, or merging PRs.

---

## Decision premise

PAY-PROTOCOL is **not** the live Identity Authority. Remediation has three tracks:

1. **Boundary track (this repo):** stop overclaiming root identity; bind payment objects to external canonical IDs.
2. **Payment-protocol track (this repo):** mature schemas, lifecycle docs, adapter contracts, and conformance fixtures without becoming a processor or IdP.
3. **Authority track (dedicated Identity Authority):** implement principals, agents, credentials, sessions, root mandates, delegation, capabilities, and revocation — then have PAY consume them.

Do not grow this repository into a wallet custodian, settlement bank, or root identity registry.

```text
principal identity (Identity Authority)
  → agent identity (Identity Authority)
  → mandate (Identity Authority root + PAY spending specialization)
  → capability (Identity Authority / ATG grammar)
  → payment authorization (PAY)
  → wallet or settlement adapter (optional adapters)
  → transaction → receipt and audit
```

Not:

```text
wallet possession → root authority
```

---

## P0 — Authority defects, misclassification, unbound identity, unenforceable fail-closed

### P0-1 — Correct registry classification (external apply, human-gated)

- **Problem:** PAY can be misread as identity authority or as a live payment runtime.
- **Action:** Manually apply `docs/audits/PAY-IDENTITY-REGISTRY-PATCH.proposed.yaml` into `wiredchaos/agentropolis/governance/repository-registry.yaml`.
- **Required dual fields:**
  - `actual_payment_role` / `payment_status: INCUBATING`
  - `identity_authority: false` / `identity_status: EMPTY-SHELL`
- **Acceptance:** Registry does not list PAY as `CANONICAL` identity; payment domain marked incubating schema/protocol, not live processor.

### P0-2 — Bind identity references to Identity Authority

- **Problem:** `principal_id`, `delegate_id`, `agent_id`, `decided_by` are free strings.
- **Action (schema/docs in this repo):**
  - Document that these MUST be canonical IDs issued by the Identity Authority.
  - Add optional typed URI / DID / `id_type` fields or `$comment`/description constraints pointing to IdA.
  - Forbid treating wallet address, payment handle, API key, or merchant id as `principal_id`.
- **Do not:** stand up a principal registry inside PAY.
- **Acceptance:** Normative text + schema descriptions make external IdA ownership unambiguous.

### P0-3 — Designate dedicated Identity Authority

- **Problem:** SOVEREIGNTY, ATG, ONTOLOGY, and PAY are all EMPTY-SHELL for identity.
- **Action:** In federation/canon process, create or name the Identity Authority component.
- **Acceptance:** Canon + hub registry show a non-PAY owner for identity authority; PAY listed as consumer.

### P0-4 — Preserve fail-closed doctrine without fake enforcement

- **Problem:** Protocol says fail closed; nothing executes it.
- **Action:** Keep normative fail-closed rules; add conformance fixtures that assert denial for missing principal, missing mandate, expired/revoked authority, and self-approval.
- **Do not:** claim runtime enforcement until an implementer ships a PDP.
- **Acceptance:** Fixtures exist; README states enforcement is implementer-owned under Identity Authority + PAY rules.

---

## P1 — Spending authority, receipts, adapters, federation alignment

### P1-1 — Complete spending-authority semantics

Specify and schema-extend (additive where possible):

- daily / rolling limits
- per-asset and per-chain limits
- destination allow/deny lists
- rail / privacy-profile permissions
- approval levels and human confirmation thresholds
- emergency freeze / suspension semantics
- empty `approved_merchants` meaning (deny-all vs unrestricted) — **must be explicit; prefer deny-all**

### P1-2 — Align receipts with federation schemas

- Map local `receipt.schema.json` to `agentropolis.receipt.v1`.
- Define emission requirements for payment actions listed in the audit.
- Consume `agentropolis.audit.v1` and `agentropolis.revocation.v1` rather than inventing a parallel identity revocation system.

### P1-3 — Adapter contract honesty

- Keep adapters as **optional settlement/custody interfaces**, never identity providers.
- Require pre-submit revocation revalidation and immutable amount/destination after approval.
- Do not embed Stripe/Base/USDC/Monero/etc. as required runtime deps in the core protocol package.

### P1-4 — SDK / conformance without custody

Deliver planned packages only with:

- schema validation
- digest helpers
- lifecycle transition checks
- fixtures for deny paths

Never include production keys, vault access, or fraud models.

### P1-5 — Agent payment hard bans (normative + fixtures)

Fixtures must reject:

- agent self-approval
- mandate self-expansion
- destination substitution after approval
- replay of the same idempotency key with mutated body
- treating payment capability as general AGENTROPOLIS authority

---

## P2 — Documentation completeness and signature envelope

### P2-1 — Fix README map drift

Either add `specification/authorization-flow.md` and `specification/lifecycle.md`, or remove them from the repository map.

### P2-2 — Constrain approval signatures

Define algorithm, canonicalization, issuer, audience, and what key root is accepted (Identity Authority / human approver keys — not agent payment keys).

### P2-3 — Privacy settlement clarity

Keep Monero/Hush54 as optional profile + policy boundary. Reiterate: no spend keys, seed phrases, or wallet credentials in protocol objects; dry-run before live.

### P2-4 — Wallet model as adapter-only

If wallet linking is needed later, Identity Authority owns link/unlink/compromise identity events; PAY owns spending limits against already-linked wallet ids.

---

## P3 — Cleanup

| ID | Action |
|---|---|
| D3-1 | Replace `agentropolis.example` schema `$id` hosts with the chosen canonical URI when federation host is stable |
| D3-2 | Annotate `examples/governed-payment-flow.json` that IDs are synthetic stand-ins for Identity Authority identifiers |
| D3-3 | README: explicit “Root identity is out of scope; PAY consumes Identity Authority” |
| D3-4 | Apply registry patch only after human review and hub assembly with SOVEREIGNTY + ATG + ONTOLOGY + PAY |
| D3-5 | Comment audit summary on federation tracking issue (#85 or successor) — do not invent production payment behavior |

---

## Proposed patch: `governance/repository-registry.yaml`

**Status:** PROPOSED ONLY — **not applied** in this audit PR.  
**Artifact:** `docs/audits/PAY-IDENTITY-REGISTRY-PATCH.proposed.yaml`

### Acceptance criteria before applying

1. Human governance owner reviews dual status (`identity: EMPTY-SHELL`, `payment: INCUBATING`).  
2. Confirm no other repo is already registered as identity CANONICAL without code evidence.  
3. Assemble cross-repo hub patch covering SOVEREIGNTY + ATG + ONTOLOGY + PAY.  
4. Open/attach federation Issue tracking (Issue #85 or successor).  
5. Apply as a dedicated governance commit in `wiredchaos/agentropolis` after review.

---

## Recommended sequencing

```text
1) Merge evidence artifacts (this PR) — audit docs only
2) Human-review dual-domain registry fields
3) Assemble hub registry patch across SOVEREIGNTY + ATG + ONTOLOGY + PAY
4) Designate/create Identity Authority runtime (not PAY)
5) Bind PAY principal/agent fields to IdA IDs; land conformance fixtures
6) Implement settlement adapters in separate packages under PAY contracts
7) Never grant root authority from wallet possession
```

---

## Out of scope for this repository

- Becoming the canonical Identity Authority
- Custody, MPC/HSM, production signing
- Live Stripe/processor integration secrets
- Moving funds, rotating secrets, emergency production halts from this public draft repo
- Auto-applying federation registry changes
