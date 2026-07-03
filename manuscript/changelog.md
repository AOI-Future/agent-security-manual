# Changelog & Errata

This book is a living document. Agent security moves faster than any print cycle, so the book carries a version history rather than pretending to be finished. This page records what changed between revisions and how errors are corrected. The current edition is always named in the front matter; this page is where you confirm what that edition contains.

## Revision history

**First edition, 2026 — initial publication of Parts I–IV.** The complete Threat → Control → Requirement → Verification chain: the threat taxonomy (TH-01..TH-10), the control taxonomy (CT-01..CT-15), the identity, tool, and untrusted-content control chapters, the operations chapters on monitoring, incident response, and runtime posture, and the assurance material — the requirement specification (REQ-000..REQ-054), the verification test plan (VT-S/VT-D/VT-E/VT-A), and the governance record. Appendices A (cross-reference tables) and B (source register) included. This is the baseline against which all later revisions are described.

## Errata process

Errors in a security manual are not embarrassments to hide; they are defects to fix in the open, because a reader relying on a wrong SHALL is worse off than one who knows the book is uncertain. When an error is found — a mis-stated requirement, a broken cross-reference, a source attribution that does not hold, a control that later evidence shows to be insufficient — it is corrected here first, with the date, the location, the nature of the error, and the correction, so that a reader can tell exactly what changed and why. Substantive corrections that change a normative claim (a SHALL that becomes a SHOULD, or vice versa) are called out explicitly rather than folded silently into a new printing, because the whole value of RFC 2119 language is defeated if the reader cannot trust that a SHALL meant SHALL in the edition they read. Corrections to moving external standards — the MCP authorization model, the EMA standard, this quarter's attack techniques — are tracked the same way, and Appendix B flags in advance which material is expected to move. Reports of errors are welcome; the current contact and submission route are on the distribution page.

## Known items carried forward

One source-level discrepancy is recorded here from first publication. The attack-to-control-to-requirement source mapping assigned threat TH-10 (model and service abuse) a requirement identifier, REQ-016, that does not exist in the requirement specification. It is treated throughout this edition as REQ-035, the incident-response and abuse-blocking requirement that actually carries the TH-10 path, and this substitution is to be verified against the next revision of the underlying specification. It is noted rather than silently corrected so that a reader cross-checking against the source material can see the reconciliation.

---

**Distribution page.** This book is published, free, at <https://leanpub.com/agent-security>. That page is the canonical source for the current edition, the errata above, and the route for reporting an error.
