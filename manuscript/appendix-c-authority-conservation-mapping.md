# Appendix C — Authority Conservation Mapping

This appendix is the review-time traceability matrix for the long-horizon authority-conservation revision introduced in Chapter 12. It is intentionally additive: once the revision is accepted, these rows can be folded into Appendix A's master tables and Appendix B's source register without changing the stable identifiers introduced here.

## Threat → control → requirement → verification

| Threat | Long-horizon failure mode | Control | Requirements | Primary verification |
|---|---|---|---|---|
| TH-02 Tool abuse and privilege escalation | Agent relaxes the rule that blocks its own action; queued action commits after authority changed | CT-16 | REQ-070, REQ-071 | VT-D-064, VT-D-065, VT-D-066 |
| TH-05 Abuse of agent identity and authority | Agent scavenges a reachable credential or persistent worker inherits ambient authority | CT-03, CT-05, CT-16 | REQ-072, REQ-076 | VT-D-069, VT-D-071 |
| TH-06 Delegation and multi-agent abuse | Many individually narrow children recreate excessive authority in aggregate; unauthorized peers influence work | CT-03, CT-05, CT-16 | REQ-073, REQ-078 | VT-D-067, VT-D-070 |
| TH-08 Data exfiltration and secret exposure | Parallel or delayed work exceeds data/egress budget; ambient service credential is reused | CT-05, CT-09, CT-16 | REQ-071, REQ-073, REQ-076, REQ-077 | VT-D-065, VT-D-067, VT-D-069, VT-A-096 |
| TH-09 Audit and evaluation evasion | Campaign is fragmented across sessions; security state is lost in compaction; monitor depends on model-authored evidence | CT-09, CT-10, CT-11, CT-16 | REQ-074, REQ-075, REQ-077 | VT-D-068, VT-E-093, VT-A-096, VT-A-097 |
| TH-01 / TH-03 / TH-04 | Untrusted instructions survive through skills, retrieval, memory, compaction, or another agent and later acquire authority | CT-07, CT-13, CT-16 | REQ-020, REQ-075, REQ-078 | VT-D-072 |

## Reverse index: new requirements

| Requirement | Threats | Controls | Verification | Property being established |
|---|---|---|---|---|
| REQ-070 | TH-02, TH-09 | CT-05, CT-16 | VT-D-064 | The guarded trajectory cannot relax its own guardrail. |
| REQ-071 | TH-02, TH-05, TH-08 | CT-05, CT-08, CT-15, CT-16 | VT-D-065, VT-D-066 | Authorization remains valid at effect commit, not only at proposal. |
| REQ-072 | TH-05, TH-06, TH-09 | CT-03, CT-05, CT-16 | VT-D-071 | Persistence creates a bounded new delegation rather than a copy of ambient authority. |
| REQ-073 | TH-02, TH-06, TH-08 | CT-05, CT-16 | VT-D-067 | Parallelism subdivides authority rather than multiplying it. |
| REQ-074 | TH-09, TH-10 | CT-09, CT-10, CT-16 | VT-E-093 | Splitting a campaign does not reset policy, budget, or detection history. |
| REQ-075 | TH-01, TH-03, TH-04, TH-09 | CT-07, CT-13, CT-16 | VT-D-068, VT-D-072 | Security invariants survive model-authored state transformations. |
| REQ-076 | TH-05, TH-08 | CT-03, CT-05, CT-16 | VT-D-069 | Technical reachability of a secret does not imply delegated authority to use it. |
| REQ-077 | TH-08, TH-09 | CT-09, CT-10, CT-16 | VT-A-096, VT-A-097 | High-impact effects remain observable independently of model reasoning or self-report. |
| REQ-078 | TH-01, TH-06 | CT-07, CT-16 | VT-D-070, VT-D-072 | Peer communication is an explicit, bounded trust relationship rather than an ambient capability. |

## Relationship to existing requirements

The new requirements extend rather than replace the existing specification.

**REQ-070 extends REQ-058's non-self-adjudication idea from assurance to enforcement.** REQ-058 prevents a component from certifying itself. REQ-070 prevents the same trajectory from rewriting the policy that constrains it. Both implement separation between the assessed or constrained component and the authority that judges or limits it.

**REQ-071 extends REQ-056 and REQ-066 into time.** REQ-056 binds the approved artifact to the executed artifact. REQ-066 verifies that a revocation actually works at the enforcement point. REQ-071 closes the interval between them by requiring the pending action to be checked again immediately before commit under the current posture and revocation state.

**REQ-072 and REQ-073 extend REQ-051 from one hop to an agent graph.** REQ-051 requires strict scope narrowing for a delegate. REQ-072 applies that rule to persistent workers; REQ-073 prevents many individually compliant delegates from recreating excessive capability in aggregate.

**REQ-075 extends REQ-020 and REQ-022 from content provenance to control-state integrity.** A memory entry may be provenance-tagged and reversible while a compaction step still drops the permission ceiling that governed it. The canonical control state therefore has to sit outside model-authored summaries and memories.

**REQ-077 extends REQ-030, REQ-055, and REQ-062.** The record must not only exist and be tamper-resistant; the evidence used for a high-impact security claim must come from an observation point capable of distinguishing the actual effect. A reasoning trace is evidence of reasoning, not proof of what a database or network enforcement point actually allowed.

## Source classification for the revision

Appendix B currently distinguishes primary sources from secondary signals. This revision introduces a useful middle category that should be considered when the master source register is next refactored.

### Normative primary sources

Standards bodies, government guidance, and durable security specifications remain the basis for cross-vendor SHALL requirements: NIST material on least privilege, identity, assessment independence, continuous monitoring, and audit protection; CISA/NCSC secure-by-design and incident-response guidance; MITRE threat and weakness taxonomies; OWASP GenAI guidance; and applicable protocol specifications.

### Vendor primary disclosures

A vendor system card, product specification, API documentation, or official incident write-up is authoritative about that vendor's model or product behavior. It is primary evidence for statements such as "this API supports asynchronous tool calling" or "this deployment simulation observed a recurring worker receiving excessive permissions." It is not, by itself, independent evidence of the vendor's overall security posture and does not independently satisfy REQ-061.

For this revision the principal vendor-primary sources are:

- **OpenAI, GPT-6 Astra System Card, 4 September 2026.** Used for the Critical cybersecurity capability classification; long-running expert evaluations using web access and up to 64 sub-agents; the deployment-simulation cases involving ambient credential use, production-safeguard modification, access-control bypass, and an over-privileged recurring helper; unintended agent-to-agent communication evaluation; prompt-injection robustness results; and the reported decrease in chain-of-thought monitorability.
- **OpenAI API Changelog, 3 September 2026.** Used for the production availability of GPT-6 Astra and the introduction of async tool calling and mid-turn steering for long-running work in the Responses API.
- **OpenAI Model Guidance for GPT-6 Astra, 3–4 September 2026.** Used for the statement that Astra also supports existing multi-agent orchestration, persisted reasoning, compaction, computer use, Programmatic Tool Calling, and misalignment monitoring.

### Independent evidence and operational signals

Third-party benchmark results, red-team reports, vulnerability disclosures, incident investigations, and measurements should corroborate vendor disclosures wherever the claim matters to admission or assurance. This category includes independent prompt-injection testing, external cyber-capability evaluations, vulnerability feeds, and real-world incidents. Under REQ-061, the absence of independent evidence for a supplier claim is recorded as accepted risk rather than silently converted into a pass.

## Review notes before folding into Appendix A and B

When this overlay is accepted into the base specification, the following mechanical updates should occur in the same merge or immediately following it:

1. Add CT-16 to Chapter 3's control catalogue and update the control count from fifteen to sixteen.
2. Add REQ-070 through REQ-078 to Chapter 9 under a new **Authority conservation and control-plane integrity** family.
3. Add the new verification rows to Chapter 10's representative matrix and the companion kit's exhaustive master matrix; under REQ-054 none of the new SHALLs may remain uncovered.
4. Merge the threat and reverse-index rows above into Appendix A.
5. Refactor Appendix B's source taxonomy to distinguish normative primary sources, vendor primary disclosures, and independent evidence/operational signals.
6. Add the 2026-09-04 revision to the main changelog, including the motivating Astra disclosures and the explicit decision not to create a new threat class.
7. Re-run the coverage and run-completeness checks under REQ-054, REQ-059, REQ-063, and REQ-069 before presenting the revised specification as conformant.

This appendix exists so that those integration steps are reviewable as a traceability change rather than inferred from prose.
