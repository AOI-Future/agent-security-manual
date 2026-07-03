# Appendix B — Source Register

The claims in this book rest on external sources, and honesty requires saying which. This register lists the material the book draws on, with a one-line note on what each contributes, and it separates two kinds of source that should never be confused. **Primary sources** are authoritative and durable: standards bodies, government agencies, and platform vendors publishing normative guidance or specifications. They are the ground the book's SHALLs stand on. **Secondary signals** are timely but provisional: vulnerability feeds, vendor threat research, and industry analysis. They tell you what is happening now, and they feed the runtime and update loops, but they are not the basis for a normative claim on their own.

The distinction is operational, not academic. When the book says SHALL, a primary source is behind it. When the book describes a moving target — the MCP authorization model, the EMA standard, this month's attack technique — a secondary signal is behind it, and the book says so. Where the two disagree, the primary source governs the requirement and the secondary signal governs the runtime posture.

## Primary sources

These are the authoritative references the requirement specification is built on. Where a requirement carries a source mapping in Chapter 9, it points here.

**NIST** — The AI Risk Management Framework and its generative-AI profile (AI 600-1) supply the govern/map/measure/manage structure and the risk-management vocabulary the governance chapter uses. Work from NIST's CAISI and the NCCoE on AI and software-agent identity underpins the identity and delegation requirements. *Foundational for CT-01, CT-03, CT-05, CT-13, CT-15 and the governance family.*

**CISA and NCSC** — Joint and individual guidance on deploying AI systems securely and on secure-by-design principles. The primary basis for least-privilege, logging, and incident-response requirements, and for the layered "defend the system, not the model" stance. *Foundational for CT-05, CT-09, CT-12.*

**OWASP GenAI / LLM Top 10** — The LLM01 prompt-injection entry and the broader GenAI security project give the standard vocabulary for the injection, RAG-poisoning, and tool-abuse threat classes. *Foundational for TH-01, TH-02, TH-03, TH-04 and CT-07.*

**MITRE ATLAS** — The adversarial-tactics knowledge base for AI systems; the reference for framing threats by attacker technique and for the evaluation-evasion class. *Foundational for the threat taxonomy and CT-11.*

**CSA (Cloud Security Alliance) — MAESTRO** — The agentic-AI threat-modeling framework; the reference for the delegation and trust-inheritance analysis behind the multi-agent requirements. *Foundational for TH-06 and the delegation-narrowing rule.*

**Microsoft** — Guidance on secure agentic systems and the Entra Agent ID work on distinct agent identity; the reference for workload identity, the tool registry, and layered controls. *Foundational for CT-02, CT-03, CT-04, CT-08.*

**Anthropic** — Published guidance on agent and tool safety, sandboxing, and content-boundary handling; the reference for execution isolation and the input/output boundary. *Foundational for CT-06, CT-07.*

**MCP specification and the EMA standard** — The Model Context Protocol specification (including its 2025 authorization revisions: OAuth 2.1 with PKCE, resource indicators, and the token-passthrough prohibition) and Enterprise-Managed Authorization, the enterprise authorization standard that pulls authorization back to the identity provider. These are primary but *moving* — recent and still settling — so the book cites them as normative for structure while flagging that specifics may change. *Foundational for CT-14 and the MCP requirements (REQ-050, REQ-014).*

## Secondary signals

These are the timely, provisional sources that feed monitoring (REQ-032), the continuous-update process, and the runtime posture. They are cited as evidence of current conditions, never as the sole basis for a SHALL.

**Vulnerability and exploitation feeds** — CVE for disclosed vulnerabilities, CISA's KEV catalog for what is being exploited in the wild, and EPSS for exploitation probability. These are the inputs the runtime posture's `critical_vulnerability` and `active_exploitation` triggers act on. *Feeds CT-10, REQ-032, Chapter 8 triggers.*

**Vendor and lab threat research** — Security research from groups such as Palo Alto Unit 42, CrowdStrike, Wiz, Lakera, and Trail of Bits, reporting agent-specific attack techniques (tool poisoning, rug-pulls, injection variants) as they emerge. Valuable for currency; treated as signal to verify, not settled fact. *Feeds CT-10, CT-11, the evaluation suite.*

**MCP ecosystem measurements** — Surveys of deployed MCP servers reporting authorization posture (for example, that a large fraction expose no authentication or rely on long-lived static keys). Used in Chapter 5 to motivate the MCP requirements; cited as a snapshot of ecosystem state, which is expected to change. *Feeds the MCP configuration checklist.*

**Industry analysis** — Adoption and risk analysis from firms such as Deloitte, McKinsey, and Accenture. Useful for framing scale and trajectory; not a source of technical requirements. *Context only.*

## A note on coverage and gaps

The source base is strongest on identity, tool safety, and the injection family, where multiple primary sources converge, and it is thinner on two fronts the reader should know about. First, the runtime-posture design (Chapter 8) synthesizes the general risk-management posture of NIST and CISA into a specific state machine that is the book's own construction rather than a direct citation; it is grounded in those primaries but goes beyond them. Second, the MCP and EMA material rests on standards that are current and authoritative but still evolving, so any specific mechanism cited there should be re-checked against the live specification before it is relied on for a compliance claim. Both gaps are flagged here rather than papered over, consistent with the book's rule that it uses SHALL only where it is confident and says so where it is not. The Changelog carries the errata process for correcting any source attribution that proves wrong.
