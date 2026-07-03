# Verification and Testing

## Turning SHALL into pass or fail

A requirement you cannot test is an opinion with a serial number. Chapter 9 was written so that every SHALL is verifiable; this chapter is where the verification actually lives. Its job is to take each requirement and attach at least one test that returns an unambiguous pass or fail, so that "we conform" stops being a statement of intent and becomes a result you can show someone who does not trust you. This is the last link in the chain the book has enforced since Chapter 1 — Threat, Control, Requirement, Verification — and it is the link that makes the other three honest.

The plan is built to survive contact with an adversary who is specifically trying to make evaluation lie (TH-09). That means tests that run against production-representative conditions, tests that are repeatable rather than one-shot, and a record of results that cannot itself be tampered into a false pass. The chapter describes four verification methods, maps requirements to tests, states the operational rules, and then connects to the companion kit that executes the whole thing and produces a signed artifact.

## Four verification methods

Requirements fail in different ways, so they need different kinds of test. The plan uses four methods, each with a two-letter code that prefixes its test identifiers.

**VT-S — Static and structural verification.** Inspect configuration, policy, inventory, and code without running an attack: is the allowlist present, are tool definitions hashed, is the posture state machine wired, is the identity distinct from a human's. VT-S answers "is the control built and configured as required." It is cheap, deterministic, and the right method for requirements that are properties of the system's structure.

**VT-D — Dynamic adversarial verification.** Run actual attacks and observe behavior: fire prompt injections through retrieved content, attempt tool abuse, try to escalate privilege through delegation, attempt exfiltration. VT-D answers "does the control hold when someone pushes on it." It is the method for the injection and tool-abuse families, and it is where the red-team tooling is used.

**VT-E — Evidence and log verification.** Inspect the record the system produces: can the episode be reconstructed from logs, are secrets redacted, is the correlation ID threaded, is every runtime action bound to a policy version. VT-E answers "did the system record what it was supposed to, in a form an investigator can use." It verifies the audit layer that all incident response depends on.

**VT-A — Attestation and audit verification.** Confirm the higher-order properties: that the runtime posture actually transitioned on a trigger within its SLA, that reconciliation happened, that governance records exist, that the verification suite itself ran and was retained. VT-A answers "does the assurance process itself function as designed." It is the method for the runtime, governance, and meta-requirements.

The tool baseline supports these methods rather than defining them. A read-only posture scanner (**Hardshell v2**) performs the static VT-S sweep without itself holding write capability, which matters because a verification tool with production write access is a new attack surface. Adversarial testing draws on **PyRIT** (Microsoft's risk-identification toolkit) and **garak** (NVIDIA's LLM vulnerability scanner, v0.15.1) for the VT-D attack library. The tools are instruments; the methods are the contract.

## Requirement-to-test mapping

Every requirement maps to one or more tests, and the test's prefix tells you how it is checked. The table below is a *representative* slice of that mapping — enough rows to make the pattern legible, not the exhaustive matrix. The complete requirement-to-test master matrix, every SHALL bound to its full set of tests, is a living artifact: it grows each time a regression case is added, which is why it lives in the companion kit rather than being frozen onto a printed page. The kit both reports against these stable identifiers and enforces, through the coverage check (REQ-054), that no SHALL is ever left without a test.

| Requirement | Tests | Method(s) |
|-------------|-------|-----------|
| REQ-001 identity distinct | VT-S-001, VT-A-001, VT-A-002 | static, attestation |
| REQ-010 tool allowlist | VT-S-010 | static |
| REQ-011 scope / input validation | VT-D-010, VT-D-011 | dynamic |
| REQ-020 untrusted-content labeling | VT-S-020, VT-D-020, VT-D-021, VT-D-022, VT-D-023, VT-D-024 | static, dynamic |
| REQ-021 TTL / freshness | VT-S-021 | static |
| REQ-030 end-to-end logging | VT-S-030, VT-D-030, VT-D-031 | static, dynamic |
| REQ-040 posture state machine | VT-A-040, VT-A-041 | attestation |
| REQ-041 triggers / SLA | VT-D-040, VT-D-041, VT-E-040, VT-E-041 | dynamic, evidence |
| REQ-050 MCP vetting / pinning | VT-D-050, VT-S-050, VT-E-050, VT-E-051 | dynamic, static, evidence |
| REQ-051 delegation narrowing | VT-A-050, VT-A-051 | attestation |
| REQ-053 approval-gate metrics | VT-A-060, VT-A-061 | attestation |
| REQ-054 verification coverage | VT-A-070, VT-A-071 | attestation |

Read the pattern rather than memorizing the rows. Structural requirements (allowlists, labeling presence, TTL config) get VT-S. Behavioral requirements (does labeling actually stop an injection, does validation actually reject a bad argument) get VT-D, often several, because one attack proves little and a family of attacks proves the control generalizes. Record-integrity requirements get VT-E. And the process-level requirements — posture transitions, delegation narrowing, approval metrics, and coverage itself — get VT-A, because what they assert is that a *process* ran correctly, which you confirm by attestation over its outputs. The injection family carries the most dynamic tests (VT-D-020 through VT-D-024) precisely because it is the hardest to close and the easiest to regress. Rows not shown here — memory reversibility, rollback exercises, the runtime-posture attestations — follow the same rule: each is a dynamic, evidence, or attestation test in the kit's master matrix, and the coverage check refuses to pass if any SHALL among them has gone untested.

## Operational rules

The tests are only trustworthy if they are run and recorded under discipline. Four rules govern that.

Results are written to a dated report at `reports/<date>_verification_report.md`, so every run is a distinct, retained artifact rather than a mutable dashboard. The report records each test by identifier with a pass or fail and nothing more — no payloads, no transcripts, no captured data — because a verification report that quotes the attack it ran becomes a manual for that attack and a place for a secret to leak. Any failure is written to `data/change_queue.jsonl`, which is the same queue the continuous-update and runtime-reconciliation processes consume, so a failed test automatically becomes tracked work rather than a line someone hopes to remember. And secrets are never recorded in any test output: where a test must handle a credential, it appears as `[REDACTED]` plus a locator, consistent with the rule that holds everywhere else in the book. The point of these rules is that the verification process is itself hardened against the threats it verifies — it does not leak, it does not lie by omission, and its failures do not evaporate.

## Regression, not a gate

The single most important operational property is that this is a standing suite, not a launch checklist. Chapter 7 argued that evaluation has to run continuously because every model, tool, RAG source, prompt, or permission change can re-open a closed case; the verification plan is the machinery that makes that argument executable. Every failure that has ever been fixed stays in the suite as a permanent regression case, so a fix cannot silently rot and a change cannot quietly re-open an old hole without a red test to show for it. **REQ-054** closes the loop on the plan itself: the coverage check (VT-A-070, VT-A-071) verifies that every SHALL still maps to at least one test, so the suite cannot be hollowed out by adding requirements without adding tests. A verification plan that is not itself verified is just another unchecked claim.

## The companion kit and the signed artifact

Everything in this chapter can be done by hand, and the book tells you exactly what to check so that you can. What the book does not do — because a book cannot — is execute the suite and hand you evidence. That is the companion kit, sold separately, and this is the seam where the free explanation meets the paid tooling.

The kit runs the full method set: the Hardshell v2 static sweep, the PyRIT and garak adversarial battery, the log and evidence inspection, and the attestation checks. It applies the operational rules automatically — dated reports, failures routed to the change queue, secrets redacted — and it consolidates the whole run into a **single artifact: one JSON payload carrying every test identifier and its pass or fail, rendered to a PDF a CISO can read, and sealed with an RFC 3161 trusted timestamp.** The timestamp is the part that matters to someone who does not trust you: it proves the results existed at a specific time and have not been edited since, which turns "we tested this" from an assertion into a verifiable record. That single signed artifact is the concrete answer to Chapter 1's last question — *can we prove all of the above to someone who does not trust us* — and it is the deliverable an enterprise buyer or a regulator actually asks to see.

Verification is where the book's chain terminates and where its claims become checkable. What remains is the human accountability that surrounds the technical system — who owns the risk, who reviewed the decision, who is answerable when a control fails. That is governance, and it is the subject of the final chapter.
