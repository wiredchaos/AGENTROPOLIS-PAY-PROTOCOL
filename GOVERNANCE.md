# Protocol Governance

AGENTROPOLIS Pay Protocol is governed as a public interoperability surface, not as a substitute for the private runtime that enforces economic authority.

## Maintainer authority

Repository maintainers may merge, reject, revert, or defer changes to protect security, compatibility, legal boundaries, and the core principle that agents cannot create their own authority.

## Change classes

### Editorial

Typos, formatting, examples, and clarifications that do not change normative behavior.

### Additive

New optional fields, adapter guidance, or lifecycle evidence that does not invalidate conforming implementations.

### Breaking

Changes to required fields, signature semantics, state transitions, authorization meaning, or receipt verification. Breaking changes require a major protocol version.

### Emergency security

A maintainer may privately prepare and rapidly merge a security correction when prior disclosure would increase risk. Public rationale may follow after implementers have a reasonable mitigation window.

## Decision criteria

Protocol changes are evaluated against:

1. least authority
2. explicit consent and attribution
3. revocability
4. provider neutrality
5. deterministic validation
6. replay and duplicate resistance
7. audit evidence
8. privacy and data minimization
9. backward compatibility
10. operational implementability

## Proposal process

Significant changes should include:

- problem statement
- proposed normative behavior
- schema changes
- lifecycle changes
- security analysis
- compatibility plan
- example objects
- migration guidance

## Non-goals

Public governance does not grant access to or decision rights over private AGENTROPOLIS runtime components, vaults, commercial routing logic, production policies, brand assets, or confidential infrastructure.

## Final principle

Transparency applies to the rules, evidence, and interoperability contracts. Production enforcement machinery may remain private.
