# AGENTROPOLIS PAY-PROTOCOL Identity Boundary Audit

**Repository audited:** `wiredchaos/AGENTROPOLIS-PAY-PROTOCOL`  
**Commit reviewed:** `e253818a6a6bc39d16130e6cd16ef7baa583cc62`  
**Audit date:** 2026-07-19  
**Auditor:** Cursor cloud agent (Phase 3 federation hardening)  
**Cloud agent:** https://cursor.com/agents/bc-e4393c67-c00f-4f95-aa5a-91003b234f3a  
**Method:** Full tree inspection of every tracked file. README and normative prose treated as claims; schemas, examples, adapter/SDK stubs, and absence of runtime treated as evidence. No production payment behavior changed. No funds moved. No secrets rotated. No registry patch applied.

**Authority documents expected but not present in this checkout / fetchable here:**

| Expected authority | Status in audit environment |
|---|---|
| `wiredchaos/agentropolis` | Repository not found (404) |
| `AGENTROPOLIS-CANON.yaml` | Absent / not fetchable |
| `docs/FEDERATION-HARDENING-V1.md` | Absent / not fetchable |
| `governance/repository-registry.yaml` | Absent (proposed patch only; not applied) |
| `wiredchaos/agentropolis/issues/85` | Not found (404) |

**Prior confirmed identity non-candidates (external context):**

| Repository | Identity-domain status |
|---|---|
| `AGENTROPOLIS-SOVEREIGNTY` | EMPTY-SHELL |
| `AGENTROPOLIS-ATG` | EMPTY-SHELL |
| `AGENTROPOLIS-ONTOLOGY` | EMPTY-SHELL |

---

## 1. Executive summary

This repository **does not implement root identity authority**.

Code evidence shows an **experimental, provider-neutral payment-authority protocol surface**: JSON Schemas for Mandate / PaymentIntent / Approval / Receipt / PrivacySettlementEnvelope, normative prose, one synthetic example, and empty adapter/SDK directories. There is **no runtime**, no package manifest, no API, no database, no wallet custody, no signing, no settlement engine, no CI, and no tests.

Identity appears only as **free-string payment-domain references** (`principal_id`, `delegate_id`, `agent_id`, `decided_by`). Those strings are not typed principal/agent registries, credentials, sessions, or revocation authorities.

| Question | Evidence-based answer |
|---|---|
| Implements root identity? | **No** |
| Implements payment-domain identity references? | **Schema fields only** (untyped strings) |
| Suitable Identity Authority? | **No** |
| What is it actually? | Payment protocol specification + schema-only package |
| Identity-domain status | **EMPTY-SHELL** |
| Payment-domain status | **INCUBATING** |
| Wallet possession → root authority? | **Not implemented** (and explicitly rejected in design prose) |

**Core answer:** PAY-PROTOCOL must **consume** identity from a separate AGENTROPOLIS Identity Authority. It may own payment intents, spending-policy evaluation contracts, settlement adapter interfaces, and payment receipts — not principals, credentials, sessions, root mandates, or revocation authority.

---

## 2. Actual repository role

### Classification (tree evidence)

| Role label | Fits? | Evidence |
|---|---|---|
| payment protocol | **Yes (spec)** | `specification/protocol.md`, schemas, README corridor |
| wallet abstraction | Partial (docs only) | Adapter README; no wallet types or code |
| settlement service | No | No executor |
| treasury service | No | Absent |
| escrow service | No | Absent |
| merchant payment service | No | `merchant_id` string field only |
| agent spending controls | Spec only | Mandate `limits` schema; no evaluator |
| payment authorization service | Spec only | Approval schema + normative rules; no PDP |
| identity adapter | No | Free strings; no IdP adapter |
| wallet identity adapter | No | Absent |
| credential service | No | Explicitly excluded |
| payment risk engine | No | Explicitly excluded (fraud models out of scope) |
| chain adapter | Spec stub | Monero profile docs; no code |
| schema-only package | **Yes** | Five JSON Schemas; no runtime package |
| documentation-only package | **Yes** | Majority of tree is markdown |
| prototype | Weak | Example JSON only; no runnable prototype |
| legacy implementation | No | Draft `0.1.0-draft` |
| duplicate | No evidence | Distinct payment schema surface vs ATG/ONTOLOGY |
| empty shell | **Yes for identity**; **partial for payment** | Identity runtime empty; payment schemas incubating |

**Primary role labels:** `schema-only package`, `documentation-only package`, `payment protocol` (specification / incubating).

### Evidence surfaces inspected

| Path | Kind | Notes |
|---|---|---|
| `README.md` | Docs | Claims identity in corridor; excludes custody/secrets |
| `specification/protocol.md` | Normative prose | Roles + MUST rules; short |
| `specification/privacy-settlement.md` | Extension prose | Monero/Hush54 profile |
| `schemas/*.schema.json` | Contracts | Mandate, intent, approval, receipt, privacy envelope |
| `examples/governed-payment-flow.json` | Synthetic fixture | Free-string IDs |
| `adapters/README.md` | Interface stub | Named ops; no code |
| `sdk/README.md` | Boundary stub | Planned packages; no code |
| `GOVERNANCE.md`, `SECURITY.md`, `THREAT-MODEL.md`, `CONTRIBUTING.md` | Process docs | No enforcement |
| `package.json` / `src/` / `.github/` / tests | **Absent** | No runtime, CI, or tests |
| `specification/authorization-flow.md`, `lifecycle.md` | **Absent** | Listed in README map; not in tree |

### Claimed vs implemented

| Claim source | Claim | Evidence |
|---|---|---|
| README lead | “identity, mandates, payment intents…” as trust surface | Identity = string fields only; no identity runtime |
| README corridor | `Identity -> Mandate -> … -> Receipt` | Documented; not executable |
| Normative rules | Agents MUST NOT self-approve; fail closed; revocation before submit | Prose only |
| Adapter README | Linq/Stripe/Apple Pay/USDC/Base/DOGE as adapters | Names only; no adapters |
| SDK README | `@agentropolis/pay-protocol` packages | Planned; not present |
| Privacy extension | Monero settlement under Hush54 | Profile docs; no wallet/RPC code |

---

## 3. Architecture map (as implemented)

```text
┌────────────────────────────────────────────────────────────┐
│ PUBLIC SPEC SURFACE (this repo)                            │
│  schemas/{mandate,payment-intent,approval,receipt,         │
│           privacy-settlement-envelope}.schema.json         │
│  specification/{protocol,privacy-settlement}.md            │
│  examples/governed-payment-flow.json                       │
│  adapters/README.md  (ops listed, no code)                 │
│  sdk/README.md       (packages planned, no code)           │
└────────────────────────────────────────────────────────────┘
                         │
                         │  (no runtime binder)
                         ▼
              ┌──────────────────────┐
              │ IMPLEMENTATION       │  ABSENT
              │ PDP / executor /     │
              │ wallet / custody /   │
              │ receipt store / CI   │
              └──────────────────────┘

MISSING FOR ROOT IDENTITY:
  principal registry · organization registry · agent identity
  credentials · sessions · devices · typed mandates from IdA
  delegation engine · capability PDP · revocation bus
  agentropolis.receipt.v1 / audit.v1 / revocation.v1 emitters
```

Implemented substance is approximately **578 lines** of schemas + docs + one example across 16 tracked files (excluding LICENSE).

---

## 4. Entity inventory

### 4.1 Identity-domain entities

| Entity | Present? | Path | Schema/type | Storage | Create/Update/Expiry/Cancel/Revoke | Ownership field | Identity reference | Typed? |
|---|---|---|---|---|---|---|---|---|
| human principal | **Reference only** | `schemas/mandate.schema.json` | `principal_id: string` | none | none | none | free string | **No** |
| organization | **No** | — | — | — | — | — | — | — |
| agent | **Reference only** | `payment-intent.schema.json` `agent_id`; mandate `delegate_id` | string | none | none | none | free string | **No** |
| service account | **No** | — | — | — | — | — | — | — |
| credential | **No** (prohibited in objects) | SECURITY.md / README | — | — | — | — | — | — |
| session | **No** | — | — | — | — | — | — | — |
| mandate | **Schema object** | `schemas/mandate.schema.json` | Pay Mandate | none | status enum + `valid_*` fields; no engine | `principal_id` | free strings | **No** |
| delegation | **No** (beyond mandate delegate_id) | mandate `delegate_id` | string | none | none | none | free string | **No** |
| capability | **No** typed capability | limits object only | amount/merchant/category limits | none | none | — | — | N/A |
| consent | **No** | — | — | — | — | — | — | — |
| revocation | **Field + prose** | `revocation_uri`; normative “check before submit” | URI string | none | no bus | — | — | N/A |
| wallet link | **No** | wallets excluded from protocol objects | — | — | — | — | — | — |
| device | **No** | — | — | — | — | — | — | — |

### 4.2 Payment-domain entities

| Entity | Present? | Path | Notes |
|---|---|---|---|
| payer | Implicit | mandate `principal_id` | Not modeled as payer account |
| payee / destination | Schema | intent `destination` free string | May be merchant id or address-shaped string; untyped |
| merchant | Schema | `merchant_id`, `merchant_category`, `approved_merchants[]` | Free strings |
| beneficiary | **No** distinct type | — | — |
| wallet | Docs only | privacy-settlement; adapter README | External boundary; no wallet schema |
| account | **No** | — | — |
| treasury | **No** | — | — |
| escrow | **No** | — | — |
| payment intent | **Schema** | `payment-intent.schema.json` | Full state enum; no store |
| invoice | **No** | — | — |
| transaction | Implicit | receipt `provider_reference` | Provider-side |
| settlement | Extension | privacy envelope + Monero profile | Docs/schema only |
| refund | Enum/prose | intent/receipt outcomes; adapter `refund()` | No implementation |
| chargeback | **No** | — | — |
| spending authority | Partial | mandate `limits` | Amount/currency/merchants/categories/human threshold |
| budget | Partial | `max_amount`, `max_transactions` | No daily/asset/chain limits in schema |
| allowance | **No** distinct | — | — |
| approval | **Schema** | `approval.schema.json` | `decided_by` free string; optional `signature` |
| signer | **No** | signing excluded | — |
| chain | Profile string | privacy `rail`, `rail_evidence.network` | Free strings |
| asset / token | Schema | intent `asset`, mandate `currency` | Free strings (e.g. USD, XMR) |
| receipt | **Schema** | `receipt.schema.json` | Pay-specific; not `agentropolis.receipt.v1` |

### 4.3 Distinction check

| Required distinction | Status |
|---|---|
| principal ≠ agent ≠ wallet ≠ session ≠ device ≠ role ≠ capability ≠ mandate | **Not enforced** — only principal/agent/mandate string refs exist |
| Wallet not treated as complete identity | **Design intent yes** (wallets out of protocol objects); **no lifecycle to misuse** |
| Payment handle / address ≠ root identity | **Not implemented as identity** |

---

## 5. Root identity vs payment identity

### Treated as root identity?

| Candidate | Treated as root identity? | Evidence |
|---|---|---|
| wallet address | **No** | Explicitly excluded from protocol objects; privacy profile forbids publishing wallet ids in public receipt |
| blockchain account | **No** | No account model |
| payment account | **No** | Absent |
| merchant account | **No** | Free-string `merchant_id` only |
| API key | **No** | No auth runtime; secrets prohibited |
| transaction signer | **No** | Signing infrastructure excluded |
| custody account | **No** | Custody excluded |
| payment handle | **No** | `destination` is opaque string for payment, not identity registry |
| email | **No** | Absent |
| device | **No** | Absent |
| token balance | **No** | Absent |

### Possession → broader AGENTROPOLIS authority?

**No implemented path.** There is no code that grants federation authority from wallet, key, payment account, or API token possession.

**Residual documentation risk:** README lists “identity” in the public trust surface without stating that root identity is externally owned. Free-string `principal_id` / `agent_id` could be mistaken for a local identity registry if a future implementer binds them without an Identity Authority.

**Required model vs observed:**

```text
REQUIRED:
  principal → agent → mandate → capability → payment auth → wallet/settlement adapter → tx → receipt/audit

OBSERVED (docs/schemas only):
  principal_id (string) → delegate_id/agent_id (string) → Mandate schema → PaymentIntent → Approval → Receipt
  (no capability object, no wallet adapter code, no executor, no audit.v1)
```

---

## 6. Payment authorization flow (trace)

Target path:

`payment request → authentication → principal resolution → agent resolution → mandate lookup → spending capability → budget check → policy → risk → approval → wallet/treasury selection → signing → settlement → receipt → audit → revocation check`

| Stage | Path / symbol | Input | Output | Fail-open/closed | External deps | Tests |
|---|---|---|---|---|---|---|
| payment request | schema `PaymentIntent` | object fields | n/a | n/a | none | missing |
| authentication | **missing** | — | — | — | — | missing |
| principal resolution | mandate `principal_id` field only | string | string | n/a | Identity Authority (unbound) | missing |
| agent resolution | `agent_id` / `delegate_id` fields | string | string | n/a | Identity Authority (unbound) | missing |
| mandate lookup | schema + example | — | — | prose: fail closed if missing | none implemented | missing |
| spending capability | mandate `limits` | amounts/merchants | — | no evaluator | none | missing |
| budget check | `max_amount`, `max_transactions` | — | — | no runtime | none | missing |
| policy decision | prose “policy engine” role | — | — | MUST fail closed (prose) | none | missing |
| risk decision | **excluded** | — | — | — | — | missing |
| approval requirement | `human_approval_above` + Approval schema | — | — | agents MUST NOT self-approve (prose) | none | missing |
| wallet/treasury selection | **missing** | — | — | — | adapters (absent) | missing |
| signing request | **excluded** | — | — | — | external wallet | missing |
| settlement | adapter ops listed | — | — | recheck revocation (prose) | providers (absent) | missing |
| receipt | `receipt.schema.json` | — | — | terminal states MUST receipt (prose) | none | missing |
| audit | **missing** `agentropolis.audit.v1` | — | — | — | — | missing |
| revocation check | `revocation_uri` + prose | URI | — | before submit (prose) | external URI unbound | missing |

**Closest implemented path:** none executable. Closest artifact path is the synthetic example `examples/governed-payment-flow.json` (mandate → intent → approval → receipt) with `mock-provider`.

---

## 7. Spending authority

| Control | Status | Evidence |
|---|---|---|
| principal | free-string field | `principal_id` |
| agent / authorized spender | free-string | `delegate_id`, `agent_id` |
| issuer | implicit principal | no issuer credential |
| budget | partial | `max_amount`, `max_transactions` |
| per-transaction limit | via `max_amount` | no separate field |
| daily limit | **missing** | — |
| asset limit | currency/asset strings only | no multi-asset policy |
| merchant/category restrictions | optional arrays | free strings |
| destination restrictions | not beyond merchants | intent `destination` free string |
| chain restrictions | **missing** in base mandate | privacy rail via separate profile |
| time window | `valid_from` / `valid_until` | schema |
| expiry | status `expired` + validity | no engine |
| approval level | `human_approval_above` | threshold string |
| human confirmation | Approval `decided_by` | free string |
| emergency freeze | **missing** | status `suspended`/`revoked` only as enum |
| cancellation / revocation | status enum + `revocation_uri` | no propagation |
| receipt linkage | receipt → intent/mandate/approval ids | schema |

### Flags

| Flag | Present? |
|---|---|
| free-string principal references | **Yes** |
| wildcard spending authority | Not in schema; empty merchant arrays could be interpreted as unrestricted by implementers — **unspecified** |
| unlimited budgets | `max_amount` required — good; no absolute ceiling semantics |
| permanent grants | `valid_until` required — good; no renewal engine |
| client-side-only checks | N/A (no client) |
| wallet-only authority | **No** (and forbidden by design) |
| lack of downstream revalidation | No runtime; prose requires pre-submit revocation check |
| cached authorization surviving revocation | No cache implemented |

---

## 8. Agent payments

| Capability | Can agents (in this repo)? |
|---|---|
| create payment intents | Schema allows `agent_id` on intent; **no API** |
| approve payments | Normative **MUST NOT** self-approve; **no enforcement** |
| sign transactions | **No** / excluded |
| select wallets | **No** |
| change destinations | Normative MUST NOT after approval; **no enforcement** |
| increase budgets | **No** mandate mutation API |
| retry failed payments | Idempotency key field only |
| issue refunds | Adapter op listed; no code |
| move treasury funds | **No** |
| create sub-accounts | **No** |
| delegate spending | Only via new mandate object conceptually; no engine |

| Must not | Enforced? |
|---|---|
| self-authorize | Prose only |
| mint own spending capability | No capability minting surface |
| expand mandate | No update API |
| bypass human approval | Prose + threshold field only |
| change treasury policy | No treasury |
| substitute destination after approval | Prose only |
| replay signed transactions | Idempotency field; no nonce/signer runtime |
| convert payment capability → general authority | No capability→authority bridge implemented |

---

## 9. Wallet model

| Concern | Status |
|---|---|
| wallet creation / linking / ownership proof | **missing** |
| custodial vs noncustodial | **documentation boundary only** |
| signer separation | Design: external wallet/signing boundary |
| key custody | Explicitly out of repo |
| rotation / unlink / compromise response | **missing** |
| multiple wallets per principal | **missing** |
| wallet-to-agent assignment | **missing** |
| spending limits | On mandate, not wallet |
| nonce / domain separation / chain ID / replay | **missing** (idempotency_key on intent only) |

**Classification of wallet integrations:** none implemented. Named future adapters (Stripe, Base/USDC, DOGE, Monero, Linq, Apple Pay) are **optional adapter** aspirational references, not core identity dependencies.

---

## 10. Chain and asset dependencies

| Dependency | Path | Role | Abstraction | Degraded mode | Replacement | Blocks chain-agnostic? |
|---|---|---|---|---|---|---|
| Monero / XMR | `specification/privacy-settlement.md` | privacy settlement **profile** | rail/asset string ids | dry-run mentioned in prose | other privacy rails | **No** (profile, not hard runtime) |
| Hush54 | privacy-settlement.md | privacy policy boundary (external) | named boundary | n/a | other policy boundary | **No** |
| Base | `adapters/README.md` | mentioned future adapter | name only | n/a | other chains | **No** |
| USDC | adapters README | mentioned future adapter | name only | n/a | other assets | **No** |
| DOGE | adapters README | mentioned future adapter | name only | n/a | other assets | **No** |
| Ethereum / Solana / BTC / XRP / Stellar / Hedera | — | **not present** | — | — | — | **No** |
| Smart accounts / custodians / bridges | — | **not present** | — | — | — | **No** |

**Verdict:** Spec is written as provider/chain-agnostic. No hard runtime chain dependency exists because there is no runtime.

---

## 11. Payment credentials and authentication

| Mechanism | Status |
|---|---|
| API keys / OAuth / JWT | **missing** |
| wallet signatures | **missing** (optional approval `signature` string unconstrained) |
| service / merchant credentials | **prohibited** in protocol objects |
| webhook secrets | Adapter must authenticate callbacks (prose); no code |
| signing keys / MPC / HSM | **excluded** |
| static bearer / admin tokens | **missing** |

### Security search (committed secrets / unsafe patterns)

| Search | Result |
|---|---|
| Committed secrets / hard-coded keys | **None found** |
| Unsigned webhooks | No webhook server |
| Permissive CORS | No HTTP server |
| Missing issuer/audience checks | No JWT |
| Timing-unsafe token compare | No token compare |
| Insecure dev fallbacks | No runtime |
| Plaintext private keys | **None** |

---

## 12. Revocation and freeze

| Control | Status | Propagation / latency / cache / pending / offline / consumers / receipts / audit / rollback |
|---|---|---|
| principal freeze | **missing** | — |
| agent freeze | **missing** | — |
| wallet freeze | **missing** | — |
| merchant freeze | **missing** | — |
| treasury freeze | **missing** | — |
| payment-intent cancellation | schema states `revoked`/`expired` | no engine |
| spending-authority revocation | mandate `status` + `revocation_uri` | no bus; latency undefined |
| signer revocation | **missing** | — |
| API-key revocation | **missing** | — |
| session revocation | **missing** | — |
| emergency halt | **missing** | — |
| chain-route / asset disablement | **missing** | — |

---

## 13. Receipts and audit

| Federation schema | Present? |
|---|---|
| `agentropolis.receipt.v1` | **No** — local `receipt.schema.json` instead |
| `agentropolis.audit.v1` | **No** |
| `agentropolis.revocation.v1` | **No** |

### Required action coverage

| Action | Coverage |
|---|---|
| wallet linked / unlinked | **missing** |
| spending capability granted / denied | **missing** (mandate object exists; no grant events) |
| spending authority revoked | status field only |
| payment requested / approved / denied | schema states + example |
| transaction signed / broadcast | **missing** |
| settlement confirmed | receipt `succeeded` + privacy envelope |
| refund issued | outcome enum |
| escrow released | **missing** |
| treasury transfer | **missing** |
| emergency freeze / recovery / compromised wallet | **missing** |

For events that exist as schema fields, **actor/principal/agent/mandate/capability/wallet/chain/policy version/integrity proof/persistence/retention** are incomplete: free-string IDs, optional digests, no durable store, no retention policy engine (privacy envelope has `retention_until` field only).

---

## 14. Smart contracts and settlement

| Item | Status |
|---|---|
| Smart contracts in repo | **None** (no `.sol` / chain programs) |
| Upgrade / pause / owner / multisig / timelock | N/A |
| Reentrancy / replay / finality / escrow release | N/A in-repo; privacy profile mentions confirmations |

**Distinction:** No contract authorization exists to confuse with AGENTROPOLIS mandate authorization. Settlement authorization is entirely unspecified at runtime and belongs to future adapters + Identity Authority mandates.

---

## 15. Provider dependencies

| Provider | Classification | Evidence |
|---|---|---|
| Stripe | optional adapter (named) | `adapters/README.md` |
| Apple Pay | optional adapter (named) | adapters README |
| Linq | optional adapter (named) | adapters README |
| USDC / Base / DOGE | optional adapter / asset/chain names | adapters README |
| Monero | optional privacy settlement profile | privacy-settlement.md |
| Coinbase / Circle / Privy / Safe / Fireblocks / Turnkey / Crossmint / MoonPay / PayPal / Square / Plaid / Alchemy / Infura | **not present** | tree search |
| mock-provider | example only | `examples/governed-payment-flow.json` |

None are required runtime dependencies — there is no runtime.

---

## 16. Data and privacy

| Data class | In repo? | Storage / encryption / retention / ACL / deletion / export / logging / third-party / jurisdiction |
|---|---|---|
| Legal names, phones, billing, bank, KYC/AML, tax, IP, device ids, risk/sanctions | **No** | Synthetic example IDs only |
| Emails | **No** | — |
| Wallet addresses | **No** (forbidden in public privacy receipt) | — |
| Transaction history | Example receipt only | Not a store |
| Merchant data | Synthetic merchant ids | — |

SECURITY.md and CONTRIBUTING forbid production PII/credentials in the public repo. No privacy runtime controls exist beyond schema fields (`authorized_viewer_roles`, `retention_until`, disclosure events).

---

## 17. Test matrix

| Case | Classification |
|---|---|
| missing principal | **missing** |
| unauthorized agent | **missing** |
| missing / expired / revoked spending authority | **documentation only** (status enums) |
| budget exceeded | **documentation only** |
| destination changed | **documentation only** (normative MUST NOT) |
| invalid wallet signature | **missing** |
| replayed nonce | **documentation only** (idempotency_key) |
| unsupported chain/asset | **missing** |
| provider/RPC outage | **missing** |
| settlement failure | outcome enum only |
| duplicate webhook | **documentation only** |
| refund failure | **missing** |
| emergency freeze / compromised wallet / revoked signer | **missing** |
| chain reorganization / rollback | **missing** |
| idempotency | field present; **untested** |

**CI workflows:** absent. **Package tests:** absent.

---

## 18. Domain boundary (recommended)

### PAY-PROTOCOL should own

- payment intent schema and lifecycle semantics
- spending-policy evaluation **contracts** (limits, thresholds, rail permissions)
- budgets/allowances as payment-domain policy objects (consuming Identity Authority IDs)
- treasury routing / wallet / custody **adapter interfaces**
- settlement / escrow / refund **adapter contracts**
- payment receipts (pay-specific) and mapping guidance to federation receipt/audit
- payment risk **interfaces** (not proprietary fraud models in public repo)

### Identity Authority should own

- principals, organizations, agent identity
- credentials, sessions, devices
- root mandates / delegation / root capabilities
- revocation authority and propagation
- ownership binding (including wallet **link** proofs as identity events)

### ATG should own

- normative authorization-message schemas shared across domains
- mandate/capability/receipt/revocation protocol contracts and conformance fixtures
- Trusted Authorization Path grammar (not payment execution)

### ONTOLOGY should own

- descriptive payment/money vocabulary referencing **canonical identity IDs**
- regulated-digital-money terms — not runtime authority

### Exact ownership boundary

```text
Identity Authority
  issues principal_id, agent_id, root mandate/delegation, revocation events
        │
        ▼
ATG (schemas/conformance)
  defines message shapes for mandate/capability/receipt/audit/revocation
        │
        ▼
PAY-PROTOCOL (this repo)
  binds payment intent + spending limits + approval + settlement adapter + pay receipt
  MUST reference Identity Authority IDs; MUST NOT mint root identity
        │
        ▼
Adapters (separate packages/repos)
  Stripe/Base/USDC/Monero/... execute under approved intent digest
```

---

## 19. Canonical suitability scores (0–5)

### As identity-authority candidate

| Dimension | Score | Rationale |
|---|---|---|
| identity clarity | 1 | Corridor mentions identity; no typed principal model |
| wallet separation | 3 | Strong exclusion of custody/keys; no wallet lifecycle |
| principal binding | 0 | Free-string only |
| agent spending controls | 1 | Schema limits; no enforcement |
| mandate integration | 2 | Pay Mandate schema exists; unbound to IdA |
| capability enforcement | 0 | No capability object/runtime |
| budget controls | 2 | Basic max amount/tx; no daily/chain/asset matrix |
| revocation | 1 | URI + status enum; no bus |
| chain portability | 4 | Agnostic by absence of hard deps |
| provider portability | 4 | Adapter names only |
| receipt coverage | 1 | Local receipt schema; not federation v1 |
| audit durability | 0 | No audit store |
| security | 2 | Good exclusions; no auth to harden |
| privacy | 2 | Privacy envelope draft; no runtime |
| test coverage | 0 | None |
| operational readiness | 0 | Not operable |

**Mean (identity lens):** ~1.4 / 5  

### As payment-protocol package (informational)

| Dimension | Score | Rationale |
|---|---|---|
| payment object clarity | 3 | Core objects present |
| normative authorization rules | 3 | Clear MUST rules; incomplete lifecycle docs |
| adapter contract maturity | 1 | README ops list only |
| SDK / conformance | 0 | Planned |
| implementability | 2 | Schemas usable as stubs |

---

## 20. Severity-ranked findings

### P0

1. **Misclassification risk:** README “identity” wording can imply this repo is Identity Authority — it is not.  
2. **Free-string principal/agent IDs** with no required binding to an external Identity Authority.  
3. **No revocation bus / fail-closed runtime** — normative fail-closed cannot be executed here.

### P1

1. Local receipt schema diverges from `agentropolis.receipt.v1` / `audit.v1` / `revocation.v1`.  
2. Spending authority incomplete (no daily/chain/destination/asset matrices; wildcard merchant semantics unspecified).  
3. Adapter/SDK directories are empty while README maps imply more completeness.

### P2

1. README lists `authorization-flow.md` and `lifecycle.md` that are absent.  
2. Approval `signature` is an unconstrained string (algorithm/issuer/audience undefined).  
3. Privacy Monero profile could be misread as embedded custody (text says otherwise; keep explicit).

### P3

1. Schema `$id` uses `agentropolis.example` placeholder host.  
2. Example IDs (`human_neuro`) are fine synthetics but should cite Identity Authority as source of truth in comments.

---

## 21. Final status recommendation

| Field | Value |
|---|---|
| Actual payment role | payment protocol specification + schema-only package + documentation-only package |
| Actual identity role | **empty shell** (payment-domain string references only) |
| Identity-domain status | **EMPTY-SHELL** |
| Payment-domain status | **INCUBATING** |
| Canonical identity? | **No** |
| Canonical payment authority runtime? | **No** (spec incubating) |
| Confidence | **high** |
| Codex / follow-on required? | **YES** — designate Identity Authority; bind PAY schemas to canonical IDs; assemble hub registry with SOVEREIGNTY + ATG + ONTOLOGY + PAY |

**Do not** register PAY-PROTOCOL as the canonical identity authority.  
**Do** register it as an incubating payment-protocol schema surface that **consumes** identity from a separate Identity Authority.

---

## Answer to the control question

> Does PAY-PROTOCOL implement root identity, or only payment-domain identity references?

**Only payment-domain identity references** (`principal_id`, `delegate_id`, `agent_id`, `decided_by` as free strings), plus payment objects (mandate limits, payment intent, approval, receipt, privacy settlement envelope). It does **not** implement root identity, credentials, sessions, wallet-link identity, or revocation authority.
