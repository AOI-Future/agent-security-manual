# Monitoring, Evaluation, and Incident Response

## What only exists once the agent is live

Parts I and II are about design: the threats you face and the controls you build in before launch. This chapter is about the things that only come into being once the agent is running against real input — the log you can actually reconstruct, the red-team suite that keeps finding regressions, the advisory feed that tells you a dependency just became exploitable, and the procedure you follow when the agent is turned against you or against a third party. Operations is where the assurance chain either proves itself continuously or quietly rots, because a control that worked at launch and was never checked again is a control you are merely assuming.

The through-line of the chapter is that **detection and response are only as good as the record they run on.** Everything here depends first on logging that can reconstruct an episode end to end, so that is where it starts.

## Logging that can reconstruct the episode

The standard for agent logging is not "we have logs." It is: given only the logs, can you reconstruct what happened, from the triggering input to the final effect, across every tool call and delegation hop? **REQ-030** requires end-to-end logging that captures the input, the model's response, each tool call and its result, the acting identity, approvals, data access, external sends, and the final output — enough that an incident can be walked step by step after the fact. This is CT-09 made operational, and it is the substrate for every other control in the chapter: monitoring watches this record, incident response preserves it, and governance audits it.

Two properties make the record trustworthy. It must be threaded by the correlation ID from Chapter 4, so a delegated action is stitched to the parent task that authorized it rather than floating free. And it must never contain secret values in plaintext — secrets are recorded as `[REDACTED]` plus a locator, so that the log itself does not become the exfiltration channel (TH-08) it is supposed to help you investigate. A log that leaks is worse than no log, because it carries the authority of an audit record while doing the attacker's work.

## Monitoring and feed integration

A record you never read is archaeology, not defense. Monitoring (CT-10) turns the log into signal by watching for the patterns that precede or accompany an attack: anomalous tool-use rates, unusual permission requests, external sends to undeclared destinations, injection indicators in retrieved content, and delegation that widens rather than narrows. **REQ-031** requires that these agent-specific signals be monitored and alerted on, not merely stored.

Monitoring also has to look outward, because much of what changes an agent's risk happens outside the agent. **REQ-032** requires integration with vulnerability and threat intelligence — CVE and KEV catalogs, EPSS scoring, and the security feeds relevant to your model, framework, and tool dependencies — so that a newly-exploited library or a freshly-disclosed MCP flaw becomes an alert on *your* agent rather than a headline you read later. KEV tells you something is being exploited in the wild; EPSS estimates the probability that a given vulnerability will be. Together they let you triage the flood of advisories down to the few that change your posture, which is the input the runtime machinery of Chapter 8 acts on.

## Evaluation as standing regression

Pre-launch red teaming is necessary and insufficient. It proves the agent resisted a set of attacks on one day, against one configuration. Every new model, tool, RAG source, prompt change, or permission grant can re-open a case that a prior evaluation closed, which is why CT-11 has to run as a standing regression suite rather than a launch gate. **REQ-033** requires continuous, repeatable evaluation covering the threat families — prompt injection, RAG and memory poisoning, tool abuse, privilege escalation, delegation abuse — executed before launch, on change, and on a schedule, with failing cases retained and re-run.

The regression discipline is the specific defense against TH-09, evaluation evasion. An attacker's best move against your assurance is to make production behavior diverge from evaluation behavior, so evaluation has to run against production-representative conditions and has to keep running. A suite that only ever gets easier — where fixed cases are quietly dropped — is a suite being hollowed out. Retaining every failure as a permanent regression case is what keeps the evaluation honest over time, and it is the reason the verification plan in Chapter 10 is a suite you run repeatedly rather than a checklist you complete once.

## Rollback exercises

Backups you have never restored are a hypothesis, and a rollback path you have never walked is the same. **REQ-034** requires that rollback and recovery be exercised, not merely designed: periodically restore a known-good state, revert a simulated memory contamination, roll a tool definition back to a pinned version, and confirm that credential revocation actually severs access. The exercise validates the machinery that incident response and the Chapter 8 kill switch both depend on. The first time you roll back memory should not be during a live contamination; by then you need the muscle memory, not the discovery that the procedure had a gap.

## Incident response for agents

Classical incident response assumes a fairly static principal and a bounded set of systems. Agent incident response (CT-12) has to contend with dynamic identity, delegation chains, and contaminated durable state, so it needs agent-specific containment moves. **REQ-035** requires a defined response procedure that can stop the agent, revoke its credentials — at the granularity of individual tools, MCP servers, external send, memory write, RAG, and delegation, not just "turn it all off" — isolate and preserve memory and logs, scope the blast radius across the delegation tree via the correlation ID, and notify the accountable owner. The granular revocation is what lets you keep a safe agent partly running while you sever the one capability under attack, rather than choosing between full outage and continued exposure.

Containment leans directly on the earlier controls. You can scope the blast radius only because the log is reconstructable (REQ-030). You can revoke narrowly only because credentials are scoped and short-lived (Chapter 4). You can undo contamination only because memory writes are reversible (Chapter 6). Incident response is not a separate system; it is the payoff for having built the others.

## The abuse path: when the agent is the weapon

Most of this chapter defends *you*. The last part defends third parties from your agent. TH-10 (model and service abuse) and the outward face of TH-09 concern an agent — usually public-facing — being driven to support an attack, commit fraud, generate harmful content, or violate a regulation, with the legal and reputational liability landing on you. The response path here is different in kind: it is about enforcing usage policy at runtime and blocking abuse as it happens.

Concretely, this means detecting abusive-use patterns (automation abuse, harmful-generation attempts, policy-violating requests) in the same monitoring stream, rate-limiting and blocking the offending session or principal, and escalating to the abuse-response owner rather than silently absorbing the traffic. It ties back to the approval and identity controls — a public agent should hold minimal standing authority precisely because it is the one most likely to be pointed at someone else — and forward to Chapter 8, where sustained abuse is one of the triggers that shifts the whole agent into a more restricted runtime posture. Blocking an abusive session is the tactical move; changing posture is the strategic one when the abuse is not a single session but a campaign.

Monitoring tells you something changed. Evaluation tells you whether a change broke a defense. Incident response tells you what to do when one did. What none of them answer on their own is what to do in the gap between *learning of a threat* and *having a fix reviewed and shipped* — the hours or days when you know you are exposed but the specification has not caught up. That gap is the subject of the next chapter: the runtime security posture that lets an agent degrade its own capability under pressure, before the paperwork is done.
