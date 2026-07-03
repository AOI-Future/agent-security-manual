# Governance and Compliance

## The accountability the technical controls assume

Every control in this book has a silent dependency: someone who owns the risk it manages, decided it was acceptable, and can be held answerable when it fails. Governance is that dependency made explicit. It is the least technical chapter in the book and, for anyone who has to sign off on shipping an agent, often the most consequential — because when an incident reaches a regulator, a customer, or a courtroom, the first questions are not about your sandbox configuration. They are: who decided to deploy this, what risks did they accept, who was supposed to be watching, and where is the record. A deployment that cannot answer those has a governance gap that no amount of engineering closes.

Governance is also where the book's opening claim comes home. An agent is a privileged automation account that a stranger can talk to, and privileged accounts in every serious organization have owners, documented scope, and review. The novelty of agents does not exempt them from that discipline; it makes the discipline more urgent, because the account can be argued into misusing itself.

## The governance record

The foundation is a documented governance record for each agent, and **REQ-048** makes it mandatory: use cases, prohibited uses, the accountable owner, the accepted risks, the exceptions granted, and the regulatory or contractual obligations that apply. This is CT-01 rendered as an artifact rather than an intention. Its value is precisely that it is written down and owned: an accepted risk that lives only in someone's head is not accepted, it is forgotten, and a prohibited use that was never documented cannot be enforced or audited.

The record is not paperwork for its own sake. Each field does work elsewhere in the system. The prohibited-use list is what the runtime abuse controls of Chapter 7 enforce. The accepted-risk register is what an auditor checks the verification results against. The accountable owner is the human the incident-response procedure of Chapter 7 notifies and the person the escalation SLAs of Chapter 8 answer to. Governance is the connective tissue that gives the technical controls someone to report to.

## Roles and responsibilities

Accountability blurs when everyone is responsible, because that means no one is. A RACI assignment — who is Responsible for operating each control, who is Accountable for the outcome, who is Consulted, who is Informed — turns "the security team handles it" into named ownership per control area. The Accountable role is the one that matters most and the one most often left implicit: for each control in Parts II and III, exactly one person should be answerable for whether it works, and that person should be able to see its verification results.

The point of the assignment is not bureaucratic tidiness. It is that under incident pressure, the questions of who can authorize a kill-switch pull, who signs off on returning an agent from a restricted posture, and who accepts the residual risk of shipping with a known SHOULD unmet all have pre-decided answers. Deciding them in advance is what keeps an incident from stalling on "who has the authority to make this call."

## Risk assessment and review records

Two records give the governance decision its evidentiary backing. The **risk assessment record** documents, before deployment and on significant change, what could go wrong (traced to the threat taxonomy), how likely and how severe it is, what controls reduce it, and what residual risk remains for the owner to accept. The **review record** documents that a competent human actually examined the deployment — the design, the verification results, the residual risks — and made a decision, with a date and a name attached.

These records are what distinguish a governed deployment from a lucky one. A team that shipped an agent that happened not to fail looks, in the good case, exactly like a team that reasoned carefully about the risks — right up until something goes wrong, at which point only one of them has the record to show that the decision was deliberate and informed. The review record is also the natural home for the verification artifact from Chapter 10: the signed, timestamped report is the evidence the reviewer relied on, and attaching it to the review record closes the loop between what was tested and what was decided.

## High-risk determination and human oversight

Not every agent carries the same weight, and governance has to say so explicitly. **REQ-049** requires that high-risk determinations be recorded, together with the human-oversight design that the determination calls for and the review record that supports the deployment decision. The determination is a deliberate classification: does this agent, given its use cases, its authority, and the people it affects, rise to a level that demands stronger oversight — and if so, what does that oversight concretely consist of.

For an agent judged high-risk, human oversight is a design requirement, not a reassuring phrase. It means specifying which actions require a human in the loop (connecting back to the approval gates of Chapter 5), what information that human is shown so they are approving authority rather than intent, who that human is, and what happens when they are unavailable. Oversight that is not designed is oversight that is not there: a claim of "a human reviews high-risk actions" is worth exactly as much as the mechanism behind it, and REQ-049 exists to force that mechanism to be written down and reviewable. This is also the field most directly aligned with external regulatory expectations around high-risk AI, where documented human oversight of consequential automated decisions is increasingly a compliance obligation rather than a good practice.

## Governance as living artifacts

The last point is that governance records are living documents bound to the same loops as the rest of the system. A risk assessment is valid until the agent changes materially; then the change-management process of Chapter 7 requires a fresh one. A runtime posture that reaches `revised_baseline` through the reconciliation of Chapter 8 is a change to the accepted-risk picture, and the governance record follows it. The verification suite runs continuously, and its results feed the review record continuously. Governance that is written once and filed is governance that is already stale; governance that is wired to the change, verification, and runtime loops stays true.

This closes the arc the book set out to trace. A threat is named (Part I), a control answers it (Part II), operations keep it honest while the agent runs (Part III), a requirement makes it auditable and a test proves it holds (Part IV), and governance puts a named human behind the whole decision. Every link connects to the next, and the chain — not any single clever defense — is what lets you deploy a privileged automation account that a stranger can talk to, and still answer for what it does. The appendices that follow carry the cross-reference tables and the source register that let you walk that chain in either direction.
