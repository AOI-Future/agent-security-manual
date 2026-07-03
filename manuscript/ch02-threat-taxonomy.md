# The Threat Taxonomy

## Why a taxonomy, and how to use it

You cannot defend what you cannot name. This chapter names the threats to an agent system as ten classes, TH-01 through TH-10. The identifiers are stable: they are used throughout the rest of the book, they map to controls in Part II and to requirements in Chapter 9, and they are the same identifiers the companion verification kit reports against. When a test fails, it fails against a threat class, so you always know *what kind of thing* just went wrong.

The taxonomy is deliberately organized by the *attacker's* objective and entry point, not by the defender's org chart. This matters. A defender is tempted to sort threats by which team owns the fix ("that's a platform problem, that's an app problem"). An attacker does not care about your org chart; they care about where untrusted input meets trusted capability. Sorting by the attacker's view keeps the boundaries — the actual battleground — in the foreground.

Each class below gives the attacker's viewpoint and representative scenario, the entry points, the impact, and the primary external references. The full threat-to-control-to-requirement mapping is in Appendix A.

## The ten threat classes

### TH-01 — Direct and indirect prompt injection

**Attacker's view.** Exploit the model's inability to reliably separate "instruction" from "data." Hide directives in anything the model will read: a web page, an email, a PDF, a RAG result, the output of a tool. The hidden instruction changes the agent's policy, its output, or its choice of tool.

**Entry points.** User input, browser content, RAG retrieval, tool output.

**Impact.** Goal hijacking, secret disclosure, wrong actions, approval bypass.

**Why it leads the list.** This is the threat with no classical analogue and no clean fix. Every agent that reads untrusted content is exposed, and "untrusted content" includes the output of tools the agent itself called. Indirect prompt injection — where the payload is not in the user's message but in data the agent fetches — is the form most teams under-test, because it does not appear in a simple chat transcript. Chapters 6 and 10 treat it at length.

*References: OWASP LLM01; Anthropic; NIST CAISI; MITRE ATLAS.*

### TH-02 — Tool abuse and privilege escalation

**Attacker's view.** Use the agent's tools as the attacker's own API. Exploit vague tool descriptions, over-broad scopes, and missing input validation to make a dangerous tool do dangerous things.

**Entry points.** Tool calls, APIs, MCP servers, browsers, code executors.

**Impact.** External exfiltration, deletion, funds transfer, code execution, production changes.

**Why it matters.** This is where a prompt injection turns into a real-world consequence. TH-01 is the way in; TH-02 is the payload. A network-capable tool with no allowlist, or a "run code" tool with no sandbox, converts a clever paragraph into an incident. Chapter 5 is the primary defense.

*References: Microsoft; OWASP; CISA; CSA MAESTRO.*

### TH-03 — RAG and knowledge-base poisoning

**Attacker's view.** Inject false information or instructions into documents before ingestion, into top-ranked search results, into shared knowledge, or into the vector store itself, so that later reasoning is steered by the plant.

**Entry points.** Ingestion pipelines, search, vector databases, web clippings.

**Impact.** Steered decisions, fabricated citations, persistent injection, contaminated decision-making.

**Why it matters.** RAG is trusted by design — the whole point is that the model treats retrieved content as authoritative. That trust is the vulnerability. Unlike a one-shot prompt injection, a poisoned knowledge base fires repeatedly, for every user, until someone finds and removes it. Chapter 6 covers provenance, TTL, and trust metadata as the primary controls.

*References: OWASP; MITRE ATLAS; NIST AI 600-1.*

### TH-04 — Memory and context contamination

**Attacker's view.** Get the agent to write attacker instructions or false facts into long-term memory, thread summaries, shared context, or user profiles.

**Entry points.** Memory writes, summarization, shared context.

**Impact.** Hijacking of future tasks, hard-to-detect persistence.

**Why it matters.** Memory is RAG poisoning's quieter sibling. The contamination is written *by the agent itself*, in the course of normal operation, which makes it look legitimate. It surfaces in a later session, decoupled from the attack that planted it, which is exactly what makes it hard to detect and hard to attribute. The control is treating memory writes as a privileged, provenance-tagged, reversible operation — see Chapters 6 and 8 (rollback).

*References: OWASP GenAI; MITRE ATLAS; CSA MAESTRO.*

### TH-05 — Abuse of agent identity and authority

**Attacker's view.** Exploit the confusion between human identity and agent identity, standing credentials, over-provisioned permissions, and unclear delegation boundaries.

**Entry points.** Workload identity, OAuth, API tokens, sessions.

**Impact.** Impersonation, lateral movement, privilege escalation, non-repudiation failure.

**Why it matters.** If an agent authenticates as a human, every action it takes is attributed to that human, and you have lost both least privilege and audit. If it holds a permanent, broadly-scoped credential, a single compromise is total. Chapter 4 makes agent identity distinct, short-lived, and scoped.

*References: NIST NCCoE; Microsoft Entra; NIST CAISI.*

### TH-06 — Delegation and multi-agent abuse

**Attacker's view.** Route around a parent agent's restrictions by pushing work to a child agent or an external agent, blurring responsibility and audit boundaries in the process.

**Entry points.** Delegation, agent-to-agent protocols, task queues.

**Impact.** Loss of responsibility boundaries, approval bypass, detection evasion.

**Why it matters.** Delegation is a permission-laundering opportunity. If a child agent can be issued the same scope as its parent, or if the delegation chain is not carried in the audit trail, an attacker can accomplish through a sub-agent what the parent would have refused. The controls are correlation IDs across the whole chain and a rule that a delegate can never hold a scope equal to or greater than its parent — see Chapters 4 and 7.

*References: NIST CAISI; CSA MAESTRO; Microsoft.*

### TH-07 — Supply-chain, MCP, and plugin compromise

**Attacker's view.** Poison the tools, MCP servers, plugins, models, prompts, dependency libraries, or data sources the agent relies on.

**Entry points.** Registries, packages, models, prompts, tool metadata.

**Impact.** Arbitrary actions, data disclosure, persistence, evaluation evasion.

**Why it matters.** The agent trusts its tools implicitly. Two agent-specific forms deserve names. **Tool poisoning** is a malicious instruction hidden in a tool's *description* or metadata — text the model reads every time it considers the tool, invisible in normal use. **Rug-pull** is a tool that is benign when reviewed and approved, then swapped for a malicious version after it is trusted. The defenses — pinning versions, hashing tool definitions, detecting changes, and an allowlist of vetted MCP servers — are the core of Chapter 5 and the highest-value rows in the verification plan.

*References: CISA/NCSC; MITRE ATLAS; NIST AI RMF.*

### TH-08 — Data exfiltration and secret exposure

**Attacker's view.** Push secrets out through any channel the agent touches: inputs, prompts, logs, tool results, the browser, RAG, external APIs.

**Entry points.** Logs, prompts, files, connectors, browsers.

**Impact.** Leakage of personal data, credentials, or trade secrets.

**Why it matters.** Exfiltration is the common endpoint of many other threats, and it is easy to do accidentally — a secret echoed into a log, a credential passed to a tool that forwards it. The controls are egress restriction, output filtering, and a hard rule that secret values never appear in plaintext in any record. That last rule is why this book, and the companion kit, redact secrets to `[REDACTED]` plus a locator everywhere.

*References: NIST AI 600-1; CISA; Microsoft.*

### TH-09 — Audit and evaluation evasion

**Attacker's view.** Behave safely under evaluation and red-teaming, then deviate under production conditions, on a specific trigger, or in a particular permission state.

**Entry points.** Eval harnesses, guardrails, telemetry gaps.

**Impact.** Hollowed-out verification, delayed detection, un-auditable behavior.

**Why it matters.** This threat attacks your *assurance* rather than your system directly. If evaluation conditions are distinguishable from production, any guarantee derived from evaluation is void. It is also the threat that most directly justifies Part IV: verification has to run against production-representative conditions, continuously, and regressions have to be caught, or the whole Threat→Verification chain is theater.

*References: Anthropic; MITRE ATLAS; NIST.*

### TH-10 — Model and service abuse

**Attacker's view.** Use the agent's own capabilities for attack support, fraud, automation abuse, harmful generation, or regulatory violation.

**Entry points.** Public-facing agents, APIs, automation.

**Impact.** Legal, brand, and regulatory risk; amplification of harm to others.

**Why it matters.** The other nine classes are mostly about protecting *you*. This one is about your agent being turned into a weapon against third parties — and about the legal and reputational liability that follows. It is most acute for public-facing agents. The controls are usage policy enforcement at runtime and the abuse-response path in Chapter 7.

*References: NIST AI 600-1; OWASP; CISA.*

## Reading the taxonomy as a system

Two structural facts about the ten classes are worth stating explicitly, because they shape every defense that follows.

**The classes chain.** They are not independent. A typical incident is a *path* through several: an indirect prompt injection (TH-01) hidden in a poisoned RAG document (TH-03) invokes an over-scoped tool (TH-02) to exfiltrate a secret (TH-08), while delegation (TH-06) and log gaps (TH-09) obscure the trail. Defending one class in isolation leaves the path open. This is why the book insists on composite reasoning: you defend the *path*, not the node.

**The entry points concentrate.** Look down the "entry points" column and the same few surfaces recur: untrusted content reaching the model, tools with reachable capability, identity and delegation, and the audit layer. These four surfaces are exactly the four attacker assumptions from Chapter 1, and they are the organizing principle of Part II. You do not need ten unrelated defense programs. You need to harden four boundaries, and verify that the hardening holds.

The next part turns to those defenses. Chapter 3 lays out the control taxonomy — the catalogue of what you can actually do — before the following chapters go deep on identity, tools, and untrusted content in turn.
