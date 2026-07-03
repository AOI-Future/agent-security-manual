# The Agent Security Problem

## What changes when a model can act

A language model that only writes text is a content risk. It can be wrong, biased, or offensive, and those are real problems, but the blast radius ends at whatever a human does after reading the output. A human is still in the loop, holding the pen.

An *agent* is different in one specific way: it closes the loop. It takes its own output and feeds it into a tool — an API call, a shell command, a database write, an email send, a payment. The human who was holding the pen is now, at best, watching a stream of actions go by, and at worst, asleep. The model's text is no longer a suggestion. It is an instruction to a machine that will carry it out.

This single change is the entire subject of this book. Everything downstream follows from it:

- The model now has **effective permissions**. Not the permissions written in an IAM policy — the permissions it can actually reach through the tools you gave it, composed together. An agent with a browser, a file system, and an email tool has the *composite* capability to read a secret from a file and mail it out, even if no single tool was meant to do that.
- The model can be **argued into using those permissions** in plain language. This is the property that has no equivalent in traditional software. A SQL injection needs a syntactic flaw. An agent can be compromised by a grammatically correct English sentence hidden in a web page, because the model was built to follow instructions and cannot reliably tell *your* instructions from an attacker's.
- The consequences **persist**. The agent writes to memory, to RAG stores, to logs, to its own future context. A successful attack is not a single bad output; it can be a poisoned fact that fires three sessions later, when no one is watching.

If you take one idea from this chapter, take this: **an agent is a privileged automation account that a stranger can talk to.** Every control in this book is a way of narrowing either the "privileged" part or the "a stranger can talk to it" part.

## Why existing security models are necessary but not sufficient

You do not get to throw away application security, cloud security, or identity engineering. You need all of them. An agent runs on infrastructure, holds credentials, calls networked services, and processes untrusted input, so every classical control still applies. If your secrets are in plaintext and world-readable, no amount of agent-specific cleverness will save you.

But three properties of agents break the assumptions those models were built on.

### 1. The trust boundary between instruction and data is gone

Classical security is built on the idea that you can separate control from data: code is code, input is input, and the job is to stop input from being executed as code. Injection attacks are what happen when that boundary leaks.

For a language model, there is no boundary to defend. Instructions and data arrive in the same channel — natural language — and are processed by the same mechanism. A note in a calendar invite, a comment in a pull request, a paragraph in a PDF, a row returned from a search: all of it enters the model's context as text, and all text is potentially instruction. You cannot escape or parameterize your way out of this the way you can with SQL. This is why prompt injection (TH-01) sits at the top of the threat taxonomy and touches almost every other threat.

### 2. Capability is composite and emergent

In classical systems you reason about permissions per component. An agent's real capability is the *composition* of every tool in its reach, and the composition is larger than the sum. Tool A reads files. Tool B makes HTTP requests. Neither is dangerous alone. Together they are an exfiltration primitive. You cannot secure the tools one at a time; you have to reason about what the agent can accomplish by chaining them, which is a harder and less familiar analysis.

### 3. Identity and audit blur under delegation

An agent acts on behalf of a human, then spawns a sub-agent, which calls an external agent over a protocol, which uses a shared credential. By the third hop, "who did this and under whose authority" is genuinely hard to answer, and an attacker can hide inside that ambiguity (TH-05, TH-06). Traditional identity assumes a principal — a human or a service account — that is stable across a request. Agent systems generate principals dynamically and delegate authority across trust domains, which is exactly the situation your audit log was not designed for.

## The four attacker assumptions

The rest of the book is organized defensively, but it is worth stating up front how a competent attacker thinks about your agent. Four assumptions recur across every threat class:

1. **Confuse the boundaries.** The blurrier the line between data, instruction, tool output, memory, and the approval UI, the easier the attack. Attackers hunt for places where untrusted text reaches a trusted decision.
2. **Compose the permissions.** They target the *combined* authority of browser, files, email, MCP servers, APIs, and human identity — not the model in isolation.
3. **Persist.** They aim to land in RAG, memory, logs, configuration, tool definitions, or the conditions that exclude a case from evaluation — anywhere the payload survives past the current turn.
4. **Defeat audit.** They break work into sub-tasks, delegate, and scatter tool calls so that no single log tells the whole story.

Notice that only the first of these is about the model at all. The other three are about the *system* around the model. This is the reason a "safer model" does not solve agent security, and why most of this book is about architecture, identity, and operations rather than prompts.

## The chain this book enforces

The book's structure is not decorative. It is a claim about how assurance is built, and it runs in one direction:

> **Threat (Ch. 2) → Control (Part II) → Requirement (Ch. 9) → Verification (Ch. 10).**

- You cannot justify a **control** without naming the **threat** it addresses. Controls with no threat are cost; threats with no control are risk you are silently accepting.
- You cannot **operate** a control you have not written down as a **requirement**. "We review tool permissions" is a hope. "REQ-010: every network-capable tool SHALL have an explicit allowlist" is a claim.
- You cannot **trust** a requirement you cannot **verify**. A SHALL with no test is decoration. Chapter 10 exists so that every SHALL in Chapter 9 terminates in a pass/fail check.

When you finish this book you should be able to take any control you run and trace it up to the threat that justifies it and down to the test that proves it works. If any link in that chain is missing, you have found either wasted effort or unmanaged risk. That traceability — not any single clever defense — is the product.

## What "good" looks like

A mature agent deployment can answer these questions with evidence, not assertions:

- *What can this agent actually do?* — a tool and permission inventory, not a guess (Chapters 3–5).
- *What happens when untrusted content tells it to misbehave?* — a tested answer, from an executable injection suite, not a policy document (Chapters 6, 10).
- *Who did what, under whose authority, and when?* — reconstructable from logs alone, across delegation hops (Chapters 4, 7).
- *What do we do when we are attacked faster than we can fix it?* — a defined runtime posture with a kill switch and an SLA, not an all-hands panic (Chapter 8).
- *Can we prove all of the above to someone who does not trust us?* — a signed, timestamped audit artifact (Part IV).

The gap between a team that can answer these and a team that cannot is the gap this manual is written to close. Chapter 2 begins with the threats.
