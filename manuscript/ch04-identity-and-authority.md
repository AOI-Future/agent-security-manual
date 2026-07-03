# Identity and Authority

## The question the log has to answer

When something goes wrong, the first question anyone asks is *who did this, and under whose authority.* For a traditional system the answer is a principal — a user or a service account — that stays stable across a request. For an agent system the answer is genuinely hard, because the agent acts on behalf of a human, then spawns a sub-agent, which calls an external agent, which uses a shared credential, and by the third hop the authority has been passed around so many times that no single record holds the whole story. An attacker can hide in exactly that ambiguity (TH-05, TH-06).

This chapter is about making that question answerable by construction. It rests on one distinction the rest of the chapter elaborates: **the agent is not the human it acts for, and its authority is not the human's authority.** Collapse those two and you lose least privilege and audit in the same move.

## Agent identity is distinct from human identity

The most common and most damaging shortcut is to let an agent authenticate as a human — to hand it a person's OAuth session, API token, or standing credential and let it act inside that person's permissions. It is convenient and it is a mistake. Every action the agent takes is now attributed to the human, so your audit trail records a person doing things a program did, and you have lost non-repudiation. The agent inherits the human's full permission set, so you have lost least privilege. And the credential is usually long-lived and broadly scoped, so a single compromise is total.

**REQ-001** makes this normative: an agent SHALL NOT directly share a human user's standing credentials, and SHALL operate under an agent or workload identity whose purpose, environment, and scope are explicit. The agent gets its own identity — the same way a service does, but purpose-built. Microsoft's Entra Agent ID and the NIST NCCoE work on software and AI agent identity are the reference points here; the vocabulary is still settling, but the principle is not in dispute.

Distinct identity is what makes the audit log truthful. When the log says "agent-invoice-drafter acted," it means the agent, not the person the agent serves, and the two are separable in every downstream question.

## Workload identity, short-lived and scoped

A distinct identity is only half the win. The other half is that the identity's *credentials* are short-lived and scoped to a purpose, not permanent and general. A permanent, broadly-scoped token is a standing invitation: it works forever, it works from anywhere, and it works for everything the agent was ever allowed to do, which is usually far more than the task in front of it needs.

The discipline is to bind credentials to a purpose, an environment, and a lifetime. **REQ-003** separates the high-risk privileges — production change, permission change, external send, payment, deletion, secret access — from the ordinary reasoning context, and grants them narrowly and just before use rather than holding them ambient throughout a session. The reasoning loop, which is the part an attacker can talk to, runs without the dangerous privileges in hand; the privileges are assembled at the moment of the sanctioned action and released after. An injection that lands in the middle of a conversation finds nothing to escalate into, because the escalated authority is not present until the approved action is executed. Chapter 5 handles the approval gate itself; the identity work here is what makes the gate meaningful, by ensuring there is a privilege boundary for the gate to sit on.

## Delegated authority must be scoped, time-bound, and auditable

Delegation is where authority gets interesting and where it most easily leaks. When an agent delegates work — to a sub-agent, a tool, or an external agent over a protocol — it is lending authority, and lent authority has to carry its terms with it. **REQ-002** requires that any delegated authority have a purpose, a duration, a target resource, an allowed set of operations, an approver, and a revocation condition. Delegation without those terms is a blank check.

Two failure modes make this urgent. The first is *scope inflation*: a child agent issued the same permissions as its parent can do anything the parent could, so an attacker who cannot get the parent to misbehave simply pushes the task down to a child that will. The second is *audit fracture*: if the delegation chain is not carried in the trail, the child's actions look like they came from nowhere, and the parent's refusal and the child's compliance are recorded as unrelated events.

**REQ-013** closes the first gap on the policy side: when work is delegated to a child or external agent, the parent's policy, permission ceiling, prohibited operations, audit identifier, and approval requirements SHALL NOT be lost. The delegate inherits the *constraints*, not a fresh unconstrained context.

## The delegate never holds scope greater than or equal to its parent

The single most important rule in this chapter is a ceiling. **A delegate SHALL NOT hold a scope equal to or greater than its parent.** Every hop down a delegation chain narrows authority; it never preserves it and never widens it. **REQ-051** makes this concrete: inter-agent delegation SHALL NOT pass the parent's credentials through unchanged, and SHALL instead issue a *new* credential scoped down to what the task actually requires, with its own expiry.

The reason for strict inequality rather than "less than or equal" is that permission-preserving delegation is a laundering primitive. If a child can hold exactly the parent's scope, delegation adds no constraint and subtracts audit clarity — you have created a second principal with the same power and a murkier provenance. Requiring strict narrowing means every delegation is also a reduction, so the deepest agent in a chain is always the least privileged, which is the opposite of how an attacker wants the topology to look. CSA MAESTRO frames this as the trust-inheritance problem, and the mitigation is to refuse to inherit trust wholesale.

## Correlation IDs across the delegation chain

Narrowing authority controls the blast radius; a correlation ID makes the chain reconstructable. **REQ-051** requires that the entire delegation chain be auditable under a single correlation identifier, so that a parent's task, every sub-agent it spawned, and every external call those made are all stitched to one thread. Without it, you can answer "what did this agent do" only one agent at a time, and the attacker's whole strategy under TH-06 is to spread work across enough agents that no single log tells the story.

A correlation ID turns the audit trail from a pile of disconnected tool calls into a tree you can walk: this human authorized this parent, which delegated to these children under these narrowed scopes, one of which made this external send under this approval. That is the exact shape of the "who did what, under whose authority, and when" that Chapter 1 set as the standard for a mature deployment, and it is only achievable if the identifier is threaded through delegation rather than reset at each hop.

## What this buys you, and what comes next

Distinct identity makes the log truthful. Short-lived scoped credentials shrink what a compromise can reach. Scoped, time-bound, auditable delegation with a strict narrowing ceiling stops permission laundering. A correlation ID makes the whole chain reconstructable. Together they turn TH-05 and TH-06 from open ambiguities into controlled, recorded structure — and they give the approval gates and tool controls of the next chapter a real privilege boundary to defend.

Identity establishes *who* may act and *how much* authority they may spend. Chapter 5 turns to *what* they can do with it: the tools, the sandboxes, the approval gates, and the supply chain — MCP and EMA included — that stand between an agent's authority and a real-world consequence.
