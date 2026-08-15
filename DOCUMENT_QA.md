# InterLayer Protocol Specification — Document QA Report

**Document:** InterLayer Core Protocol Specification & Technical Architecture  
**Network:** Gravity Testnet  
**Version:** v0.1 (Draft Technical Specification)  
**Date:** August 2026  
**Author:** Bharath B R ([@bharathcoorg7](https://x.com/bharathcoorg7))  
**Organization:** InterLayer Foundation  

---

## 1. Formatting Fixes Completed

- **Document Identity & Front Matter**:
  - Standardized official title: *InterLayer: Core Protocol Specification & Technical Architecture*.
  - Added structured **Document Information** metadata table immediately preceding the Abstract.
  - Specified Version: `v0.1` and Status: `Draft Technical Specification`.
  - Added explicit **Document Scope & Conventions** classification distinguishing `[Implemented]`, `[Current Testnet Configuration]`, `[Design-Target Architecture]`, and `[Optional / Future Governance]` components.

- **Implementation Status Table**:
  - Replaced ad-hoc status icons/emojis with typography-safe textual status labels (`Implemented`, `Optional`, `Standalone Utility`).
  - Corrected pallet count accounting: 36 core active pallets detailed in Chapter 11 + 2 optional legacy pallets (`bridge-pallet` [915 lines], `dex-pallet` [218 lines]) preserved in codebase = 38 pallets total.

- **Handle & Address Resolution System (§4.2 & §4.4)**:
  - Standardized handle formatting to single-domain format (`alice@btc`, `alice@eth`, `alice@sol`, `alice@dot`, `alice@ton`), eliminating redundant double `@` prefixes (e.g. `@alice@btc`).
  - Fully articulated the **Dual-Layer Address Architecture**:
    - **Layer 1 (Internal Multi-VM Bindings)**: Substrate Native (SS58: 106), EVM (H160 `0x...`), SVM (Ed25519 Base58), PolkaVM (RISC-V), Move VM (`0x1::...`), CosmWasm (Actor ID).
    - **Layer 2 (External Cross-Chain Mappings)**: Bitcoin SegWit/Taproot, Ethereum L1, Solana L1, Polkadot Relay, TON Blockchain via MPC TSS key derivation.

---

## 2. Pagination Fixes Completed

- **Major Chapter Page Breaks**:
  - Enforced strict page-break directives (`page-break-before: always; break-before: page;`) for all 15 Chapters and all 9 Appendices.
  - Ensured Chapter 1 starts on a clean page immediately following the Table of Contents, preventing chapter titles from being stranded at the bottom of the TOC.
- **Orphan & Widow Control**:
  - Added `break-after: avoid` across all heading tiers (H1, H2, H3, H4) and `break-inside: avoid` on code blocks and figure containers.
  - Section 3.2 to Section 3.3 transition: Ensured Figure 3 (`Atomic Cross-VM Execution Flow`) and Section 3.3 (`Atomic Bundle Execution Engine`) maintain natural flow without orphaned introductory paragraphs.
- **Final Page Spacing & Elimination of Trailing Overflow**:
  - Appendix I (Glossary of Terms) given a dedicated clean break, allowing the 24-term glossary and official Colophon to cleanly fill Page 81 without trailing 2-line overflow pages.

---

## 3. Typography & Aesthetic Polish Completed

- **Document Typography System**:
  - Primary text: `Inter` (9.6pt, 1.58 line-height, left-aligned) for maximum technical readability.
  - Monospace identifiers and code blocks: `JetBrains Mono` (7.2pt, 1.44 line-height, dark background `#0c0c1a` with 2.5pt solid `#3636ee` accent border).
  - Headings: Bold Inter hierarchy with clean proportional margins and bottom dividers.
- **Table Formatting**:
  - Multi-page tables repeat table header rows (`thead { display: table-header-group; }`).
  - Alternating row shading (`#f6f6fc`) with clean border dividers (`0.3pt solid #dcdcf0`).
- **Figure Presentation**:
  - All 6 architecture diagrams embedded as high-resolution base64 data URIs.
  - Fixed scaling bounds (`max-width: 90%; max-height: 105mm; object-fit: contain;`) to eliminate excessive white voids while preserving crisp vector line clarity.

---

## 4. Technical Inconsistencies Requiring Author Review

| # | Topic | Location | Issue Description | Recommendation for Author |
|---|---|---|---|---|
| 1 | **Optional Pallet Governance** | Chapter 11, Appendix C | `bridge-pallet` (915 lines) and `dex-pallet` (218 lines) are preserved in `runtime/pallets/` but excluded from core active runtime composition (36 pallets). | Confirm whether these pallets will be activated via future governance referendum or deprecated in v0.2. |
| 2 | **EVM Precompile Ranges** | Chapter 7, §7.1 | Precompile addresses `0x0000...0800` through `0x0000...0804` are mapped for MEL cross-VM invocations. | Verify if standard Ethereum precompiles (`0x01`–`0x09`) remain strictly passthrough to revm. |
| 3 | **Confirmation Depth Parameterization** | Chapter 5, §5.3 | Bitcoin requires 3 confirmations (60m timeout), Ethereum 12 confirmations (180s), Solana 32 confirmations (15s). | Confirm if reorg-depth parameters will be dynamically adjusted by the LiteVerse DePIN mesh based on network hash rate variance. |

---

## 5. Remaining Known Items / Notes

- **PDF Compilation Output**: 81 physical pages (1 full-bleed cover + 80 body pages), 4.04 MB.
- **Figures Index**: Figure 1 through Figure 6 sequentially numbered and cross-referenced in Appendix B.
- **Repository Single Source of Truth**: All Markdown source, image assets, and compiled PDF synchronized with GitHub repository `Bharathcoorg/Interlayer-paper`.
