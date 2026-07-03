# The Control Taxonomy

## From threats to a catalogue of moves

Chapter 2 named what can go wrong. This chapter names what you can do about it. The control taxonomy is a catalogue of fifteen control areas, CT-01 through CT-15, each of which is a class of defensive move rather than a specific product or config line. The identifiers are stable and they carry through the rest of the book: every control maps up to the threats it answers and down to the requirements (Chapter 9) that make it operable and the tests (Chapter 10) that prove it works.

A catalogue is not a program. You do not deploy all fifteen controls with equal weight on every agent; you deploy the ones that answer the threats your agent is actually exposed to, at the strength its risk tier justifies. The value of the taxonomy is that it forces the mapping to be explicit. A control with no threat behind it is cost you cannot justify. A threat with no control in front of it is risk you are silently carrying. The taxonomy is the ledger where both are visible.

## The four hardening surfaces

Chapter 1 ended with four attacker assumptions, and Chapter 2 observed that the ten threats' entry points collapse onto the same few surfaces. Those surfaces are the organizing spine of the defense, and every control in this chapter sits on one or more of them:

1. **Untrusted content reaching the model.** Everything the model reads is potentially an instruction, and most of it is not yours. The defense is to keep untrusted text from overriding trusted policy — content labeling, isolation of retrieved and remembered data, input/output boundaries.
2. **Tool capability the model can reach.** The agent's real power is the composition of its tools. The defense is to inventory that capability, minimize it, gate the dangerous parts, and sandbox execution.
3. **Identity and delegation.** Who the agent is, whose authority it borrows, and how that authority narrows as it is passed down a chain. The defense is distinct agent identity, scoped and short-lived credentials, and delegation that cannot launder permissions.
4. **The audit and assurance layer.** Whether you can reconstruct what happened and whether your verification reflects production. The defense is end-to-end logging, continuous evaluation, change management, and a runtime posture that degrades capability under uncertainty.

The rest of Part II is built on these four surfaces, one chapter at a time. This chapter lays out the whole catalogue so that the deep-dive chapters have a shared vocabulary to point back to.

## The fifteen control areas

**CT-01 — Governance and risk management.** Document the use cases, the prohibited uses, the accountable owner, the accepted risks, the exceptions, and the regulatory or contractual obligations. This is the surface no scanner sees; its absence is the precondition for every other failure. Answers the general case. *NIST AI 600-1; CISA/NCSC.*

**CT-02 — Agent inventory.** Register each agent with its owner, model, prompts, tools, data, identity, permissions, environment, and approval points. You cannot secure an agent you have not written down. Answers TH-05, TH-06, TH-07. *Microsoft; NIST NCCoE.*

**CT-03 — Agent and workload identity.** Give the agent an identity distinct from any human's: purpose-specific, environment-specific, short-lived, revocable, auditable. Answers TH-05, TH-06. *NIST NCCoE; Microsoft Entra Agent ID.*

**CT-04 — Tool registry and allowlist.** Track each tool's owner, schema, version, risk class, and its network, file, financial, and code-execution capability, and admit only what is on the allowlist. Answers TH-02, TH-07. *Microsoft; OWASP; CSA.*

**CT-05 — Least privilege and separation of duties.** Separate read, write, delete, external send, permission change, production change, and code execution, and delegate each only when needed and only for as long as needed. Answers TH-02, TH-05, TH-08. *CISA; NIST NCCoE; Microsoft.*

**CT-06 — Sandboxing and execution isolation.** Isolate the browser, the code executor, the file system, the network, and credentials, so that a dangerous operation runs where it can do the least harm. Answers TH-02, TH-07, TH-08. *Anthropic; Microsoft; CISA.*

**CT-07 — Input/output and content boundaries.** Label untrusted content and design the system so it cannot override system or developer instructions, policy, or tool schemas. This is the primary defense against the injection family. Answers TH-01, TH-03, TH-04. *OWASP LLM01; Anthropic; Microsoft.*

**CT-08 — Approval gates and human-in-the-loop.** For high-impact actions, present the diff, the destination, the rationale, and the risk, and require explicit approval or two-person control before proceeding. Answers TH-02, TH-05, TH-08, TH-10. *Microsoft; CISA; vendor safety docs.*

**CT-09 — Logging and audit trail.** Record input, model response, tool calls, identity, approvals, data access, external sends, and final output so the whole episode can be reconstructed. Answers the general case. *CISA/NCSC; Microsoft; NIST.*

**CT-10 — Monitoring, detection, and feed integration.** Watch for anomalous tool use, unusual permission requests, unexpected external sends, injection indicators, dependency vulnerabilities, and known-exploited advisories. Answers TH-02, TH-07, TH-09. *CISA; Microsoft; secfeed/KEV.*

**CT-11 — Evaluation and red teaming.** Test for prompt injection, RAG and memory poisoning, tool abuse, privilege escalation, and delegation abuse before launch, on change, and on a schedule. Answers TH-01 through TH-10. *NIST; Anthropic; MITRE ATLAS; OWASP.*

**CT-12 — Incident response.** Have a procedure for stopping the agent, revoking credentials, isolating memory, preserving logs, scoping the blast radius, and notifying. Answers the general case. *CISA/NCSC; NIST.*

**CT-13 — Lifecycle and change management.** Treat changes to the model, prompts, tools, RAG, memory, dependencies, and evaluation sets as reviewable, recorded, and reversible. Answers TH-03, TH-04, TH-07, TH-09. *CISA/NCSC; NIST AI RMF.*

**CT-14 — Vendor and external service risk.** Confirm the responsibility boundary, data retention, auditability, and vulnerability handling of every model, API, plugin, and MCP provider. Answers TH-07, TH-08. *NIST AI 600-1; CISA; vendor docs.*

**CT-15 — Runtime security posture and capability degradation.** When new information, an observed attack, or an anomaly arrives, switch posture *before* the specification is formally revised, temporarily restricting or halting tools, external sends, secrets, memory, RAG, delegation, code execution, and scheduled runs. Answers the general case. *NIST AI RMF Manage; CISA IR; Microsoft layered controls.*

## Four principles that make the catalogue hold together

The controls are not a menu to pick one from; they are layers that assume each other. Four design principles keep them coherent, and they recur through the rest of the book.

**Defend at the system boundary, not the model.** Model safety, application control, identity and IAM, the tool execution environment, and audit are separate layers, and you stack them. A safer model narrows one surface; it does not close the other three. This is why CT-03 through CT-15 are mostly about architecture and operations rather than prompts.

**Approve on authority, not on intent.** An approval gate that asks "should the agent do this?" is answering the wrong question, because the model's stated intent is exactly what an attacker controls. A useful gate (CT-08) shows *which privilege, against which target, changing what* — the diff, the destination, the scope, the data classification, and the rollback condition — so the human is checking authority and effect, not vibes.

**Evaluate as regression, not as a one-time gate.** Every new model, tool, RAG source, or permission re-opens cases that a prior evaluation closed. CT-11 only works if the failing cases from last quarter are re-run this quarter, which is why the verification plan in Chapter 10 is a standing suite rather than a launch checklist.

**Manage memory, RAG, and tool definitions like code.** They change agent behavior as surely as source does, and an unreviewed change to any of them is an unreviewed change to your security posture. CT-13 puts them under version control, review, and rollback — and CT-15 buys you time when a change lands faster than review can keep up.

That last pairing — permanent change management and provisional runtime degradation — is the seam between the controls you design ahead of time and the ones you invoke under pressure. The next three chapters work through the first three hardening surfaces in the order an attacker probes them, starting with identity: who the agent is, and whose authority it is allowed to spend.
