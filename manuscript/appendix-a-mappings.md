# Appendix A — Cross-Reference Tables

This appendix carries the traceability that the body of the book keeps in the background. It exists so that you can walk the assurance chain in either direction: from a threat down to the tests that prove you have answered it, or from a failing test back up to the threat it leaves open. An empty control or requirement cell means an unaddressed threat or an ungoverned control — a real gap, and exactly what the tables are for. The verification column is different in kind: it lists the *in-book* tests defined in Chapter 10, which are a representative slice; the exhaustive requirement-to-test matrix is the living artifact maintained and enforced by the companion kit's coverage check (REQ-054). A thin VT cell here is therefore a pointer to the kit, not a confession of an unverified control.

## The master traceability table (TH → CT → REQ → VT)

Read a row left to right as a claim: *this threat is answered by these controls, which are made operable by these requirements, which are proven by these tests.* The identifiers are the stable ones used throughout the book (threats TH-01..TH-10 from Chapter 2, controls CT-01..CT-15 from Chapter 3, requirements REQ-000..REQ-054 from Chapter 9, tests VT-* from Chapter 10).

| Threat | Controls (CT) | Requirements (REQ) | Verification (VT) |
|--------|---------------|--------------------|--------------------|
| TH-01 Prompt injection (direct/indirect) | CT-07, CT-08, CT-11 | REQ-020, REQ-021, REQ-031, REQ-033 | VT-S-020, VT-D-020, VT-D-021, VT-D-022, VT-D-023, VT-D-024 |
| TH-02 Tool abuse / privilege escalation | CT-04, CT-05, CT-06, CT-08, CT-09, CT-15 | REQ-010, REQ-011, REQ-012, REQ-015, REQ-030, REQ-040, REQ-042, REQ-044 | VT-S-010, VT-D-010, VT-D-011 |
| TH-03 RAG / knowledge-base poisoning | CT-07, CT-11, CT-13 | REQ-020, REQ-021, REQ-033 | VT-S-020, VT-S-021, VT-D-020, VT-D-021, VT-D-022, VT-D-023, VT-D-024 |
| TH-04 Memory / context contamination | CT-07, CT-08, CT-13, CT-15 | REQ-022, REQ-052, REQ-034, REQ-042 | VT-D / VT-E (memory-reversibility and rollback exercise; enumerated in kit matrix) |
| TH-05 Agent identity / authority abuse | CT-02, CT-03, CT-05, CT-08 | REQ-001, REQ-002, REQ-003, REQ-013 | VT-S-001, VT-A-001, VT-A-002 |
| TH-06 Delegation / multi-agent abuse | CT-02, CT-03, CT-05 | REQ-002, REQ-013, REQ-051 | VT-A-050, VT-A-051 |
| TH-07 Supply-chain / MCP / plugin compromise | CT-04, CT-10, CT-13, CT-14 | REQ-014, REQ-050, REQ-032 | VT-S-050, VT-D-050, VT-E-050, VT-E-051 |
| TH-08 Data exfiltration / secret exposure | CT-05, CT-06, CT-07, CT-09 | REQ-003, REQ-011, REQ-012, REQ-030 | VT-D-010, VT-D-011, VT-S-030 |
| TH-09 Audit / evaluation evasion | CT-09, CT-10, CT-11 | REQ-030, REQ-031, REQ-033, REQ-043, REQ-054 | VT-E-040, VT-E-041, VT-A-070, VT-A-071 |
| TH-10 Model / service abuse | CT-01, CT-08, CT-10, CT-12 | REQ-035, REQ-048, REQ-049 | VT-A-060, VT-A-061 |

Three notes on this table. First, the runtime-posture requirements (REQ-040..REQ-047) and the logging requirement (REQ-030) recur across many threat rows rather than belonging to one, because they are cross-cutting: posture degradation and reconstructable logging answer the general case and reinforce the specific controls. Second, the source mapping register handed TH-10 a requirement identifier (REQ-016) that does not exist in the requirement specification; it is treated here as REQ-035, the incident-response and abuse-blocking requirement that actually carries the TH-10 abuse path. That substitution is flagged in the Changelog errata as a source correction to verify against the next specification revision. Third, TH-04's verification cell names test *methods* rather than in-book test identifiers: memory reversibility (REQ-022) and rollback demonstration (REQ-034) are proven dynamically and by evidence, and Chapter 10's representative table does not enumerate those specific tests — they live in the kit's master matrix. The cell says what class of test applies and where the exhaustive list is, rather than borrowing an unrelated static identifier.

## Requirement-to-threat index (REQ → TH)

The inverse index lets you start from a requirement — for instance when triaging a failed test — and recover the threats it defends, so you can judge what a failure actually exposes.

| Requirement | Threats answered |
|-------------|------------------|
| REQ-001, REQ-002, REQ-003, REQ-013 | TH-05 |
| REQ-051 | TH-05, TH-06 |
| REQ-010, REQ-011, REQ-012, REQ-015 | TH-02, TH-08 |
| REQ-014, REQ-050 | TH-07 |
| REQ-020, REQ-021 | TH-01, TH-03 |
| REQ-022, REQ-052 | TH-04 |
| REQ-030 | TH-02, TH-08, TH-09 (cross-cutting) |
| REQ-031, REQ-032, REQ-033 | TH-01, TH-02, TH-07, TH-09 |
| REQ-034, REQ-035 | TH-04, TH-10, incident recovery (cross-cutting) |
| REQ-040..REQ-047 | general case (cross-cutting) |
| REQ-048, REQ-049 | TH-10, governance (cross-cutting) |
| REQ-053 | TH-02, TH-05 (approval quality) |
| REQ-054 | TH-09 (assurance integrity) |

## Control-to-chapter index (CT → where it is treated)

Finally, a pointer from each control area to the chapter that develops it, so a reader who enters through this appendix can find the prose.

| Control | Primary chapter(s) |
|---------|--------------------|
| CT-01 Governance and risk | Chapter 11 |
| CT-02 Agent inventory | Chapter 4 (identity), Chapter 11 (record) |
| CT-03 Agent / workload identity | Chapter 4 |
| CT-04 Tool registry / allowlist | Chapter 5 |
| CT-05 Least privilege / separation of duties | Chapter 4, Chapter 5 |
| CT-06 Sandboxing / isolation | Chapter 5 |
| CT-07 Input/output content boundary | Chapter 6 |
| CT-08 Approval gates / HITL | Chapter 5, Chapter 11 |
| CT-09 Logging / audit trail | Chapter 7 |
| CT-10 Monitoring / detection / feeds | Chapter 7 |
| CT-11 Evaluation / red team | Chapter 7, Chapter 10 |
| CT-12 Incident response | Chapter 7 |
| CT-13 Lifecycle / change management | Chapter 6, Chapter 7 |
| CT-14 Vendor / external service risk | Chapter 5, Chapter 11 |
| CT-15 Runtime posture / capability degradation | Chapter 8 |

Together the three tables make the book navigable as a reference rather than only readable as a narrative. Start anywhere — a threat, a requirement, a control, a failing test — and the chain leads you to every other part it touches.
