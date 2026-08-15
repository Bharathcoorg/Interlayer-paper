# InterLayer Protocol Specification — Technical Claims QA

**Document:** InterLayer Core Protocol Specification & Technical Architecture  
**Network:** Gravity Testnet  
**Version:** v0.1 (Draft Technical Specification)  
**Date:** August 2026  
**Author:** Bharath B R ([@bharathcoorg7](https://x.com/bharathcoorg7))  
**Organization:** InterLayer Foundation  

---

## Sensitive Technical Claims Deserving Explicit Author Verification Prior to Production Mainnet Release

The following claims appear within the technical specification. While consistent with the Gravity Testnet codebase and target protocol design, they involve critical cryptographic, economic, performance, or security assumptions that should be formally benchmarked and audited prior to mainnet deployment:

| # | Domain | Specification Location | Claim Statement in Specification | Verification / Audit Recommendation |
|---|---|---|---|---|
| 1 | **Block Production Timing** | Chapter 1 (§1.1), Chapter 6 (§6.8) | "sub-500ms block targets and 10ms fast polling loops" | Perform WAN multi-region latency benchmarks across geodistributed validator nodes to verify if 500ms is achievable under high network jitter. |
| 2 | **Security & Trust Model** | Abstract (§1.0), Chapter 1 (§1.1) | "zero-trust, multi-virtual machine Layer-1/Interlayer state transition platform" | Verify that "zero-trust" is accurately scoped to validator BFT quorum assumptions rather than absolute trustlessness across external MPC signer subsets. |
| 3 | **Post-Quantum Cryptography** | Chapter 9 (§9.3), Chapter 11 (§11.22, §11.23) | "CRYSTALS-Dilithium and SPHINCS+ post-quantum signature verification" | Benchmark on-chain WASM execution cost and storage footprint of Dilithium public keys (1,312 bytes) and signatures (2,420 bytes) to confirm gas calibration feasibility. |
| 4 | **Cross-VM Atomic Rollbacks** | Chapter 3 (§3.1, §3.3), Chapter 15 (§15.1) | "If any operation produces an exception or error, the rollback operator reverts global state back to snapshot, leaving storage untouched." | Execute adversarial multi-VM fuzzing suites (e.g. out-of-gas in Move VM during an EVM-to-Move atomic call) to formally verify zero state leakage. |
| 5 | **Economic Staking & Yield Model** | Chapter 10 (§10.1, §10.3) | "Purely non-inflationary, real-yield staking model driven exclusively by network gas fees" | Model validator operational cost sustainability during early testnet phases with low organic transaction volume before fee velocity reaches equilibrium. |
| 6 | **Threshold MPC Custody Security** | Chapter 8 (§8.1–§8.5), Appendix F | "FROST-style Schnorr threshold signing with $(t, n)$ polynomial Shamir Secret Sharing" | Conduct formal cryptographic audit of DKG ceremony transcripts, nonce commitments, and emergency timeout slash conditions in `mpc-executor`. |
| 7 | **LiteVerse DePIN Watcher Mesh** | Chapter 5 (§5.1, §5.2) | "Mobile and Browser nodes participate in Tier 0/1 header verification and DA sampling" | Verify mobile background execution constraints (battery throttling, OS suspension on iOS/Android) under sustained network load. |
| 8 | **Address Mapping Bijectivity** | Chapter 4 (§4.4), Chapter 15 (§15.2) | "Deterministic 1-to-1 bijective address mapping across internal VMs and external chains" | Audit double-registration collision resistance in `pallet-handles` and `unified-address-registry` under concurrent extrinsic submissions. |
