# The Requirement Specification

## Why controls have to become requirements

A control is a good intention until it is written as a requirement someone can audit. "We review tool permissions" describes an aspiration; "REQ-010: every tool available to an agent SHALL be on an explicit allowlist" describes a claim that is either true or false of your system on any given day. The move from the first sentence to the second is the whole purpose of this chapter. Part II told you what controls to build and why; this chapter restates those controls as normative requirements, in language precise enough that the verification plan in Chapter 10 can attach a pass-or-fail test to each one.

The requirements carry stable identifiers, REQ-000 through REQ-054, and each traces up to the threats and controls that justify it and down to the tests that prove it. This chapter presents them by family, with a table per family giving the identifier, the RFC 2119 level, and the statement. The tables are the normative core of the book; the surrounding prose explains how to read them.

## RFC 2119, used honestly

The requirements use three keywords from RFC 2119, and the book uses them to mean exactly what the standard says, no more and no less.

**SHALL** marks an absolute requirement. If a SHALL is not met, the system is non-conformant — there is no "mostly." SHALL is reserved for controls whose absence is a genuine security failure, and every SHALL in this specification is testable, because an untestable mandatory requirement is a claim no one can check.

**SHOULD** marks a strong recommendation: do this unless you have a specific, articulated reason not to, and record the reason. SHOULD is used where the control is real but the right implementation is genuinely context-dependent — appropriate TTLs, useful approval-fatigue thresholds — so that the obligation is to make a deliberate decision rather than to hit a universal number.

**MAY** marks an option that is permitted but not expected, included so that a conformant implementation is not accidentally forbidden from doing something reasonable. No requirement in this revision is stated as a MAY — the keyword is defined for completeness and because future revisions will use it to bless optional hardening without mandating it; where this specification has nothing to require, it says SHALL or SHOULD, and where it would only permit, it stays silent rather than manufacturing a hollow option.

The discipline the book imposes on itself is stated as a requirement in its own right. **REQ-054** (a SHOULD) holds that every SHALL requirement must map to at least one verification test, so that mandatory means verifiable by construction. A SHALL with no test is decoration, and decoration masquerading as a control is worse than an honest gap, because it earns trust it has not paid for. This is the rule that makes Chapter 10 a necessity rather than an appendix.

## Scope

The specification begins by bounding itself, because a requirement set that does not say what it covers cannot be conformed to.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-000 | SHALL | The requirements apply to any system where a language model can take actions through tools — calling APIs, running code, sending messages, moving funds, or changing production — on behalf of a principal. |
| REQ-000B | SHALL | Each deployment SHALL record which requirements apply, which are out of scope, and the justification, so that conformance is assessed against a declared boundary rather than an assumed one. |

## Identity and delegation (TH-05, TH-06)

This family makes the agent a distinct, scoped, auditable principal and stops delegation from laundering permissions. It is the subject of Chapter 4.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-001 | SHALL | An agent SHALL NOT directly share a human user's standing credentials, and SHALL operate under an agent or workload identity whose purpose, environment, and scope are explicit. |
| REQ-002 | SHALL | Any delegated authority SHALL carry a purpose, a duration, a target resource, an allowed operation set, an approver, and a revocation condition. |
| REQ-003 | SHALL | High-risk privileges (production change, permission change, external send, payment, deletion, secret access) SHALL be separated from the reasoning context and granted narrowly and just before use rather than held ambient across a session. |
| REQ-013 | SHALL | When work is delegated to a child or external agent, the parent's policy, permission ceiling, prohibited operations, audit identifier, and approval requirements SHALL NOT be lost. |
| REQ-051 | SHALL | Inter-agent delegation SHALL NOT pass parent credentials through unchanged; it SHALL issue a new credential scoped strictly below the parent's, with its own expiry, and the whole chain SHALL be auditable under one correlation identifier. |

## Tool and action safety (TH-02, TH-07)

This family constrains the surface where authority becomes action, including the MCP supply chain. It is the subject of Chapter 5.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-010 | SHALL | Every tool available to an agent SHALL be on an explicit allowlist, and network-capable, file-writing, code-executing, or fund-moving tools SHALL be classified by risk. |
| REQ-011 | SHALL | Each tool's permissions SHALL be minimized to its function, and its inputs SHALL be validated against a schema before execution. |
| REQ-012 | SHALL | Code execution, browsing, and file operations SHALL run in a sandbox isolated from production, credentials, and the network by default, with outbound access only to declared destinations. |
| REQ-014 | SHALL | External services, plugins, and MCP providers SHALL be subject to a vendor risk assessment covering responsibility boundary, data handling, auditability, and vulnerability response before use. |
| REQ-015 | SHALL | High-impact actions SHALL present the concrete effect — diff or payload, destination, privilege exercised, data classification, and rollback condition — for explicit approval before execution. |
| REQ-050 | SHALL | MCP servers SHALL be admitted only from a vetted allowlist; tool definitions SHALL be version-pinned and hashed, and any change to an approved definition SHALL be detected and re-reviewed before use. |
| REQ-053 | SHOULD | Approval-gate effectiveness (approval rate, latency, override frequency) SHOULD be measured, and a rubber-stamp pattern SHOULD be treated as a defect in the gate's design. |

## Untrusted content, RAG, and memory (TH-01, TH-03, TH-04)

This family governs the content that steers the agent's reasoning. It is the subject of Chapter 6.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-020 | SHALL | Content entering the model from retrieval, tools, or memory SHALL be labeled with its source and trust level, and labeled-untrusted content SHALL NOT be able to override system instructions, developer policy, or tool schemas. |
| REQ-021 | SHOULD | Retrieved and remembered content SHOULD carry a time-to-live and a freshness signal, so that stale content is re-validated or expired rather than trusted indefinitely. |
| REQ-022 | SHALL | Writes to long-term memory, profiles, and shared context SHALL be provenance-tagged, subject to policy, and reversible. |
| REQ-052 | SHOULD | Memory entries SHOULD carry structured metadata (origin, trust level, TTL) so they are subject to the same expiry and re-validation as retrieved content. |

## Monitoring, evaluation, and incident response (general; TH-09, TH-10 abuse)

This family makes the running agent observable, continuously tested, and recoverable. It is the subject of Chapter 7.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-030 | SHALL | The system SHALL log end to end — input, model response, tool calls and results, acting identity, approvals, data access, external sends, and final output — sufficient to reconstruct an incident, with secrets redacted. |
| REQ-031 | SHALL | Agent-specific signals (anomalous tool use, unusual permission requests, undeclared external sends, injection indicators, widening delegation) SHALL be monitored and alerted on. |
| REQ-032 | SHALL | Monitoring SHALL integrate vulnerability and threat intelligence (CVE, KEV, EPSS, relevant feeds) for the model, framework, and tool dependencies. |
| REQ-033 | SHALL | Evaluation covering the threat families SHALL run continuously and repeatably — before launch, on change, and on a schedule — with failing cases retained and re-run. |
| REQ-034 | SHALL | Rollback and recovery SHALL be exercised, not merely designed, including state restore, memory-contamination revert, tool-version rollback, and credential-revocation validation. |
| REQ-035 | SHALL | A defined incident-response procedure SHALL be able to stop the agent, revoke credentials at capability granularity, isolate and preserve memory and logs, scope the blast radius across delegation, and notify the owner; the same path SHALL support runtime abuse blocking for public-facing misuse. |

## Runtime security posture (general)

This family provides the fast, provisional loop that degrades capability under pressure. It is the subject of Chapter 8.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-040 | SHALL | The system SHALL implement a runtime posture state machine (normal, watch, provisional_guard, restricted, emergency_block, revised_baseline) governing capability. |
| REQ-041 | SHALL | Posture transitions SHALL be driven by classified triggers, each with a target posture and a response-time SLA. |
| REQ-042 | SHALL | A granular kill switch SHALL be able to revoke individual capabilities (tool, MCP server, external send, memory write, RAG, delegation, scheduled run, agent unit) independently. |
| REQ-043 | SHALL | Every runtime action SHALL be bound to the specification and posture policy version in force when it occurred. |
| REQ-044 | SHALL | Every trigger, posture change, and degradation SHALL be recorded in an append-only runtime signal queue. |
| REQ-045 | SHALL | Runtime signals SHALL be reconciled — promoted, encoded as a conditional rule, dismissed as false positive, merged, or carried as incident follow-up — before a temporary posture becomes the revised baseline. |
| REQ-046 | SHALL | The runtime loop SHALL only reduce capability, never expand it. |
| REQ-047 | SHALL | Return to normal or to a revised baseline SHALL occur only through slow-loop review, not silently. |

## Governance and compliance (general)

This family carries the accountability and oversight record. It is the subject of Chapter 11.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-048 | SHALL | Each agent SHALL have documented governance: use cases, prohibited uses, accountable owner, accepted risks, exceptions, and applicable obligations. |
| REQ-049 | SHALL | High-risk determinations SHALL be recorded, with the human-oversight design and the review record that supports the deployment decision. |

## How to read the tables as a whole

Two reading habits make the specification useful rather than ornamental. First, read each requirement together with its threat and its test: a requirement in isolation is an assertion, but a requirement traced up to TH-xx and down to VT-xx is a link in an auditable chain, and the chain is the product. Appendix A holds that full traceability. Second, treat the SHALL/SHOULD split as information, not decoration — a SHOULD is the book telling you honestly that it does not know your context well enough to mandate a specific choice, only that you must make one and record it. Nothing here is a mandate for its own sake; every line answers a named threat and terminates in a named test. The next chapter is where those tests live.
