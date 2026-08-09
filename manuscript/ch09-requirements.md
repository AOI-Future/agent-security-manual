# The Requirement Specification

## Why controls have to become requirements

A control is a good intention until it is written as a requirement someone can audit. "We review tool permissions" describes an aspiration; "REQ-010: every tool available to an agent SHALL be on an explicit allowlist" describes a claim that is either true or false of your system on any given day. The move from the first sentence to the second is the whole purpose of this chapter. Part II told you what controls to build and why; this chapter restates those controls as normative requirements, in language precise enough that the verification plan in Chapter 10 can attach a pass-or-fail test to each one.

The requirements carry stable identifiers, REQ-000 through REQ-069, and each traces up to the threats and controls that justify it and down to the tests that prove it. This chapter presents them by family, with a table per family giving the identifier, the RFC 2119 level, and the statement. The tables are the normative core of the book; the surrounding prose explains how to read them.

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
| REQ-056 | SHALL | The artifact presented for approval under REQ-015 and the artifact executed after approval SHALL be the same object, bound by a content digest that is recorded in the audit trail; where the payload at execution does not match the approved digest, execution SHALL fail rather than proceed. |
| REQ-061 | SHALL | A supplier's own statement about its security posture SHALL NOT by itself satisfy the assessment required by REQ-014; the statement SHALL be recorded as a claim, and admission SHALL depend on at least one item of evidence the supplier does not author — an independent audit report, a third-party attestation, a signed build provenance record, or the operator's own test result — with the absence of such evidence recorded as an accepted risk rather than as a pass. |

## Untrusted content, RAG, and memory (TH-01, TH-03, TH-04)

This family governs the content that steers the agent's reasoning. It is the subject of Chapter 6.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-020 | SHALL | Content entering the model from retrieval, tools, or memory SHALL be labeled with its source and trust level, and labeled-untrusted content SHALL NOT be able to override system instructions, developer policy, or tool schemas. |
| REQ-021 | SHOULD | Retrieved and remembered content SHOULD carry a time-to-live and a freshness signal, so that stale content is re-validated or expired rather than trusted indefinitely. |
| REQ-022 | SHALL | Writes to long-term memory, profiles, and shared context SHALL be provenance-tagged, subject to policy, and reversible. |
| REQ-052 | SHOULD | Memory entries SHOULD carry structured metadata (origin, trust level, TTL) so they are subject to the same expiry and re-validation as retrieved content. |
| REQ-065 | SHALL | The correctness of the labeling required by REQ-020, and of the tool risk classification required by REQ-010, SHALL be measured against a labeled reference set held outside the system that performs the labeling, with misclassification in the direction of greater trust — untrusted content labeled trusted, a high-risk tool classified low — counted and recorded separately from the aggregate error; the operator SHALL declare the accuracy level it accepts and the metric used to express it, and this specification does not mandate a value. |

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
| REQ-055 | SHALL | The record required by REQ-030 SHALL be written to a store that the agent, its tools, and its orchestration cannot retroactively alter — append-only, with retention enforced under an identity separate from the agent's — and SHALL be replicated out of band to a system outside the agent's blast radius, so that the integrity of the record does not depend on the good behaviour of the system it records. |
| REQ-057 | SHALL | The evaluation required by REQ-033 SHALL carry a known-failing control case on every run, and a run in which that case does not fail SHALL be treated as a failed run rather than a passing one, so that a silently broken harness cannot be read as a clean result. |
| REQ-059 | SHALL | The set of items an evaluation or coverage report is scored against SHALL be defined outside the system under test and referenced by version, so that the denominator of any "n of n" claim is not chosen by the thing being measured; and a check whose input is absent, empty, null, or errored SHALL be recorded as a failure or as not-run, and SHALL NOT be rounded to a passing value or omitted from the total. |
| REQ-060 | SHOULD | Evidence records SHOULD be written to an append-only log whose inclusion can be proved to a party outside the reporting system, so that a report which is true in everything it states but incomplete in what it chose to state can be detected by an independent monitor rather than only by the system that produced it. |
| REQ-062 | SHALL | Where conformance is assessed by measuring a proxy for the property a requirement is about rather than the property itself — the presence of a mechanism, the completion of a run, the count of executed checks — the proxy and the property it stands for SHALL both be named in the verification record, and at least one test SHALL exist that fails when the proxy holds and the property does not. The expected result of that test SHALL be held outside the system under test. |
| REQ-063 | SHALL | A verification, evaluation, or coverage report SHALL distinguish what was checked from what was found, and SHALL state what the run could not have detected — checks not selected, not applicable, or not run, and conditions outside the reach of the method used. A report that states only its findings SHALL NOT be read as a statement about its scope. |
| REQ-064 | SHALL | The monitoring required by REQ-031 SHALL be exercised on a defined interval by a production-safe signal designed to trigger it, and a monitored source that has delivered no input for longer than its declared expected interval SHALL be raised as a failure of the monitoring rather than read as an absence of events. |
| REQ-067 | SHALL | Every check in an evaluation, monitoring, or verification run SHALL declare the claim it is intended to establish and the oracle by which it discriminates that claim — transport (a response was returned), syntactic (the response is well-formed), schematic (the response conforms to a declared schema), semantic (the response is correct against an external reference), or intent (the response satisfies the requester's purpose) — and the claim recorded for a check SHALL NOT exceed what its oracle can discriminate; a well-formed response SHALL NOT be recorded as a correct one unless a semantic oracle or stronger established it. |
| REQ-069 | SHALL | Every automated evaluation, verification, or coverage run SHALL reconcile the versioned target set required by REQ-059 against execution-boundary evidence retained by a collector the reporting layer cannot alter. Each target SHALL have a unique stable identifier or collision-resistant digest matching the target set, exactly one terminal processing outcome — analyzed, processing-error, or not-run — a run or correlation identifier, and a timestamp. The report SHALL state the total-target, analyzed, processing-error, and not-run counts, SHALL reference the retained record, and the three outcome counts SHALL equal the total-target count. A missing, extra, or duplicate target; count mismatch; timeout; unhandled exception; loss of execution evidence; or any processing-error or not-run outcome SHALL make the run failed or incomplete. A run SHALL NOT be presented or accepted as PASS or as a complete zero-finding result unless every target has the analyzed outcome and the reconciliation has no unexplained remainder. |

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
| REQ-066 | SHALL | A capability revoked by the kill switch (REQ-042) or withdrawn by a posture transition (REQ-040, REQ-041) SHALL be confirmed unusable by attempting the revoked action at the enforcement point after the transition, and both the attempt and its refusal SHALL be recorded; a posture change recorded as applied but not confirmed in effect SHALL NOT be counted as a completed transition. |

## Governance and compliance (general)

This family carries the accountability and oversight record. It is the subject of Chapter 11.

| REQ | Level | Statement |
|-----|-------|-----------|
| REQ-048 | SHALL | Each agent SHALL have documented governance: use cases, prohibited uses, accountable owner, accepted risks, exceptions, and applicable obligations. |
| REQ-049 | SHALL | High-risk determinations SHALL be recorded, with the human-oversight design and the review record that supports the deployment decision. |
| REQ-058 | SHALL | An artifact that asserts conformance to a requirement SHALL NOT be generated, scored, or adjudicated by the component that implements that requirement; the verdict SHALL rest on artifacts the assessed component cannot write, and SHALL be issued under an identity separate from the assessed system's, so that no component certifies itself. Where organizational scale makes full separation impracticable, the assessing role SHALL be held by a party that does not report to the owner of the assessed system, the arrangement SHALL be recorded as an accepted risk, and the assignment SHALL be rotated on a defined interval. |
| REQ-068 | SHALL | Every accepted finding against the deployed specification or its verification plan SHALL result in both a new or amended verification test and a changelog entry that references the finding, so that a lesson once learned is bound to a test that fails if it is unlearned; a finding closed without a corresponding test SHALL be recorded with the reason the lesson does not generalize. |

## How to read the tables as a whole

Seven reading habits make the specification useful rather than ornamental. First, read each requirement together with its threat and its test: a requirement in isolation is an assertion, but a requirement traced up to TH-xx and down to VT-xx is a link in an auditable chain, and the chain is the product. Appendix A holds that full traceability. Second, treat the SHALL/SHOULD split as information, not decoration — a SHOULD is the book telling you honestly that it does not know your context well enough to mandate a specific choice, only that you must make one and record it.

Third, wherever a requirement calls for an artifact — a log, an approval record, an evaluation result — read it together with the requirement that the artifact be *true*. Requiring a thing to exist and requiring its contents to be correct are different obligations, and a specification that only ever asks the first question can be satisfied in full by a system that is lying. That is why REQ-030 is paired with REQ-055, REQ-015 with REQ-056, and REQ-033 with REQ-057: presence is the cheap half of the check, and an adversary who has reached the point of producing evidence about themselves (TH-09) will produce evidence that is present.

Fourth, ask of every artifact who wrote it, against what list, and what was allowed to be left out. A true statement can still be a misleading report. REQ-058 forbids a component from adjudicating its own conformance, because a verdict issued by the thing being judged carries no more information than the thing's own opinion of itself; this is the same separation that NIST SP 800-53 requires of assessors in CA-2(1) and of audit storage in AU-9(2), that ISO/IEC 42001 clause 9.2 requires of internal auditors, and that the EU AI Act requires of notified bodies in Article 31. REQ-059 governs the denominator: "six of six" is a claim about the numerator only, and it is worthless if the system under test also chose the six — the reference set has to come from outside, versioned, the way SLSA requires build provenance to be generated by the control plane rather than by the tenant it describes. REQ-059 also closes the quieter failure in which an absent, empty, or errored result is rounded to a passing value instead of being counted as a failure. REQ-060 addresses selective omission, where every sentence in a report is true and the report is still false by what it excludes; the answer is the Certificate Transparency answer, an append-only log with inclusion proofs that an independent monitor can pull, rather than trusting the reporting system's account of its own completeness. And REQ-061 applies the same rule to suppliers, whose self-declarations are claims until something they did not author corroborates them.

Fifth, ask what each test actually measures, and whether that is the thing the requirement is about. Nearly all verification measures a stand-in: the allowlist is present, the labeler ran, the evaluation completed, the posture flipped. Standing in is not a defect — a property you cannot observe directly has to be approached through something you can — but it is a substitution, and substitutions come apart. A labeler that runs on every document and gets half of them wrong passes a presence check. A monitor whose input stopped arriving reports no anomalies, which reads on a dashboard exactly like a quiet week. A kill switch that flips a flag no enforcement point consults produces a clean transition record and no revocation. In each case the proxy passed without the thing it stood for. **REQ-062** requires the substitution to be declared and to have a test that fails when it comes apart; **REQ-063** requires a report to say what it could not have seen, so that silence is not read as absence; **REQ-064** applies to production monitoring the liveness check REQ-057 already applies to evaluation; **REQ-065** asks the labeling and classification requirements for their error rate rather than their existence; and **REQ-066** asks a revoked capability to demonstrate that it is actually unusable. None of them mandates a number. What accuracy is good enough, and what interval is right for your traffic, is a decision this book cannot make for you and will not pretend to — only one you must make, declare, and be held to, which is the same stance the EU AI Act takes in Article 15(3) when it requires accuracy levels and metrics to be declared without prescribing values.

Sixth, ask of every check what claim it is entitled to make. A check is a claim plus an oracle — a thing asserted and a mechanism that can tell whether the assertion is false — and oracles come in strengths: transport, which knows only that a response came back; syntactic, which knows it was well-formed; schematic, which knows it matched a declared shape; semantic, which knows it was correct against a reference the system under test does not control; and intent, which knows it satisfied the purpose behind the request. A 200 with a well-formed body and the wrong half of the data passes the first three and fails the last two, and a check that reports "no findings" on the strength of a transport oracle is telling the truth about far less than its reader will assume. The temptation this habit resists is enumeration — adding a named check for each newly discovered way responses can be wrong. The failure modes are unbounded and the list would always be one incident behind; the generative rule is not. **REQ-067** requires every check to declare its claim and its oracle and forbids the claim to exceed the oracle's discriminating power, which covers the variant nobody has seen yet as well as the ones everyone has — the same distinction SARIF draws when `invocation.executionSuccessful` is recorded separately from the results a run carries.

Seventh, ask whether the run finished over the set it claims to cover. A canary proves the harness can still fail; a scope statement says what was selected; neither proves that every selected target reached a terminal state. That proof comes from reconciliation: the versioned target set on one side, per-target terminal events retained outside the reporting layer on the other, and no missing, extra, or duplicate target between them. **REQ-069** makes PASS and a complete zero-finding conclusion conditional on that reconciliation and treats timeouts, exceptions, count mismatches, and missing execution evidence as failed or incomplete runs. Partial findings may survive; the claim of completeness does not. **REQ-068** then closes the loop the other way, the discipline NIST SP 800-53 CA-7 calls continuous monitoring: every accepted finding against this specification becomes a test and a changelog line, so the specification's own improvement is subject to the same discipline it imposes. Nothing here is a mandate for its own sake; every line answers a named threat and terminates in a named test. The next chapter is where those tests live.
