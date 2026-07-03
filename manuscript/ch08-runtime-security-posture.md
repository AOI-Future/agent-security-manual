# Runtime Security Posture

## The gap between knowing and fixing

There is a window in every security program between the moment you learn you are exposed and the moment a reviewed, tested fix is live. For traditional software that window is uncomfortable. For an agent it is dangerous, because the agent keeps acting autonomously the whole time — reading untrusted content, calling tools, moving money — on a specification that everyone now knows is out of date. Waiting for the specification to be formally revised means leaving a privileged automation account running under rules you no longer trust.

This chapter is about closing that window with a second control loop. The book's normal loop is slow and deliberate: a threat is analyzed, a control designed, a requirement written, a test added, the change reviewed and shipped. That is the right process and it should not be rushed, because rushing it is how you ship a bad control. But it is too slow to be your only response to an active attack. So you run a second loop alongside it — **a fast runtime loop that temporarily degrades the agent's capability on evidence, before the slow loop has produced a permanent answer.** The runtime posture is that fast loop. It buys time for the slow loop to do its job properly.

## A dual-loop design

Keeping the two loops distinct is the central design decision, because they have opposite failure modes. The slow loop (specification) must be careful, reviewed, and permanent; its danger is moving too fast and codifying a bad rule. The fast loop (runtime protection) must be immediate and reversible; its danger is moving too slow and leaving the agent exposed. If you collapse them — if every runtime reaction becomes a permanent requirement — you either paralyze the slow loop with churn or corrupt it with unreviewed rules. So the runtime loop is explicitly provisional: everything it does is temporary and everything it does is logged for the slow loop to adjudicate later.

This is why the runtime posture is capability *degradation* rather than reconfiguration. It does not rewrite the agent's policy; it turns capabilities down or off for a while. When the slow loop catches up, the degradation is either promoted into a real requirement, relaxed as a false alarm, or folded into an existing rule — but that adjudication happens in the slow loop, on its own timeline, not in the heat of the moment.

## The posture state machine

The agent occupies one of a small set of named postures, and it moves between them on evidence. The states form a ladder from full capability to full stop:

**normal** — full capability under the current specification. The default.

**watch** — nothing is restricted, but monitoring is heightened and signals are being collected. This is where low-urgency intelligence lands: a best-practice update, a documentation change, a weak anomaly worth watching but not acting on.

**provisional_guard** — selected capabilities are tightened pending review. This is the posture for a credible but unconfirmed concern: high-risk guidance has changed, or an anomaly looks real but is not yet an established attack. The agent keeps working with specific powers constrained.

**restricted** — dangerous capabilities are curtailed materially. This is where a confirmed critical vulnerability in a dependency, or a persistent anomaly, puts the agent: it can still do its safe work, but the high-risk tools, sends, and privileges are pulled back.

**emergency_block** — the dangerous surface is halted outright. This is the posture for active exploitation: an attack is happening now, and the agent's ability to cause harm is cut while humans take over.

**revised_baseline** — the temporary posture has been adjudicated by the slow loop and a new normal is in force. This is the exit that closes the loop: the runtime reaction has become a reviewed part of the specification, and the agent returns to full, correct operation under updated rules.

Movement up the ladder is fast and evidence-driven; movement back down to normal happens only through the slow loop's review, so you never quietly forget that you were under attack.

## Trigger classes and their SLAs

Postures do not change on vibes; they change on classified triggers, each with a target posture and a response-time SLA. Naming the trigger classes ahead of time is what makes the reaction fast, because the decision has already been made — the on-call responder is executing a plan, not inventing one under pressure.

**active_exploitation** — an attack observed in progress. Target posture emergency_block, SLA immediate, on the order of fifteen minutes. This is the only trigger that halts first and asks questions after.

**critical_vulnerability** — a confirmed critical flaw in the model, framework, tool, or dependency, especially one on a known-exploited list. Target posture restricted, SLA on the order of one hour.

**high_risk_guidance_change** — authoritative guidance shifts in a way that materially changes risk (a standard body reclassifies something, a vendor discloses a design weakness). Target posture provisional_guard, SLA on the order of twenty-four hours.

**suspicious_runtime_anomaly** — monitoring surfaces behavior that looks like an attack but is not yet confirmed. Target posture provisional_guard or restricted depending on severity, SLA on the order of one hour.

**best_practice_update** — new guidance that improves posture but signals no active risk. Target posture watch; folded into the slow loop on its normal cadence.

**documentation_only** — informational change with no capability impact. Target posture watch, reviewed on a monthly cadence.

The SLAs are deliberately asymmetric: minutes for active exploitation, a day for a guidance change, a month for documentation. Matching urgency to evidence is what keeps the fast loop from either under-reacting to a live attack or over-reacting to a blog post.

## The kill switch and granular revocation

At the top of the ladder sits the kill switch, and its most important property is that it is not a single switch. A binary "stop everything" is often the wrong tool, because it converts a narrow threat into a full outage, which is itself a cost an attacker may be trying to impose. The runtime posture instead exposes *granular* revocation: the ability to cut individual capabilities — a specific tool, an MCP server, external send, memory write, RAG retrieval, delegation, scheduled or cron runs, or an entire agent unit — independently.

Granularity is what lets the response fit the threat. A rug-pull on one MCP server revokes that server and nothing else. A memory-contamination event freezes memory writes while reads continue. An exfiltration attempt cuts external send while the agent keeps doing safe internal work. Only a genuinely systemic compromise pulls the whole agent. Each of these is the same kill-switch mechanism aimed at a different capability, and each maps to one of the granular credentials that Chapter 4's scoped-credential design made revocable in the first place. This is where identity design pays off operationally: you can revoke narrowly only because you granted narrowly.

## Policy version binding, the signal queue, and reconciliation

Three mechanisms keep the fast loop honest and connected to the slow one.

**Policy version binding** means every runtime action records which version of the specification and posture policy was in force when it happened. Without this, a posture change makes the audit trail ambiguous — you cannot tell whether an action was allowed at the time or not. With it, every logged action is interpretable against the exact rules that governed it, which is what an auditor and an incident reviewer both need.

**The runtime signal queue** is the append-only record — in practice a `runtime_signal_queue.jsonl` — where every trigger, posture change, and degradation is written as it happens. It is the fast loop's memory and the slow loop's inbox: nothing the runtime does is invisible, and nothing is lost between the reaction and the review.

**Reconciliation** is the disposition step where the slow loop works through the queue and assigns each entry an outcome: promote it to a permanent requirement, encode it as a lasting conditional rule, mark it a false positive and relax the degradation, merge it into an existing requirement, or carry it forward as incident follow-up. Reconciliation is what moves the agent from a temporary posture to `revised_baseline`, and it is the discipline that stops provisional guards from silently becoming permanent without ever being reviewed.

One design prohibition holds all of this together: **the runtime loop may only reduce capability, never expand it.** A fast loop that could grant new powers would be an unreviewed privilege-escalation path — exactly the thing the whole book is built to prevent. The runtime posture can turn the agent down and stop it; it can never turn it up. Turning it up is the slow loop's job, with review, and only ever ends at `revised_baseline`.

The runtime posture is the operational answer to Chapter 1's fifth question — what do we do when we are attacked faster than we can fix it. With it, the answer is a defined state machine with named triggers and SLAs, not an all-hands panic. That completes the operational picture. Part IV turns the entire book into something auditable, beginning with the requirement specification: the normative SHALLs that every control in Parts II and III must be expressible as, if anyone is to verify that you actually do them.
