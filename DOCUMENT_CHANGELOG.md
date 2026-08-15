# InterLayer Protocol Specification — Document Changelog

All notable formatting, editorial, typographical, and structural changes to the InterLayer Core Protocol Specification & Technical Architecture are documented below.

---

## [v0.1] — August 2026

### Document Identity & Front Matter
- Established standard document identity:
  - **Title:** InterLayer: Core Protocol Specification & Technical Architecture
  - **Network:** Gravity Testnet
  - **Version:** v0.1
  - **Status:** Draft Technical Specification
  - **Author:** Bharath B R ([@bharathcoorg7](https://x.com/bharathcoorg7))
  - **Organization:** InterLayer Foundation
- Added structured **Document Information** table before Abstract.
- Added visual classification of specification content: `[Implemented]`, `[Current Testnet Configuration]`, `[Design-Target Architecture]`, and `[Optional / Future Governance]`.
- Standardized **Implementation Status** table with clean textual labels across all 11 core components.

### Table of Contents & Structure
- Generated complete, hierarchical Table of Contents covering all 15 Chapters and 9 Appendices with anchor links.
- Fixed out-of-order and duplicate subsection numbering in Chapters 3, 4, 5, 6, 8, and 10.
- Standardized heading hierarchy: H1 (Chapter Title), H2 (Major Section), H3 (Subsection), H4 (Minor Heading).

### Pagination & Typesetting Engine
- Implemented **two-phase PDF rendering** via Playwright (Chromium) and `pypdf`:
  - **Cover Page:** Full-bleed A4 (0 margin, no white gaps, no header overlays).
  - **Body Pages:** Paginated A4 layout with running headers and dynamic `Page X of Y` footers.
- Enforced strict page-break directives: every major Chapter (1–15) and Appendix (A–I) begins on a fresh page.
- Added orphan heading prevention (`break-after: avoid; break-inside: avoid;`).
- Balanced Appendix I (Glossary of Terms) and Colophon to cleanly occupy the final page without trailing overflow fragments.

### Technical Architecture & Figure Restorations
- Restored **Figure 1**: Global State Tuple & Merkle Root Structure (`state_space_vector_diagram.png`) in Section 2.4.
- Restored **Figure 4**: Unified Address Resolution — Dual-Layer Binding & Domain Mapping (`unified_address_resolution_diagram.png`) in Section 4.4.
- Standardized sequential figure numbering across the entire document (Figures 1 through 6).
- Standardized handle naming to single-domain format (`alice@btc`, `alice@eth`, `alice@sol`, `alice@dot`, `alice@ton`), eliminating redundant double `@` prefixes.
- Preserved all 38 Substrate pallets (36 core active pallets detailed in Chapter 11 + 2 optional legacy pallets in Appendix C).
- Preserved all constants: EVM Chain ID `2021`, SS58 Prefix `106`.

### Quality Assurance Documentation
- Created `DOCUMENT_QA.md` documenting completed formatting, pagination, and typography improvements, alongside author review items.
- Created `TECHNICAL_CLAIMS_QA.md` cataloging sensitive performance, cryptographic, and economic claims for pre-mainnet benchmarking.
