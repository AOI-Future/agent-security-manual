{backmatter}

# Acknowledgments

A book like this one is not finished when it is published. It is a specification, and a specification improves the way any specification improves: someone reads it carefully, finds the place where it asks the wrong question, and says so. That is a gift, and it deserves to be recorded rather than quietly absorbed.

This section names the readers whose questions changed the text. Where a contribution led to a normative change — a new requirement, a new test, a corrected claim — the changelog records what changed and this section records who prompted it. The intent is that a reader can always see that a revision came from somewhere real.

**Second revision.** A reader on LinkedIn asked whether the verification plan contained any test that fails when the evidence trail is *present but fabricated*, rather than merely absent, and described a real case in which their own report claimed full evidence coverage over a score the reporting system had partly invented. Nothing was missing on the page; that was precisely what let it pass. The question was correct and the book did not answer it. REQ-055, REQ-056, and REQ-057, the rewritten definition of VT-E in Chapter 10, and the qualification of what an RFC 3161 timestamp does and does not prove all exist because of that question.

**Third revision.** The same reader returned with the harder half of the question: does anything in the specification stop a control from attesting to its own coverage? They described a harness in which a judge returned nothing, the nothing was recorded as a hard zero, and the harness then stamped the evidence sufficient — one failure in which an absent result was silently rounded to a value, and a second in which the system issued a verdict about its own output. Nothing in REQ-000 to REQ-054 prevented either, and the sharpest instance was in this book's own assurance layer, where the coverage report required by REQ-054 is produced and checked by the same kit it describes. REQ-058, REQ-059, REQ-060, and REQ-061 exist because of that question, together with the three further failure modes the review turned up while working it out against external precedent. Two questions from one reader have now moved this specification twice.

If you find the next gap, the same applies. Corrections and challenges are welcome, and the errata process in the changelog describes how they are handled.

# About the Companion Kit

This book is the free explanation layer. The paid execution layer is a separate product: **the AI Agent Security Verification Kit.**

The book tells you *what* to verify and *why*. The kit *runs* the verification and produces the evidence:

- an executable injection test suite and MCP/EMA configuration checklist that runs with one command and returns PASS/FAIL against a target agent configuration;
- a single build artifact combining machine-readable JSON results with a human-readable PDF report;
- an RFC 3161 timestamp over that artifact, producing a tamper-evident record designed to be handed to a CISO or auditor alongside the system context they need for review.

The kit exists because "we tested our agent" is worth nothing without evidence, and producing that evidence by hand is the tedious part.

**Run the full verification suite and generate the evidence package:**
<https://0xshugo.gumroad.com/l/AI-Agent?utm_source=leanpub_en&utm_medium=book&utm_campaign=agent_security_funnel&utm_content=en_backmatter>
