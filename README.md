# AI Agent Security: A Field Manual

A living manual on threats, controls, and verifiable assurance for autonomous (tool-using) AI agents. Written for engineers, security architects, and the people who have to sign off on shipping an agent.

This repository is the **source of the free book (Tier 1 / "1F")**, published on Leanpub with GitHub sync. The paid execution layer (the verification kit) lives in a separate repository.

## The core discipline

The book is organized around one chain that runs top to bottom:

> **Threat → Control → Requirement → Verification**

Every threat class (TH-01..TH-10) maps to controls (CT-*), expressed as normative RFC 2119 requirements (REQ-*), each tied to a pass/fail verification test (VT-*). Traceability is the product.

## Repository layout (Leanpub / Markua)

```
manuscript/
  Book.txt         # ordered list of files Leanpub includes in the book
  Sample.txt       # files included in the free sample
  frontmatter.md   # title, license, preface, how-to-read
  partN.md         # part dividers
  chNN-*.md        # chapters
  appendix-*.md    # cross-reference tables, source register
  changelog.md     # revision history / errata (living document)
  images/          # figures
  resources/       # inclusion assets
LICENSE            # CC BY-NC-ND 4.0 (text only; kit is separate)
```

Leanpub reads `manuscript/Book.txt` to assemble the book, and `manuscript/Sample.txt` for the free sample. Pushing to this repo triggers a Leanpub GitHub-sync preview.

## License

The manual text is licensed **CC BY-NC-ND 4.0** — read, share, and quote with attribution; no commercial use, no derivatives. See [LICENSE](LICENSE). The taxonomies and requirement identifiers are published openly, in part as prior art.

The companion **AI Agent Security Verification Kit** (executable tests, checklists, tooling) is a separate product under its own license and is **not** covered by CC BY-NC-ND.

## Status

First edition, living document. See `manuscript/changelog.md` for the current revision and errata process.
