{frontmatter}

# AI Agent Security: A Field Manual

## Threats, Controls, and Verifiable Assurance for Autonomous Systems

*A living manual for engineers, security architects, and the people who have to sign off on shipping an agent.*

{width: 40%}
![](cover-mark.png)

---

**Author:** Shugo Nozaki

**Edition:** First edition (living document). See the Changelog for the current revision.

**License:** This book is released under the Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License (CC BY-NC-ND 4.0). You may read, share, and quote it with attribution. You may not sell it, and you may not publish modified versions. The full license text is at <https://creativecommons.org/licenses/by-nc-nd/4.0/>.

The normative framework, taxonomies, and requirement identifiers in this book are published openly, in part, as prior art: to keep the vocabulary of agent security in the commons rather than behind a single vendor's paywall.

**Companion kit (separate product):** The explanations in this book are free. The *tools that execute and produce evidence* are not. An executable verification suite — the injection tests, the MCP/EMA configuration checklists, and the timestamped audit artifact described in Part IV — is sold separately. This book tells you what to check and why. The kit checks it for you and hands a CISO a signed report. See the back matter for details.

---

# Preface

Most security writing about AI agents falls into one of two traps. The first is the checklist with no theory: a list of "best practices" you cannot reason about, cannot adapt, and cannot defend to an auditor. The second is the think-piece with no teeth: a thoughtful essay on "alignment" and "trust" that gives an engineer nothing to type on Monday morning.

This manual is built to avoid both. It is organized around a single chain that runs from top to bottom:

**Threat → Control → Requirement → Verification.**

Every threat class in Chapter 2 maps to one or more controls in Part II. Every control is expressed as a normative requirement in Chapter 9, using RFC 2119 language (SHALL / SHOULD / MAY), so that "we do this" becomes a claim someone can audit rather than a vibe. And every SHALL requirement is tied to at least one verification test in Chapter 10 that returns a pass or a fail. If a requirement cannot be tested, either the requirement is wrong or you are missing a control. That is the discipline the book enforces on itself.

## Who this is for

You are building, operating, or signing off on a system where a language model can *take actions*: call tools, run code, send email, move money, change production. The moment a model stops producing text for a human to read and starts producing calls for a machine to execute, it inherits the entire threat surface of a privileged automation account — plus a new one, because it can be argued into misusing that account in plain language.

If that is your system, this book is for you. Prior knowledge of application security helps but is not assumed. Where a concept is specific to agents (delegation chains, tool poisoning, memory contamination, enterprise-managed authorization), the book defines it before using it.

## How to read this book

- **Part I — Foundations** establishes why agents are a distinct security problem and lays out the threat taxonomy (TH-01 through TH-10). Read this first even if you skim the rest.
- **Part II — Controls** is the defensive core: the control taxonomy, then three chapters on identity, tool safety, and untrusted content, in the same order an attacker would probe them.
- **Part III — Operations** covers the things that only exist once the agent is live: monitoring, incident response, and a runtime "security posture" state machine for when you are attacked faster than you can patch.
- **Part IV — Assurance** turns the whole book into something auditable: the requirement specification, the verification test plan, and the governance record a regulator or enterprise buyer will ask for.

The appendices carry the cross-reference tables (threat-to-control-to-requirement) and the source register, so the body of the book stays readable.

## A note on scope and honesty

This is a *living* manual. Agent security is moving faster than any print cycle. Specifications change (the MCP authorization model and the EMA standard discussed in Chapter 5 are recent and still settling); attack techniques change monthly. Where the book states something that depends on a moving external standard, it says so and cites the source in Appendix B. Where the book is confident, it uses SHALL. Where it is not, it uses SHOULD and explains the trade-off. The book never dresses an opinion up as a settled fact to sound authoritative. If you catch it doing so, that is a bug; the errata process is in the Changelog.

{mainmatter}
