# 📜 InterLayer: A Multi-VM Execution Kernel and Unified State Machine

**Testnet Architecture, Formal Specification & Protocol Blueprint (Grey Paper)**

**Author:** Bharath B R ([@Bharathcoorg](https://forum.polkadot.network/u/Bharathcoorg))
**Version:** 2.0-TESTNET (Devnet / Testnet Specification)
**Status:** Active Grey Paper & Protocol Blueprint — Official Release Edition
**Repository:** [interlayer-gravity-testnet](https://github.com/Bharathcoorg/interlayer-gravity-testnet)

---

## Abstract

This document presents the formal Grey Paper, architectural specification, and developer protocol blueprint for the formal mathematical, cryptographic, and architectural specification of **InterLayer 2.0 (Gravity Testnet)**, a zero-trust, multi-virtual machine Layer-1/Interlayer state transition platform built on Substrate. InterLayer introduces the **Multi-VM Execution Layer (MEL)**, enabling heterogeneous, atomic contract execution across five major Virtual Machine standards—Ethereum Virtual Machine (EVM), Solana Virtual Machine (SVM), PolkaVM (RISC-V), Move VM, and CosmWasm—within a single unified state machine kernel.

Rather than relying on traditional wrapped-asset bridge contracts that lock assets on Chain A to mint synthetic tokens on Chain B, InterLayer introduces a native **LiteVerse DePIN Watcher Mesh** paired with **Threshold Multi-Party Computation (MPC TSS)**. Users receive unique per-user, per-chain deposit addresses across Bitcoin, Ethereum, Solana, and external networks; deposits are verified directly into a single unified native balance usable seamlessly across all five internal VM execution environments.

This specification establishes the formal set-theoretic definitions of the global state $\sigma$, the atomic bundle transition operator $\mathcal{A}$, the state rollback operator $\mathcal{R}$, the universal gas calibration function $\Phi_g$, the canonical unified address resolution bijection $f_{\text{map}}$, the 3-Chain HotStuff BFT consensus protocol safety and liveness proofs under BLS12-381 threshold signatures, the off-chain DKG key generation and signing protocols, the non-inflationary real-yield fee distribution equations, and exhaustive technical specifications for all 38 custom FRAME runtime pallets.

---

## Table of Contents

1. [Chapter 1: System Overview & Protocol Architecture](#chapter-1-system-overview--protocol-architecture)
2. [Chapter 2: Formal Mathematical Foundations & Global State Trias](#chapter-2-formal-mathematical-foundations--global-state-trias)
3. [Chapter 3: Multi-VM Execution Layer (MEL) Engine Architecture](#chapter-3-multi-vm-execution-layer-mel-engine-architecture)
4. [Chapter 4: Canonical Unified Address Space & Asset Invariants](#chapter-4-canonical-unified-address-space--asset-invariants)
5. [Chapter 5: LiteVerse DePIN Watcher Mesh & Liquidity Orchestration](#chapter-5-liteverse-depin-watcher-mesh--liquidity-orchestration)
6. [Chapter 6: Pipelined 3-Chain HotStuff BFT Consensus & Safety Proofs](#chapter-6-pipelined-3-chain-hotstuff-bft-consensus--safety-proofs)
7. [Chapter 7: Deep-Dive Virtual Machine Execution Adapters](#chapter-7-deep-dive-virtual-machine-execution-adapters)
8. [Chapter 8: Off-Chain Threshold MPC Signer Infrastructure (TSS)](#chapter-8-off-chain-threshold-mpc-signer-infrastructure-tss)
9. [Chapter 9: Cryptographic Foundations & Quantum Signature Engine](#chapter-9-cryptographic-foundations--quantum-signature-engine)
10. [Chapter 10: Real-Yield Economic Model & Fee Distribution Routing](#chapter-10-real-yield-economic-model--fee-distribution-routing)
11. [Chapter 11: Comprehensive Substrate Runtime Pallet Architecture (All 38 Pallets Exhaustively Detailed with Call Indices, Storage Hashers & Parameter Types)](#chapter-11-comprehensive-substrate-runtime-pallet-architecture-all-38-pallets-exhaustively-detailed-with-call-indices-storage-hashers--parameter-types)
12. [Chapter 12: Wire-Format & Binary Serialization Specifications (SCALE, RLP, Borsh, BCS, Wasm)](#chapter-12-wire-format--binary-serialization-specifications-scale-rlp-borsh-bcs-wasm)
13. [Chapter 13: Exhaustive JSON-RPC Interface & API Specification (Core, MEL, LiteVerse, MPC)](#chapter-13-exhaustive-json-rpc-interface--api-specification-core-mel-liteverse-mpc)
14. [Chapter 14: Multi-VM Smart Contract & Cross-VM Developer Integration Guide](#chapter-14-multi-vm-smart-contract--cross-vm-developer-integration-guide)
15. [Chapter 15: Formal Invariants, Verification Theorems & Architectural Mapping](#chapter-15-formal-invariants-verification-theorems--architectural-mapping)

**Appendices**

- [Appendix A: Testnet Deployment Configuration](#appendix-a-testnet-deployment-configuration)
- [Appendix B: List of Figures](#appendix-b-list-of-figures)
- [Appendix C: Public Runtime Composition Index](#appendix-c-public-runtime-composition-index)
- [Appendix D: Canonical Protocol Type Definitions](#appendix-d-canonical-protocol-type-definitions)
- [Appendix E: Gas Calibration Constants & Cross-VM Overhead Matrix](#appendix-e-gas-calibration-constants--cross-vm-overhead-matrix)
- [Appendix F: MPC Threshold Signer Protocol Listings](#appendix-f-mpc-threshold-signer-protocol-listings)
- [Appendix G: HotStuff Consensus Protocol Reference](#appendix-g-hotstuff-consensus-protocol-reference)
- [Appendix H: Atomic Execution State Machine Reference](#appendix-h-atomic-execution-state-machine-reference)
- [Appendix I: Glossary of Terms](#appendix-i-glossary-of-terms)

---

## Chapter 1: System Overview & Protocol Architecture

### 1.1 Executive Summary & Historical Context
The evolution of decentralized smart contract platforms over the past decade has produced an explosion of specialized execution machines. Ethereum introduced the Ethereum Virtual Machine (EVM) in 2015, establishing stack-based, 256-bit word execution with Solidity bytecodes. Solana introduced Sealevel in 2020, leveraging parallel eBPF bytecodes to enable non-overlapping account state concurrency. Polkadot introduced PolkaVM, bringing RISC-V zero-cost abstraction compilation to Web3. Aptos and Sui popularized Diem’s Move VM, emphasizing linear asset resources and formal memory safety. Meanwhile, Cosmos standardized CosmWasm, deploying WebAssembly (Wasm) actor model smart contracts across Tendermint chains.

While each virtual machine excels in its native domain, this diversity has created catastrophic execution fragmentation across the Web3 ecosystem. Developers are forced to rebuild smart contracts from scratch in different languages for each ecosystem. Users are trapped in isolated state silos, forced to move assets across third-party wrapped-asset bridges that lock funds on Chain A to mint synthetic tokens on Chain B. Historically, cross-chain bridge hacks have accounted for over $2.8 billion in lost protocol funds, exposing the fundamental structural risk of wrapped-asset messaging bridges.

InterLayer solves this execution and liquidity fragmentation at the root layer. Built as a native Substrate blockchain kernel, InterLayer introduces the **Multi-VM Execution Layer (MEL)**—an integrated execution engine that embeds all five major virtual machine standards (EVM, SVM, PolkaVM, Move VM, and CosmWasm) directly within a single unified Substrate state machine runtime.

### 1.2 Core Architectural Principles of the InterLayer Substrate Kernel
The design of InterLayer rests upon four foundational architectural principles:

1. **State-Level Unification Over Message-Passing Bridges**: Rather than passing asynchronous messages between distinct blockchains, InterLayer unifies storage, account balances, and contract state under a single global Merkle Patricia Trie.
2. **Heterogeneous VM Native Adapters**: VM execution environments are integrated into the runtime executive as first-class adapters (`mel-evm`, `mel-svm`, `mel-polkavm`, `mel-move`, `mel-cosmwasm`). Contracts run natively without compiling down to a lowest-common-denominator intermediate representation.
3. **Atomic Cross-VM Bundles**: Developers can assemble an atomic transaction bundle containing contract calls to multiple distinct VMs (for example, executing an EVM swap followed by a Solana program instruction in a single block). If any call fails, the entire atomic bundle rolls back cleanly without state corruption.
4. **Native Unique Deposit Addresses via LiteVerse DePIN & MPC TSS**: Users do not hold wrapped synthetic tokens. Every user is allocated unique per-user, per-chain deposit addresses on Bitcoin, Ethereum, Solana, and external networks. Deposits are monitored by the LiteVerse DePIN Watcher Mesh and signed by an off-chain Threshold Multi-Party Computation (MPC TSS) validator network, crediting a single unified native balance directly usable across all internal VMs.

### 1.3 Minimalist System Architecture Diagram

<svg viewBox="0 0 800 920" width="100%" height="auto" xmlns="http://www.w3.org/2000/svg" style="background:#ffffff; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;">
  <!-- Header Container -->
  <rect x="140" y="30" width="520" height="90" rx="16" ry="16" fill="#ffffff" stroke="#111111" stroke-width="2.5"/>
  <path d="M 375 50 L 400 62 L 425 50 L 400 38 Z M 375 62 L 400 74 L 425 62 M 375 74 L 400 86 L 425 74" fill="none" stroke="#111111" stroke-width="2.5" stroke-linejoin="round"/>
  <text x="400" y="98" font-size="20" font-weight="700" fill="#111111" text-anchor="middle">Substrate Runtime</text>
  <text x="400" y="112" font-size="13" fill="#555555" text-anchor="middle">Core Blockchain Infrastructure</text>

  <!-- Arrow Down 1 -->
  <path d="M 400 120 L 400 150" stroke="#111111" stroke-width="2.5"/>

  <!-- MEL Container -->
  <rect x="40" y="150" width="720" height="150" rx="16" ry="16" fill="#ffffff" stroke="#111111" stroke-width="2.5"/>
  <text x="400" y="180" font-size="20" font-weight="700" fill="#111111" text-anchor="middle">MEL – Multi-VM Execution Layer</text>
  <text x="400" y="198" font-size="13" fill="#555555" text-anchor="middle">Orchestration  •  Routing  •  Interoperability</text>

  <!-- MEL Pillars -->
  <line x1="200" y1="215" x2="200" y2="280" stroke="#e0e0e0" stroke-width="1.5"/>
  <line x1="360" y1="215" x2="360" y2="280" stroke="#e0e0e0" stroke-width="1.5"/>
  <line x1="540" y1="215" x2="540" y2="280" stroke="#e0e0e0" stroke-width="1.5"/>

  <text x="120" y="240" font-size="15" font-weight="600" fill="#111111" text-anchor="middle">Routing</text>
  <text x="280" y="240" font-size="15" font-weight="600" fill="#111111" text-anchor="middle">Orchestration</text>
  <text x="450" y="240" font-size="15" font-weight="600" fill="#111111" text-anchor="middle">Shared Services</text>
  <text x="640" y="240" font-size="15" font-weight="600" fill="#111111" text-anchor="middle">Interoperability</text>

  <!-- Arrow Down 2 (5 Split Arrows) -->
  <path d="M 115 300 L 115 340" stroke="#111111" stroke-width="2"/>
  <path d="M 255 300 L 255 340" stroke="#111111" stroke-width="2"/>
  <path d="M 400 300 L 400 340" stroke="#111111" stroke-width="2"/>
  <path d="M 545 300 L 545 340" stroke="#111111" stroke-width="2"/>
  <path d="M 685 300 L 685 340" stroke="#111111" stroke-width="2"/>

  <!-- 5 VM Boxes -->
  <!-- EVM -->
  <rect x="40" y="340" width="130" height="150" rx="14" ry="14" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="105" y="380" font-size="17" font-weight="700" fill="#111111" text-anchor="middle">EVM</text>
  <text x="105" y="445" font-size="12" fill="#444444" text-anchor="middle">Smart Contracts</text>
  <text x="105" y="462" font-size="12" fill="#444444" text-anchor="middle">&amp; dApps</text>

  <!-- SVM -->
  <rect x="190" y="340" width="130" height="150" rx="14" ry="14" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="255" y="380" font-size="17" font-weight="700" fill="#111111" text-anchor="middle">SVM</text>
  <text x="255" y="445" font-size="12" fill="#444444" text-anchor="middle">Solana Programs</text>
  <text x="255" y="462" font-size="12" fill="#444444" text-anchor="middle">&amp; dApps</text>

  <!-- Move VM -->
  <rect x="335" y="340" width="130" height="150" rx="14" ry="14" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="400" y="380" font-size="17" font-weight="700" fill="#111111" text-anchor="middle">Move VM</text>
  <text x="400" y="445" font-size="12" fill="#444444" text-anchor="middle">Move Contracts</text>
  <text x="400" y="462" font-size="12" fill="#444444" text-anchor="middle">&amp; dApps</text>

  <!-- CosmWasm -->
  <rect x="480" y="340" width="130" height="150" rx="14" ry="14" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="545" y="380" font-size="17" font-weight="700" fill="#111111" text-anchor="middle">CosmWasm</text>
  <text x="545" y="445" font-size="12" fill="#444444" text-anchor="middle">Wasm Contracts</text>
  <text x="545" y="462" font-size="12" fill="#444444" text-anchor="middle">&amp; dApps</text>

  <!-- PolkaVM -->
  <rect x="625" y="340" width="130" height="150" rx="14" ry="14" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="690" y="380" font-size="17" font-weight="700" fill="#111111" text-anchor="middle">PolkaVM</text>
  <text x="690" y="445" font-size="12" fill="#444444" text-anchor="middle">PolkaVM Contracts</text>
  <text x="690" y="462" font-size="12" fill="#444444" text-anchor="middle">&amp; dApps</text>

  <!-- Arrow Down 3 (5 Merge Arrows) -->
  <path d="M 115 490 L 115 530 L 400 530 L 400 550" stroke="#111111" stroke-width="2" fill="none"/>
  <path d="M 255 490 L 255 530" stroke="#111111" stroke-width="2"/>
  <path d="M 545 490 L 545 530" stroke="#111111" stroke-width="2"/>
  <path d="M 685 490 L 685 530 L 400 530" stroke="#111111" stroke-width="2" fill="none"/>

  <!-- Shared Runtime Services Container -->
  <rect x="40" y="550" width="720" height="130" rx="16" ry="16" fill="#ffffff" stroke="#111111" stroke-width="2.5"/>
  <text x="400" y="580" font-size="18" font-weight="700" fill="#111111" text-anchor="middle">Shared Runtime Services</text>

  <text x="80" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Accounts</text>
  <text x="160" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Balances</text>
  <text x="240" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Identity</text>
  <text x="320" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Staking</text>
  <text x="400" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Governance</text>
  <text x="490" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Messaging</text>
  <text x="570" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Storage</text>
  <text x="650" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">Oracles</text>
  <text x="720" y="630" font-size="13" font-weight="600" fill="#111111" text-anchor="middle">More</text>

  <!-- Arrow Down 4 -->
  <path d="M 400 680 L 400 720" stroke="#111111" stroke-width="2.5"/>

  <!-- Developer & Ecosystem Layer -->
  <rect x="40" y="720" width="720" height="140" rx="16" ry="16" fill="#ffffff" stroke="#111111" stroke-width="2.5" stroke-dasharray="6,6"/>
  <text x="400" y="755" font-size="18" font-weight="700" fill="#111111" text-anchor="middle">Built by Developers &amp; Ecosystem</text>

  <text x="95" y="810" font-size="14" font-weight="600" fill="#111111" text-anchor="middle">dApps</text>
  <text x="210" y="810" font-size="14" font-weight="600" fill="#111111" text-anchor="middle">Bridge Builders</text>
  <text x="345" y="810" font-size="14" font-weight="600" fill="#111111" text-anchor="middle">Liquidity Providers</text>
  <text x="475" y="810" font-size="14" font-weight="600" fill="#111111" text-anchor="middle">Institutions</text>
  <text x="590" y="810" font-size="14" font-weight="600" fill="#111111" text-anchor="middle">Agents / Bots</text>
  <text x="695" y="810" font-size="14" font-weight="600" fill="#111111" text-anchor="middle">Users</text>
</svg>

---

## Chapter 2: Formal Mathematical Foundations & Global State Trias

### 2.1 Mathematical Notations & Set Theory
Throughout this paper, we adopt standard set-theoretic and cryptographic notation:
- $\mathbb{B} \equiv \{0, 1\}$: The set of binary bits.
- $\mathbb{B}^N$: The set of byte sequences of length $N$. $\mathbb{B}^* \equiv \bigcup_{N=0}^{\infty} \mathbb{B}^N$.
- $\mathbb{H}^{256} \equiv \mathbb{B}^{32}$: The set of 256-bit cryptographic hash outputs.
- $\mathbb{H}^{160} \equiv \mathbb{B}^{20}$: The set of 160-bit address hashes.
- $\mathbb{N}_0, \mathbb{N}_1$: Non-negative integers $\{0, 1, 2, \dots\}$ and positive integers $\{1, 2, 3, \dots\}$.
- $\mathcal{VM} \equiv \{\text{EVM}, \text{SVM}, \text{PolkaVM}, \text{Move}, \text{CosmWasm}\}$: The domain of supported VM execution engines.

### 2.2 Global State Tuple $\sigma$
Let $\mathbf{\mathbb{S}}$ denote the set of all valid global state configurations. The global state $\sigma \in \mathbf{\mathbb{S}}$ is formally defined as an 8-tuple of sub-states:

$$\sigma \equiv \Big( \Sigma_{\text{EVM}}, \, \Sigma_{\text{SVM}}, \, \Sigma_{\text{PolkaVM}}, \, \Sigma_{\text{Move}}, \, \Sigma_{\text{CosmWasm}}, \, \Sigma_{\text{Unified}}, \, \Sigma_{\text{Staking}}, \, \Sigma_{\text{MPC}} \Big)$$

Where each sub-state $\Sigma_k$ represents a key-value trie mapping:

$$\Sigma_k : \mathcal{K}_k \longrightarrow \mathcal{V}_k$$

- $\Sigma_{\text{EVM}}$: Storage mapping account nonces, balances, code hashes ($\mathbb{H}^{256} \to \mathbb{B}^*$), and storage slots ($\mathbb{U}^{256} \to \mathbb{U}^{256}$).
- $\Sigma_{\text{SVM}}$: Storage mapping Solana Program Derived Addresses (PDAs), account data vectors (up to 10MB per account), owner pubkeys, and lamport balances.
- $\Sigma_{\text{PolkaVM}}$: Storage mapping RISC-V contract bytecodes, memory page tables, and PSP-22/PSP-34 state items.
- $\Sigma_{\text{Move}}$: Storage mapping resource key tags `(Address, StructTag) => ResourceBytes` and module bytecodes `(Address, ModuleName) => Bytecode`.
- $\Sigma_{\text{CosmWasm}}$: Storage mapping WebAssembly state keys, contract instantiations, and Bech32 address balances.
- $\Sigma_{\text{Unified}}$: Mapping of canonical 32-byte unified addresses to sub-VM handles, native balances, and cross-chain deposit addresses.
- $\Sigma_{\text{Staking}}$: Active validator set $\mathcal{V}_{\text{validators}}$, delegator pools, unbonding queues, slashing evidence logs, and real-yield fee escrow pools.
- $\Sigma_{\text{MPC}}$: DKG polynomial secret shares, threshold signature request queues, and active master public keys.

### 2.3 Merkle Patricia Trie Commitments & Block Transition
The global state root hash commitment function $\mathcal{H}: \mathbf{\mathbb{S}} \longrightarrow \mathbb{H}^{256}$ calculates the root hash of the global Substrate Merkle Patricia Trie:

$$\mathcal{H}(\sigma) = \text{Blake2b-256}\left( \mathcal{H}(\Sigma_{\text{EVM}}) \,||\, \mathcal{H}(\Sigma_{\text{SVM}}) \,||\, \mathcal{H}(\Sigma_{\text{PolkaVM}}) \,||\, \mathcal{H}(\Sigma_{\text{Move}}) \,||\, \mathcal{H}(\Sigma_{\text{CosmWasm}}) \,||\, \mathcal{H}(\Sigma_{\text{Unified}}) \,||\, \mathcal{H}(\Sigma_{\text{Staking}}) \,||\, \mathcal{H}(\Sigma_{\text{MPC}}) \right)$$

The block state transition function $\Upsilon$ maps a valid global state $\sigma_{n-1}$ and block $B_n$ to the resulting state $\sigma_n$:

$$\sigma_n = \Upsilon(\sigma_{n-1}, B_n)$$

### 2.4 State Space Vector Diagram

<svg viewBox="0 0 800 420" width="100%" height="auto" xmlns="http://www.w3.org/2000/svg" style="background:#ffffff; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;">
  <rect x="40" y="30" width="720" height="360" rx="16" ry="16" fill="#ffffff" stroke="#111111" stroke-width="2.5"/>
  <text x="400" y="65" font-size="18" font-weight="700" fill="#111111" text-anchor="middle">Global State Tuple σ Structure &amp; Merkle Root H(σ)</text>
  <line x1="60" y1="85" x2="740" y2="85" stroke="#e0e0e0" stroke-width="1.5"/>

  <!-- Substates -->
  <rect x="60" y="110" width="150" height="70" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="135" y="140" font-size="14" font-weight="600" text-anchor="middle">Σ_EVM</text>
  <text x="135" y="160" font-size="11" fill="#666666" text-anchor="middle">revm Account Storage</text>

  <rect x="225" y="110" width="150" height="70" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="300" y="140" font-size="14" font-weight="600" text-anchor="middle">Σ_SVM</text>
  <text x="300" y="160" font-size="11" fill="#666666" text-anchor="middle">Solana Account Vectors</text>

  <rect x="390" y="110" width="150" height="70" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="465" y="140" font-size="14" font-weight="600" text-anchor="middle">Σ_PolkaVM</text>
  <text x="465" y="160" font-size="11" fill="#666666" text-anchor="middle">RISC-V Contract State</text>

  <rect x="555" y="110" width="180" height="70" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="645" y="140" font-size="14" font-weight="600" text-anchor="middle">Σ_Move &amp; Σ_CosmWasm</text>
  <text x="645" y="160" font-size="11" fill="#666666" text-anchor="middle">Resources &amp; Wasmi Store</text>

  <!-- Merkle Combine Arrows -->
  <path d="M 135 180 L 135 230 L 400 230" fill="none" stroke="#111111" stroke-width="1.5"/>
  <path d="M 300 180 L 300 230" stroke="#111111" stroke-width="1.5"/>
  <path d="M 465 180 L 465 230" stroke="#111111" stroke-width="1.5"/>
  <path d="M 645 180 L 645 230 L 400 230" fill="none" stroke="#111111" stroke-width="1.5"/>

  <path d="M 400 230 L 400 260" stroke="#111111" stroke-width="2"/>

  <!-- Merkle Root Box -->
  <rect x="250" y="260" width="300" height="90" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="400" y="295" font-size="16" font-weight="700" text-anchor="middle">Blake2b-256 State Root H(σ)</text>
  <text x="400" y="325" font-size="12" fill="#555555" text-anchor="middle">Committed in Substrate Block Header</text>
</svg>

---

## Chapter 3: Multi-VM Execution Layer (MEL) Engine Architecture

![Figure 1: MEL Multi-VM Architecture  The five execution adapters (EVM, SVM, PolkaVM, Move, CosmWasm) connected through the unified MEL orchestration layer to the shared Substrate state trie.](images/mel_architecture.png)

### 3.1 Intuitive Explanation of Multi-VM Atomic Execution
In traditional single-VM networks (like Ethereum or Solana), smart contract execution is constrained to a single execution environment. If a dApp requires logic across Solidity (EVM) and Solana (SVM), the user must perform two asynchronous transactions connected through an external cross-chain messaging bridge. This introduces multi-block latency, bridge fee overhead, and severe vulnerability to front-running and bridge exploits.

MEL resolves this by providing a unified meta-execution layer. When an **Atomic Bundle** $\mathcal{B} = [C_1, C_2, \dots, C_N]$ is submitted to the network:
1. **Validation & Checkpoint**: MEL validates the context deadline and generates a baseline state snapshot $\mathcal{S}_0 = \text{Snapshot}(\sigma_0)$.
2. **Sequential Execution**: MEL passes call $C_1$ to the source VM adapter (e.g. `mel-evm`), staging state mutations in transient memory. It then passes call $C_2$ to the target VM adapter (e.g. `mel-svm`), staging its state mutations.
3. **Atomic Commit**: If all operations execute without error ($\epsilon_i = \emptyset$), staged state diffs commit simultaneously to global storage $\sigma_N$.
4. **Instant Rollback**: If any operation produces an exception or error ($\epsilon_i \neq \emptyset$), the rollback operator $\mathcal{R}(\sigma_i, \mathcal{S}_0)$ reverts global state back to $\sigma_0$, leaving storage untouched.

### 3.2 Atomic Execution Flowchart

<svg viewBox="0 0 800 480" width="100%" height="auto" xmlns="http://www.w3.org/2000/svg" style="background:#ffffff; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;">
  <text x="400" y="35" font-size="20" font-weight="700" fill="#111111" text-anchor="middle">MEL Atomic Execution &amp; Rollback Flowchart</text>

  <!-- Step 1: Input Bundle -->
  <rect x="40" y="60" width="150" height="100" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="115" y="95" font-size="14" font-weight="700" text-anchor="middle">Phase 1: Validate</text>
  <text x="115" y="118" font-size="11" fill="#444444" text-anchor="middle">Check deadline &amp;</text>
  <text x="115" y="133" font-size="11" fill="#444444" text-anchor="middle">Create Snapshot S_0</text>

  <path d="M 190 110 L 230 110" stroke="#111111" stroke-width="2"/>

  <!-- Step 2: Source Execution -->
  <rect x="230" y="60" width="160" height="100" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="310" y="95" font-size="14" font-weight="700" text-anchor="middle">Phase 2: Source VM</text>
  <text x="310" y="118" font-size="11" fill="#444444" text-anchor="middle">Execute Call C_1</text>
  <text x="310" y="133" font-size="11" fill="#444444" text-anchor="middle">Stage State Diff</text>

  <path d="M 390 110 L 430 110" stroke="#111111" stroke-width="2"/>

  <!-- Step 3: Target Execution -->
  <rect x="430" y="60" width="160" height="100" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="510" y="95" font-size="14" font-weight="700" text-anchor="middle">Phase 3: Target VM</text>
  <text x="510" y="118" font-size="11" fill="#444444" text-anchor="middle">Execute Call C_2</text>
  <text x="510" y="133" font-size="11" fill="#444444" text-anchor="middle">Stage State Diff</text>

  <path d="M 590 110 L 630 110" stroke="#111111" stroke-width="2"/>

  <!-- Step 4: Decision -->
  <rect x="630" y="60" width="130" height="100" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="695" y="95" font-size="14" font-weight="700" text-anchor="middle">All Succeeded?</text>
  <text x="695" y="125" font-size="12" fill="#444444" text-anchor="middle">Status ε_i == Ø?</text>

  <!-- Success Branch -->
  <path d="M 695 160 L 695 240 L 510 240 L 510 270" fill="none" stroke="#111111" stroke-width="2"/>
  <text x="630" y="230" font-size="12" font-weight="700" fill="#111111">YES (Commit)</text>

  <rect x="430" y="270" width="160" height="90" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="510" y="305" font-size="14" font-weight="700" text-anchor="middle">Commit State σ_N</text>
  <text x="510" y="330" font-size="11" fill="#444444" text-anchor="middle">Write Diffs to Storage</text>

  <!-- Rollback Branch -->
  <path d="M 695 160 L 695 315 L 270 315 L 270 270" fill="none" stroke="#111111" stroke-width="2"/>
  <text x="600" y="305" font-size="12" font-weight="700" fill="#111111">NO (Revert)</text>

  <rect x="190" y="270" width="160" height="90" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="270" y="305" font-size="14" font-weight="700" text-anchor="middle">Rollback R(σ_i, S_0)</text>
  <text x="270" y="330" font-size="11" fill="#444444" text-anchor="middle">Restore State σ_0</text>
</svg>

![Figure 2: Atomic Cross-VM Execution Flow  The 5-phase pipeline showing snapshot creation, source VM execution, target VM execution, validation, and commit/rollback branching.](images/atomic_execution_flow.png)

### 3.3 Formal Atomic Bundle Operator $\mathcal{A}$
An **Atomic Bundle** $\mathcal{B}$ contains a vector of $N$ contract operations across arbitrary VM types:

$$\mathcal{B} = \big[ C_1, \, C_2, \, \dots, \, C_N \big], \quad C_i = (\text{VM}_i, \, a_{\text{target}}, \, \mathbf{d}_i, \, g_i)$$

The atomic state transition operator $\mathcal{A}$ operates on initial state $\sigma_0$:

$$\mathcal{A}(\sigma_0, \mathcal{B}) = \begin{cases}
(\sigma_N, \, \text{State}_{\text{Committed}}), & \text{if } \forall i \in \{1 \dots N\}, \, \epsilon_i = \emptyset \text{ and } \sum \Phi_g(g_i, \text{VM}_i) \le g_{\text{limit}} \\
(\sigma_0, \, \text{State}_{\text{RolledBack}}), & \text{if } \exists i \in \{1 \dots N\} \text{ s.t. } \epsilon_i \neq \emptyset \text{ or } t > t_{\text{deadline}}
\end{cases}$$

### 3.4 Universal Gas Metering & Calibration Function $\Phi_g$
To compute transaction fees fairly across heterogeneous compute engines, MEL maps native VM instruction units to a standardized gas metric using calibration multipliers $\gamma_k$:

$$\Phi_g(g_k, k) = \left\lceil g_k \cdot \gamma_k \right\rceil$$

$$\begin{aligned}
\gamma_{\text{EVM}} &= 1.00 \quad (\text{1 EVM Gas Unit}) \\
\gamma_{\text{SVM}} &= 0.05 \quad (\text{1 Solana Compute Unit (CU)} = 0.05 \text{ Standard Gas}) \\
\gamma_{\text{PolkaVM}} &= 0.01 \quad (\text{1 RISC-V Cycle} = 0.01 \text{ Standard Gas}) \\
\gamma_{\text{Move}} &= 0.80 \quad (\text{1 Move Gas Unit} = 0.80 \text{ Standard Gas}) \\
\gamma_{\text{CosmWasm}} &= 0.10 \quad (\text{1 Wasmi Instruction Step} = 0.10 \text{ Standard Gas})
\end{aligned}$$

Total fee $F_{\text{tx}}$ for atomic bundle $\mathcal{B}$:

$$F_{\text{tx}} = \left( \sum_{i=1}^N \Phi_g(g_i, \text{VM}_i) \right) \cdot P_{\text{base\_gas}} + F_{\text{atomic\_premium}}$$

The admission check uses the same rounded unit: $\sum_i \Phi_g(g_i, \text{VM}_i) + G_{\text{coord}} \leq g_{\text{limit}}$, where $G_{\text{coord}}$ is the published cross-VM coordination charge. Implementations MUST reject a bundle before execution if this bound or its declared maximum fee cannot be satisfied; they MUST NOT silently round a fractional calibrated charge downward.

---

## Chapter 4: Canonical Unified Address Space & Asset Invariants

### 4.1 Address Resolution Bijection $f_{\text{map}}$
Every account entity on InterLayer is assigned a canonical 32-byte unified address $a_{\text{unified}} \in \mathbb{B}^{32}$.

For VM $k$, let $\mathcal{U}_k \subseteq \mathbb{B}^{32}$ be the set of registered unified addresses and let $\mathcal{A}_k$ be that VM's allocated address set. The registry maintains the following *restricted* bijection:

$$f_{\text{map},k}: \mathcal{U}_k \xleftrightarrow[\text{resolve}_{k}]{\text{register}_{k}} \mathcal{A}_k$$

The restriction is essential: a global bijection from 32-byte identities to a 20-byte EVM address space cannot exist. At registration, the registry records a unique native address and rejects a collision; hash-derived values are candidate addresses only, never a proof of uniqueness. Consequently, reverse resolution is always performed through the committed registry record, not by reversing a hash.

The native encodings are $\mathbb{B}^{20}$ hexadecimal for EVM, $\mathbb{B}^{32}$ Base58 for SVM, a 32-byte SS58-encoded account identifier for PolkaVM, the chain-configured Move address width, and Bech32 with human-readable prefix `il` for CosmWasm. A mapping is valid only while its handle, ownership proof, domain, and address-format checks remain active on-chain.

### 4.2 Unified Address Resolution Diagram

<svg viewBox="0 0 800 360" width="100%" height="auto" xmlns="http://www.w3.org/2000/svg" style="background:#ffffff; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;">
  <text x="400" y="35" font-size="18" font-weight="700" fill="#111111" text-anchor="middle">Unified Address Bijection f_map Mapping</text>

  <!-- Central Unified Address -->
  <rect x="250" y="60" width="300" height="60" rx="12" fill="#ffffff" stroke="#111111" stroke-width="2.5"/>
  <text x="400" y="88" font-size="15" font-weight="700" text-anchor="middle">a_unified ∈ B^32 (32-byte Key)</text>
  <text x="400" y="106" font-size="11" fill="#555555" text-anchor="middle">Canonical User Identity</text>

  <!-- Arrows to 5 VM Formats -->
  <path d="M 280 120 L 100 200" stroke="#111111" stroke-width="1.5"/>
  <path d="M 340 120 L 250 200" stroke="#111111" stroke-width="1.5"/>
  <path d="M 400 120 L 400 200" stroke="#111111" stroke-width="1.5"/>
  <path d="M 460 120 L 550 200" stroke="#111111" stroke-width="1.5"/>
  <path d="M 520 120 L 700 200" stroke="#111111" stroke-width="1.5"/>

  <!-- 5 Targets -->
  <rect x="30" y="200" width="140" height="90" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="100" y="230" font-size="13" font-weight="700" text-anchor="middle">EVM Address</text>
  <text x="100" y="255" font-size="11" fill="#555555" text-anchor="middle">20-byte Hex</text>
  <text x="100" y="272" font-size="10" fill="#777777" text-anchor="middle">0x71C...a29</text>

  <rect x="180" y="200" width="140" height="90" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="250" y="230" font-size="13" font-weight="700" text-anchor="middle">SVM Pubkey</text>
  <text x="250" y="255" font-size="11" fill="#555555" text-anchor="middle">32-byte Base58</text>
  <text x="250" y="272" font-size="10" fill="#777777" text-anchor="middle">7xK8...mP9</text>

  <rect x="330" y="200" width="140" height="90" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="400" y="230" font-size="13" font-weight="700" text-anchor="middle">PolkaVM ID</text>
  <text x="400" y="255" font-size="11" fill="#555555" text-anchor="middle">32-byte SS58</text>
  <text x="400" y="272" font-size="10" fill="#777777" text-anchor="middle">5Grw...bQ5</text>

  <rect x="480" y="200" width="140" height="90" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="550" y="230" font-size="13" font-weight="700" text-anchor="middle">Move Address</text>
  <text x="550" y="255" font-size="11" fill="#555555" text-anchor="middle">16-byte Hex</text>
  <text x="550" y="272" font-size="10" fill="#777777" text-anchor="middle">0x00...01</text>

  <rect x="630" y="200" width="140" height="90" rx="10" fill="#ffffff" stroke="#111111" stroke-width="1.5"/>
  <text x="700" y="230" font-size="13" font-weight="700" text-anchor="middle">CosmWasm</text>
  <text x="700" y="255" font-size="11" fill="#555555" text-anchor="middle">Bech32 String</text>
  <text x="700" y="272" font-size="10" fill="#777777" text-anchor="middle">il1q2w...x8</text>
</svg>

### 4.3 Balance Conservation Invariant
Let $\text{Bal}_{\text{IL}}(a, k)$ denote native `IL` tokens held by unified address $a$ within sub-state $k$. Global balance conservation requires:

$$\sum_{a \in \text{Accounts}} \left( \sum_{k \in \mathcal{VM}} \text{Bal}_{\text{IL}}(a, k) + \text{Bal}_{\text{Staked}}(a) + \text{Bal}_{\text{Escrow}}(a) \right) = \text{Supply}_{\text{Total}}$$

### 4.4 Implementation: `unified-address-registry` Pallet

The address resolution system is implemented in the **Unified Address Registry** pallet pallet . This section documents the actual data structures, storage layout, and extrinsic interface.

#### 4.4.1 Chain Domain Enumeration

The system supports six external chain domains, each corresponding to a distinct address format:

```rust
pub enum ChainDomain {
    Ethereum,     // 20-byte Keccak-256 derived addresses
    Bitcoin,      // Bech32/Base58Check addresses
    Solana,       // 32-byte ed25519 public keys (Base58 encoded)
    Polkadot,     // 32-byte sr25519 public keys (SS58 encoded)
    Ton,          // 36-byte workchain:hash addresses
    InterLayer,   // 32-byte unified canonical addresses
}
```

#### 4.4.2 Address Type Discriminant

```rust
pub enum AddressType {
    Native,       // Substrate SS58 native format
    Evm,          // 20-byte Ethereum format
    SVM,          // 32-byte Solana format
    Move,         // 16-byte Move format
    PolkaVM,      // 32-byte PolkaVM/RISC-V format
    CrossVM,      // Unified cross-VM canonical address
}
```

#### 4.4.3 `UnifiedAddress` Structure

```rust
pub struct UnifiedAddress<HandleLen: Get<u32> = ConstU32<64>, AddressLen: Get<u32> = ConstU32<128>> {
    pub handle: BoundedVec<u8, HandleLen>,     // Human-readable handle (e.g., "bharath")
    pub domain: ChainDomain,                    // Target chain domain
    pub address: BoundedVec<u8, AddressLen>,    // Resolved native address bytes
    pub chain_id: u32,                          // EVM chain ID or chain-specific identifier
    pub is_active: bool,                        // Whether this mapping is currently active
}
```

#### 4.4.4 Handle Registration Lifecycle

Each handle is registered with ownership tracking, expiration, fee payment, verification status, and domain restrictions:

```rust
pub struct HandleRegistration<T: Config, HandleLen = ConstU32<64>, MaxDomains = ConstU32<10>> {
    pub handle: BoundedVec<u8, HandleLen>,               // The handle string
    pub owner: T::AccountId,                              // Substrate AccountId of the owner
    pub registered_at: BlockNumberFor<T>,                 // Block number of registration
    pub expires_at: Option<BlockNumberFor<T>>,            // Optional expiration block
    pub fee_paid: u128,                                   // Registration fee (in IL tokens)
    pub is_active: bool,                                  // Active status flag
    pub is_verified: bool,                                // Whether identity is verified
    pub allowed_domains: BoundedVec<ChainDomain, MaxDomains>, // Whitelisted chain domains
}
```

#### 4.4.5 Signature Scheme Support

Address verification supports both classical and post-quantum signature schemes:

```rust
pub enum SignatureScheme {
    Ecdsa,       // secp256k1 ECDSA (Ethereum-compatible)
    Ed25519,     // Edwards curve (Solana-compatible)
    Sr25519,     // Schnorrkel/Ristretto (Substrate-native)
    Dilithium,   // CRYSTALS-Dilithium lattice-based PQ signatures
    Falcon512,   // FALCON-512 NTRU lattice-based PQ signatures
    Hybrid,      // Classical + post-quantum dual verification
}
```

#### 4.4.6 On-Chain Storage Layout

The address registry maintains five storage maps:

| Storage Item | Type | Key | Value |
| :--- | :--- | :--- | :--- |
| `HandleRegistrations` | `StorageMap` | `BoundedVec<u8, 64>` (handle) | `HandleRegistration<T>` |
| `AddressMappings` | `StorageMap` | `(handle, ChainDomain)` | `AddressMapping<T>` |
| `UnifiedAddresses` | `StorageMap` | `BoundedVec<u8, 128>` (handle+domain) | `UnifiedAddress` |
| `DomainRegistrations` | `StorageMap` | `ChainDomain` | `u32` (count) |
| `AddressResolutionCache` | `StorageMap` | `BoundedVec<u8, 128>` (resolution key) | `AddressResolution<T>` |

#### 4.4.7 Registry Trait Interface

Other pallets interact with the address registry through the `UnifiedAddressRegistry` trait:

```rust
pub trait UnifiedAddressRegistry {
    type AccountId;
    fn register_unified_address(handle: Vec<u8>, vm_addresses: BTreeMap<AddressType, Vec<u8>>,
                                 metadata: Vec<u8>) -> DispatchResult;
    fn update_vm_address(handle: &Vec<u8>, ty: AddressType, address: Vec<u8>) -> DispatchResult;
    fn get_vm_address(handle: &Vec<u8>, ty: AddressType) -> Option<Vec<u8>>;
    fn get_all_vm_addresses(handle: &Vec<u8>) -> Vec<(AddressType, Vec<u8>)>;
    fn register_random_handle_for(account: Self::AccountId, domains: Vec<ChainDomain>)
        -> Result<Vec<u8>, DispatchError>;
}
```

## Chapter 5: LiteVerse DePIN Watcher Mesh & Liquidity Orchestration

### 5.1 Architecture: Native Unique Deposit Addresses vs. Traditional Bridges
Traditional bridges lock funds on Chain A to mint synthetic wrapped tokens (e.g. `wBTC`, `wETH`) on Chain B, introducing smart contract vulnerability points and fragmented token liquidity. InterLayer eliminates wrapped synthetic assets through **LiteVerse DePIN Watchers** and **MPC Threshold Signing**.

### 5.2 Liquidity Inflow & Outflow Flowchart

<svg viewBox="0 0 800 720" width="100%" height="auto" xmlns="http://www.w3.org/2000/svg" style="background:#ffffff; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;">
  <!-- Section 1: Liquidity Inflow -->
  <text x="400" y="35" font-size="20" font-weight="700" fill="#111111" text-anchor="middle">Liquidity Inflow (Deposit)</text>

  <!-- Step 1: User -->
  <rect x="20" y="60" width="120" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="80" y="88" font-size="14" font-weight="700" fill="#111111" text-anchor="middle">1. User</text>
  <text x="80" y="130" font-size="11" fill="#444444" text-anchor="middle">User wants to</text>
  <text x="80" y="145" font-size="11" fill="#444444" text-anchor="middle">deposit assets</text>

  <path d="M 140 115 L 170 115" stroke="#111111" stroke-width="2"/>

  <!-- Step 2: External Chains -->
  <rect x="170" y="60" width="130" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="235" y="88" font-size="14" font-weight="700" fill="#111111" text-anchor="middle">2. External Chains</text>
  <text x="235" y="130" font-size="11" fill="#444444" text-anchor="middle">BTC / ETH / SOL</text>
  <text x="235" y="145" font-size="11" fill="#444444" text-anchor="middle">and more</text>

  <path d="M 300 115 L 330 115" stroke="#111111" stroke-width="2"/>

  <!-- Step 3: Deposit Addr -->
  <rect x="330" y="60" width="140" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="400" y="82" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">3. Unique Deposit</text>
  <text x="400" y="98" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">Address</text>
  <text x="400" y="130" font-size="11" fill="#444444" text-anchor="middle">System provides unique</text>
  <text x="400" y="145" font-size="11" fill="#444444" text-anchor="middle">address (per user)</text>

  <path d="M 470 115 L 500 115" stroke="#111111" stroke-width="2"/>

  <!-- Step 4: Verification -->
  <rect x="500" y="60" width="130" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="565" y="88" font-size="14" font-weight="700" fill="#111111" text-anchor="middle">4. Verification</text>
  <text x="565" y="130" font-size="11" fill="#444444" text-anchor="middle">LiteVerse watchers</text>
  <text x="565" y="145" font-size="11" fill="#444444" text-anchor="middle">verify deposit</text>

  <path d="M 630 115 L 660 115" stroke="#111111" stroke-width="2"/>

  <!-- Step 5: Unified Balance -->
  <rect x="660" y="60" width="120" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="720" y="82" font-size="12" font-weight="700" fill="#111111" text-anchor="middle">5. Unified Balance</text>
  <text x="720" y="98" font-size="12" font-weight="700" fill="#111111" text-anchor="middle">on InterLayer</text>
  <text x="720" y="130" font-size="10" fill="#444444" text-anchor="middle">Credited to user's</text>
  <text x="720" y="145" font-size="10" fill="#444444" text-anchor="middle">unified balance</text>

  <!-- Usable Across Internal VMs box -->
  <path d="M 720 170 L 720 200 L 400 200 L 400 210" fill="none" stroke="#111111" stroke-width="2"/>

  <rect x="150" y="210" width="500" height="60" rx="10" ry="10" fill="#ffffff" stroke="#111111" stroke-width="1.5" stroke-dasharray="4,4"/>
  <text x="400" y="228" font-size="12" font-weight="700" fill="#111111" text-anchor="middle">Usable Across All Internal VMs</text>

  <rect x="175" y="235" width="70" height="25" rx="5" fill="#ffffff" stroke="#111111" stroke-width="1"/>
  <text x="210" y="252" font-size="11" font-weight="600" text-anchor="middle">EVM</text>

  <rect x="265" y="235" width="70" height="25" rx="5" fill="#ffffff" stroke="#111111" stroke-width="1"/>
  <text x="300" y="252" font-size="11" font-weight="600" text-anchor="middle">SVM</text>

  <rect x="355" y="235" width="85" height="25" rx="5" fill="#ffffff" stroke="#111111" stroke-width="1"/>
  <text x="397" y="252" font-size="11" font-weight="600" text-anchor="middle">Move VM</text>

  <rect x="450" y="235" width="90" height="25" rx="5" fill="#ffffff" stroke="#111111" stroke-width="1"/>
  <text x="495" y="252" font-size="11" font-weight="600" text-anchor="middle">CosmWasm</text>

  <rect x="550" y="235" width="80" height="25" rx="5" fill="#ffffff" stroke="#111111" stroke-width="1"/>
  <text x="590" y="252" font-size="11" font-weight="600" text-anchor="middle">PolkaVM</text>

  <!-- Divider Line -->
  <line x1="20" y1="300" x2="780" y2="300" stroke="#cccccc" stroke-width="1.5"/>

  <!-- Section 2: Liquidity Outflow -->
  <text x="400" y="340" font-size="20" font-weight="700" fill="#111111" text-anchor="middle">Liquidity Outflow (Withdrawal)</text>

  <!-- Step 1: User Request -->
  <rect x="40" y="370" width="150" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="115" y="400" font-size="14" font-weight="700" fill="#111111" text-anchor="middle">1. User</text>
  <text x="115" y="440" font-size="11" fill="#444444" text-anchor="middle">User requests</text>
  <text x="115" y="455" font-size="11" fill="#444444" text-anchor="middle">withdrawal</text>

  <path d="M 190 425 L 220 425" stroke="#111111" stroke-width="2"/>

  <!-- Step 2: Request Processed -->
  <rect x="220" y="370" width="160" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="300" y="400" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">2. Request Processed</text>
  <text x="300" y="440" font-size="11" fill="#444444" text-anchor="middle">InterLayer processes</text>
  <text x="300" y="455" font-size="11" fill="#444444" text-anchor="middle">the withdrawal</text>

  <path d="M 380 425 L 410 425" stroke="#111111" stroke-width="2"/>

  <!-- Step 3: Validators + MPC -->
  <rect x="410" y="370" width="170" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="495" y="400" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">3. Validators + MPC</text>
  <text x="495" y="440" font-size="11" fill="#444444" text-anchor="middle">Validators approve and</text>
  <text x="495" y="455" font-size="11" fill="#444444" text-anchor="middle">MPC signs transaction</text>

  <path d="M 580 425 L 610 425" stroke="#111111" stroke-width="2"/>

  <!-- Step 4: Assets Released -->
  <rect x="610" y="370" width="150" height="110" rx="12" ry="12" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="685" y="400" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">4. Assets Released</text>
  <text x="685" y="440" font-size="11" fill="#444444" text-anchor="middle">Assets sent to user's</text>
  <text x="685" y="455" font-size="11" fill="#444444" text-anchor="middle">external address</text>

  <!-- Comparison Box -->
  <rect x="40" y="520" width="720" height="90" rx="14" ry="14" fill="#ffffff" stroke="#111111" stroke-width="1.5" stroke-dasharray="4,4"/>

  <text x="220" y="550" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">Traditional Bridge:</text>
  <text x="220" y="575" font-size="12" fill="#555555" text-anchor="middle">Lock on Chain A  →  Mint Wrapped on Chain B</text>

  <line x1="400" y1="535" x2="400" y2="595" stroke="#e0e0e0" stroke-width="1.5"/>

  <text x="580" y="550" font-size="13" font-weight="700" fill="#111111" text-anchor="middle">InterLayer Approach:</text>
  <text x="580" y="575" font-size="12" fill="#555555" text-anchor="middle">Deposit to Unique Address  →  Verify  →  Unified Balance</text>
</svg>

### 5.3 Implementation: `liteverse-pallet` Architecture

The LiteVerse DePIN watcher mesh is implemented in the **LiteVerse Framework** pallet . This is the largest custom pallet in the InterLayer runtime and contains chain-specific verification modules, a task assignment system, a points-based reward economy, and a withdrawal pipeline.

#### 5.3.1 Supported Chain Identifiers

```rust
pub enum ChainId {
    Bitcoin,      // BTC mainnet  uses SPV header chain verification
    Ethereum,     // ETH mainnet  uses Merkle Patricia Trie proof verification
    Solana,       // SOL mainnet  uses slot-based epoch verification
    Polkadot,     // DOT relay chain  uses Grandpa/BABE header verification
    Ton,          // TON network  uses BoC (Bag of Cells) verification
}
```

#### 5.3.2 Chain-Specific Verification Modules

Each supported external domain is served by a chain-specific light-client verifier that implements the following public verification contract:

```rust
pub trait ExternalChainVerifier {
    type Header;
    type Proof;

    fn verify_header(header: &Self::Header) -> Result<(), VerificationError>;
    fn verify_finality(header: &Self::Header) -> Result<(), VerificationError>;
    fn verify_inclusion(header: &Self::Header, proof: &Self::Proof)
        -> Result<VerifiedDeposit, VerificationError>;
}
```

Bitcoin, Ethereum, Solana, Polkadot, and TON verifiers each deserialize headers, validate the chain-native commitment and finality rules, and then validate an inclusion proof against that commitment. A watcher submission is accepted only after all three checks succeed; a watcher assertion alone is never a deposit proof.

#### 5.3.3 `TransactionProof`  Deposit Verification Data

When a watcher observes a deposit on an external chain, it submits a `TransactionProof` to the on-chain pallet:

```rust
pub struct TransactionProof {
    pub chain_id: ChainId,                                    // Which external chain
    pub tx_hash: H256,                                        // Transaction hash
    pub block_hash: H256,                                     // Block containing the tx
    pub merkle_proof: BoundedVec<u8, ConstU32<16384>>,        // Merkle inclusion proof (up to 16 KB)
    pub confirmations: u32,                                   // Number of confirmations observed
    pub timestamp: u64,                                       // Observation timestamp
    pub tx_data: BoundedVec<u8, ConstU32<65536>>,             // Raw transaction data (up to 64 KB)
    pub block_header: BoundedVec<u8, ConstU32<65536>>,        // Serialized block header
    pub merkle_leaf_index: u32,                               // Position of tx in Merkle tree
}
```

#### 5.3.4 `ChainHeader`  External Chain Block Header

```rust
pub struct ChainHeader {
    pub chain_id: ChainId,                                    // Chain identifier
    pub block_hash: H256,                                     // Block hash
    pub previous_hash: H256,                                  // Parent block hash (chain continuity)
    pub merkle_root: H256,                                    // Transaction Merkle root
    pub timestamp: u64,                                       // Block timestamp
    pub difficulty: u32,                                      // PoW difficulty (Bitcoin) or slot (Solana)
    pub height: u64,                                          // Block height
    pub header_data: BoundedVec<u8, ConstU32<65536>>,         // Raw header bytes for verification
    pub signature: BoundedVec<u8, ConstU32<256>>,             // Header signature/seal
}
```

#### 5.3.5 Withdrawal Pipeline

The withdrawal flow progresses through six states:

```rust
pub enum WithdrawalStatus {
    Pending,       // User submitted withdrawal request
    Processing,    // Validators are reviewing
    Signed,        // MPC threshold signature produced
    Broadcasted,   // Transaction broadcasted to external chain
    Finalized,     // Confirmed on external chain
    Failed,        // Withdrawal failed (refunded)
}

pub struct WithdrawalRequest<AccountId, Balance> {
    pub id: u64,                                              // Unique withdrawal ID
    pub account: AccountId,                                   // Requesting account
    pub chain_id: ChainId,                                    // Target external chain
    pub amount: Balance,                                      // Amount to withdraw
    pub destination: BoundedVec<u8, ConstU32<128>>,           // External chain address
    pub status: WithdrawalStatus,                             // Current status
    pub signatures: BoundedVec<BoundedVec<u8, ConstU32<65>>, ConstU32<10>>, // Up to 10 MPC sigs
}
```

#### 5.3.6 Points-Based Watcher Reward System

LiteVerse watchers earn points for successful deposit verifications. The reward system is designed to incentivize fast, accurate reporting:

```rust
pub struct PointBalance<BlockNumber> {
    pub total_earned: u64,        // All-time points earned
    pub available: u64,           // Points available for redemption
    pub redeemed: u64,            // Points already converted to IL tokens
    pub total_submissions: u32,   // Total successful submissions
    pub last_activity: BlockNumber, // Block of last activity
}
```

**Reward Configuration Constants** (from pallet config trait):
- `MaxRewardParticipants`: Maximum watchers sharing reward for a single deposit
- `SubmissionWindow`: Time window (in blocks) for watchers to submit proofs
- `BaseRewardWeight`: Base points per successful submission
- `SpeedBonus`: Additional points per position (earlier submission = more points)
- `PointsPerToken`: Exchange rate (e.g., 1000 points = 1 IL token)
- `MinRedemptionPoints`: Minimum points required for token redemption

#### 5.3.7 Task Assignment System

The pallet implements a decentralized task assignment system for coordinating watcher activity:

| Storage Item | Type | Key | Value | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `ChainHeaders` | `StorageMap` | `ChainId` | `ChainHeader` | Latest synced header per chain |
| `VerifiedTransactions` | `StorageMap` | `H256` (tx hash) | `TransactionProof` | Verified deposit proofs |
| `LastSyncBlock` | `StorageMap` | `ChainId` | `u64` | Last synced block height |
| `ChainHeadersCache` | `StorageMap` | `(ChainId, u64)` | `ChainHeader` | Historical header cache |
| `LiteClientPoints` | `StorageMap` | `AccountId` | `PointBalance` | Watcher point balances |
| `RewardLaneStatsByLane` | `StorageMap` | `RewardLane` | `RewardLaneStats` | Aggregate reward metrics |
| `NextTaskId` | `StorageValue` |  | `u64` | Auto-incrementing task ID |
| `AvailableTasks` | `StorageMap` | `u64` (task_id) | `Task` | Pending verification tasks |
| `RegisteredClients` | `StorageMap` | `AccountId` | `ClientCapabilities` | Registered watcher nodes |
| `TaskAssignments` | `StorageDoubleMap` | `(task_id, AccountId)` | `TaskAssignment` | Per-watcher task progress |
| `TaskParticipantCount` | `StorageMap` | `u64` (task_id) | `u8` | Participants per task |
| `TransactionSubmitters` | `StorageMap` | `H256` (tx hash) | `BoundedVec<AccountId>` | Dedup: who submitted |
| `PendingWithdrawals` | `StorageMap` | `u64` (withdrawal_id) | `WithdrawalRequest` | Active withdrawal queue |

#### 5.3.8 Off-Chain Worker Integration

The LiteVerse pallet uses Substrate's off-chain worker mechanism (`CreateBare<Call<Self>>`) to periodically poll external chain RPC endpoints for new blocks and deposit transactions. The off-chain worker:

1. Queries external chain RPC endpoints for new block headers
2. Deserializes headers using chain-specific modules (`BitcoinHeader`, `EthereumBlockHeader`, etc.)
3. Validates header chain continuity (parent hash linkage)
4. Scans block transactions for deposits to MPC-controlled addresses
5. Constructs `TransactionProof` with Merkle inclusion proof
6. Submits unsigned extrinsic `submit_chain_header` or `verify_transaction` to the runtime

---

## Chapter 6: Pipelined 3-Chain HotStuff BFT Consensus & Safety Proofs

### 6.1 Pipelined Consensus Protocol
InterLayer utilizes a 3-chain HotStuff BFT consensus engine operating over views $v \in \mathbb{N}_1$.

<svg viewBox="0 0 800 240" width="100%" height="auto" xmlns="http://www.w3.org/2000/svg" style="background:#ffffff; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;">
  <rect x="40" y="30" width="150" height="80" rx="10" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="115" y="65" font-size="15" font-weight="700" text-anchor="middle">Prepare Phase</text>
  <text x="115" y="85" font-size="11" fill="#555555" text-anchor="middle">Broadcast Proposal</text>

  <path d="M 190 70 L 240 70" stroke="#111111" stroke-width="2"/>

  <rect x="240" y="30" width="160" height="80" rx="10" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="320" y="65" font-size="15" font-weight="700" text-anchor="middle">Precommit Phase</text>
  <text x="320" y="85" font-size="11" fill="#555555" text-anchor="middle">Form LockedQC</text>

  <path d="M 400 70 L 450 70" stroke="#111111" stroke-width="2"/>

  <rect x="450" y="30" width="150" height="80" rx="10" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="525" y="65" font-size="15" font-weight="700" text-anchor="middle">Commit Phase</text>
  <text x="525" y="85" font-size="11" fill="#555555" text-anchor="middle">Form CommitQC</text>

  <path d="M 600 70 L 650 70" stroke="#111111" stroke-width="2"/>

  <rect x="650" y="30" width="110" height="80" rx="10" fill="#ffffff" stroke="#111111" stroke-width="2"/>
  <text x="705" y="75" font-size="15" font-weight="700" text-anchor="middle">FINALITY</text>
</svg>

A Quorum Certificate $\text{QC}_v$ is defined as:

$$\text{QC}_v = \Big( h(B_v), \, v, \, \text{Type}, \, \sigma_{\text{agg}}, \, \text{Bitfield} \Big)$$

### 6.2 BLS12-381 Aggregate Cryptography
Let $G_1, G_2$ be pairing groups of prime order $r$ with pairing $e: G_1 \times G_2 \to G_T$.
- Secret key $x_i \in \mathbb{Z}_r^*$, Public key $Y_i = x_i \cdot g_2 \in G_2$.
- Partial signature on proposal hash $m$: $\sigma_i = x_i \cdot H_{G_1}(m) \in G_1$.
- The signed message is domain-separated and binds the protocol version, validator-set identifier, view, block height, block hash, and vote phase. Reusing a valid signature in another view, phase, or validator set is invalid.
- Aggregate signature over quorum $Q$, where $|Q| \ge q = \left\lceil \frac{2n}{3} \right\rceil$ and signer identities are unique:

$$\sigma_{\text{agg}} = \sum_{i \in Q} \sigma_i \in G_1$$

#### Verification Equation:
$$e\left( \sigma_{\text{agg}}, \, g_2 \right) = e\left( H_{G_1}(m), \, \sum_{i \in Q} Y_i \right)$$

Under this convention, a compressed $G_1$ signature is 48 bytes and a compressed $G_2$ public key is 96 bytes. The QC carries the signer bitmap or a canonical ordered signer list so the verifier aggregates precisely the public keys in $Q$.

### 6.3 Safety Theorem and Proof

> **Theorem 1 (BFT Safety)**: If $f < \frac{n}{3}$, no two conflicting blocks $B, B'$ can be finalized at the same block height.

#### Proof:
1. Finalization requires a *linked* 3-chain $B \leftarrow B' \leftarrow B''$: each child names the preceding block as parent and carries a valid QC for that parent. Consecutive view numbers without parent linkage do not constitute a 3-chain.
2. A validator locks the highest certified block. It votes for a proposal only if that proposal extends its locked block, or if the proposal's justify-QC has a strictly higher view than the lock and is itself valid.
3. Let $Q_1$ and $Q_2$ be two quorums. Since $q=\lceil 2n/3 \rceil$, $|Q_1 \cap Q_2| \ge 2q-n > f$; their intersection contains at least one honest validator.
4. An honest validator in this intersection cannot vote for two conflicting branches while respecting the lock rule. A higher-view QC that permits a new vote must extend the locked branch, and therefore cannot justify a conflicting finalization.
5. Hence two conflicting linked 3-chains cannot both form, so no two conflicting blocks at one height can be finalized. $\blacksquare$

### 6.4 Implementation: Consensus Engine Main Loop


The HotStuff consensus engine runs a fast-polling async loop with a 10ms poll interval. The `self.timeout` field serves as a view-change safety mechanism  if no activity occurs within the timeout duration, the engine advances to the next view (leader rotation):

```rust
pub async fn run(&mut self) {
    let poll_interval = Duration::from_millis(10);  // Sub-500ms block times

    loop {
        let loop_start = Instant::now();

        // Phase 1: Propose if we are the leader for this view (once per view)
        if self.is_leader() && !self.proposed_views.contains(&self.current_view) {
            match self.propose_block().await {
                Ok(_) => {
                    self.proposed_views.insert(self.current_view);
                    self.last_activity = Instant::now();
                }
                Err(e) => { log::error!("Proposal failed: {:?}", e); }
            }
        }

        // Phase 2: Drain all pending messages from the P2P network
        let mut messages = Vec::new();
        if let Some(network) = &self.network {
            loop {
                match network.receive_message() {
                    Ok(msg) => messages.push(msg),
                    Err(NetworkError::ReceiveTimeout) => break,
                    Err(e) => { log::error!("Network error: {:?}", e); break; }
                }
            }
        }

        // Phase 3: Process each message (Proposal, Vote, or QC)
        for msg in messages {
            if let Err(e) = self.process_message(msg).await {
                log::warn!("Error processing message: {:?}", e);
            }
        }

        // Phase 4: View-change timeout (safety mechanism for stalled leaders)
        if self.last_activity.elapsed() > self.timeout {
            self.handle_timeout().await;  // Advance current_view, reset timer
        }

        // Sleep remaining interval (fast polling)
        let elapsed = loop_start.elapsed();
        if elapsed < poll_interval {
            tokio::time::sleep(poll_interval - elapsed).await;
        }
    }
}
```

### 6.5 Message Processing Pipeline

The engine processes three types of consensus messages:

```rust
async fn process_message(&mut self, msg: ConsensusMessage<Block>) -> Result<()> {
    let msg_view = msg.view();
    // Only reset activity timer for relevant (current or future) views
    if msg_view >= self.current_view {
        self.last_activity = Instant::now();
    }
    match msg {
        ConsensusMessage::Proposal(proposal) => self.process_proposal(&proposal).await,
        ConsensusMessage::Vote(vote)         => self.process_vote(vote).await,
        ConsensusMessage::QC(qc)             => self.process_qc(qc).await,
    }
}
```

**Proposal Processing** :
1. Validate proposal structure via `Self::validate_proposal()`
2. Store block in `pending_blocks` map (keyed by view)
3. Sync local view if the proposal is ahead
4. Cast BLS vote using local validator key
5. Broadcast vote to P2P network
6. Process own vote locally (crucial for single-node consensus)

**Vote Processing** :
1. Delegate to `VoteCollector::process_vote()`
2. If vote reaches quorum threshold ($\ge \lfloor 2n/3 \rfloor + 1$), form QC
3. Broadcast QC to all validators via `network.send_qc(b"ALL", &qc)`
4. Auto-process the formed QC locally via `process_qc()`

### 6.6 `NetworkInterface` Trait

```rust
pub trait NetworkInterface<Block: BlockT>: Send + Sync {
    fn broadcast_proposal(&self, proposal: &Proposal<Block>)
        -> Result<(), NetworkError>;
    fn broadcast_vote(&self, view: u64, proposal_hash: Block::Hash,
                      validator_index: u32, signature: &[u8])
        -> Result<(), NetworkError>;
    fn send_qc(&self, target_validator: &[u8], qc: &QuorumCertificate)
        -> Result<(), NetworkError>;
    fn receive_message(&self) -> Result<ConsensusMessage<Block>, NetworkError>;
}
```

### 6.7 Liveness Guarantee

The consensus engine guarantees liveness through deterministic leader rotation. When a view times out (no valid proposal received within `self.timeout`), the engine calls `handle_timeout()`:

```rust
async fn handle_timeout(&mut self) {
    self.current_view += 1;                    // Advance to next view
    self.vote_collector.set_view(self.current_view); // Reset vote collector
    self.last_activity = Instant::now();       // Prevent immediate re-trigger
}
```

The leader for each view is determined by `self.is_leader()`, which computes `current_view % validators.len()` to find the leader index. This round-robin rotation ensures that if one leader is offline, the network advances to the next leader within one timeout period.

---

## Chapter 7: Deep-Dive Virtual Machine Execution Adapters

This chapter provides an exhaustive technical breakdown of each of the five VM execution adapters embedded within the InterLayer Substrate runtime. Each adapter is a standalone Rust crate that implements the `VmAdapter` and `AtomicVmAdapter` traits defined in `mel-core`, bridging native VM execution engines into the unified MEL orchestration layer.

### 7.1 EVM Adapter (`mel-evm`): Production-Grade Ethereum Execution

#### 7.1.1 Overview and Engine Selection

The EVM adapter (MEL-EVM Module) provides full Ethereum Virtual Machine compatibility within the MEL framework. It uses **`revm`** (v33.1 compatible), a production-grade Rust EVM implementation originally developed for the Reth Ethereum client. The choice of `revm` over alternative implementations (such as `evmone` or `geth`-derived engines) was motivated by three factors: (1) native Rust compilation without FFI boundaries, (2) `no_std` compatibility for Substrate WASM runtimes, and (3) comprehensive EIP coverage including EIP-1559 fee markets, EIP-2930 access lists, and EIP-4844 blob transactions.

The adapter struct `EvmAdapter<S: MelStorage, G: GasConfig>` is parameterized over two generic traits:
- `S: MelStorage`  the Substrate storage backend providing account state, code storage, and contract storage operations.
- `G: GasConfig`  configurable gas parameters including `MaxGasPerTx`, `BaseGasPrice`, `ContractCreationGas`, and `TransferGas`.

#### 7.1.2 Substrate Storage Database Bridge (`SubstrateEvmDb<S>`)

The critical innovation in `mel-evm` is the `SubstrateEvmDb<S>` struct, which implements `revm::DatabaseRef` to bridge Substrate's Merkle Patricia Trie storage into `revm`'s account model. This means that EVM contract execution reads and writes directly to and from the Substrate on-chain state, with no intermediate caching layer or separate database.

The `DatabaseRef` implementation provides four core methods:

1. **`basic_ref(address)`**: Loads account information (balance, nonce, code hash, bytecode) from `MelStorage::get_evm_account()`. Balances are stored as 256-bit big-endian byte arrays and converted to `revm::U256`. If no account exists at the given address, `None` is returned (the EVM treats this as a zero-balance externally owned account).

2. **`code_by_hash_ref(code_hash)`**: Retrieves deployed contract bytecode by its Keccak-256 hash from `MelStorage::get_evm_code()`. The bytecode is wrapped in `revm::Bytecode::new_raw()` for direct interpretation by the EVM execution engine.

3. **`storage_ref(address, index)`**: Reads a 256-bit storage slot value at a given contract address and storage key. The key is converted from `revm::U256` to `sp_core::U256` via big-endian byte serialization, then looked up through `MelStorage::get_evm_storage()`.

4. **`block_hash_ref(number)`**: Returns the block hash for a given block number. In the current testnet implementation, this returns `B256::ZERO` as a placeholder. In production, this will query `frame_system::BlockHash` for historical block hashes.

#### 7.1.3 Transaction Lifecycle and Execution

When an EVM transaction arrives (either as a raw RLP-encoded `TxEnvelope` or as a MEL-wrapped `MelTx` with `vm: VmType::EVM`), the adapter performs the following steps:

1. **Transaction Decoding**: Raw EVM transactions are decoded using `alloy_consensus::TxEnvelope::decode_2718()`, supporting Legacy, EIP-2930, and EIP-1559 transaction formats. The decoded transaction fields (nonce, gas limit, gas price/max fee, to address, value, input data, access list) are extracted.

2. **Signature Recovery**: The sender address is recovered from the ECDSA signature using `secp256k1` curve recovery. The recovered address is validated against the transaction's `from` field.

3. **`revm` Context Construction**: A `revm::Context` is built with `TxEnv` (caller, gas limit, gas price, transact_to, value, data, access list), block environment (number, timestamp, base fee, coinbase), and the `CacheDB<SubstrateEvmDb<S>>` database.

4. **Execution**: The `revm` execution engine runs the EVM bytecode, computing state transitions, gas consumption, and log emissions. The `CacheDB` layer stages all state mutations (account balance changes, storage writes, contract deployments) in memory.

5. **State Commitment**: If execution succeeds, staged mutations are committed to Substrate storage via `MelStorage::store_evm_account()`, `MelStorage::set_evm_storage()`, and `MelStorage::store_evm_code()`. If execution fails (out of gas, revert, invalid opcode), all staged changes are discarded.

#### 7.1.4 Precompiled Contracts

The adapter registers both standard Ethereum precompiles and custom cross-VM precompiles:

- **Standard precompiles** at addresses `0x01` through `0x09`: ecRecover, SHA-256, RIPEMD-160, identity, modexp, ecAdd, ecMul, ecPairing, and blake2f.
- **Cross-VM precompiles** at address range `0x0900+k`: These enable EVM contracts to invoke operations on other VMs (e.g., calling a Solana program from within a Solidity contract). Each cross-VM precompile accepts an ABI-encoded payload specifying the target VM type, contract address, method selector, and arguments.

The precompile system is implemented via a `BTreeMap<H160, Box<dyn PrecompileExecutor>>` stored in the `EvmAdapter` struct. Each precompile implements the `PrecompileExecutor` trait with an `execute(input, gas_limit, caller) -> PrecompileResult` method.

#### 7.1.5 EIP-1559 Fee Market Integration

The EVM adapter fully supports EIP-1559 dynamic base fee pricing. The `base_fee` field in `EvmAdapter` tracks the current base fee per gas, which is updated by the `fees-pallet` based on block fullness relative to target gas usage. Priority fees (tips) are paid directly to the block producer's coinbase address. The fee calculation follows the standard EIP-1559 formula:

$$\text{EffectiveGasPrice} = \min(\text{maxFeePerGas}, \text{baseFee} + \text{maxPriorityFeePerGas})$$

$$\text{Fee} = \text{gasUsed} \times \text{EffectiveGasPrice}$$

---

### 7.2 Solana SVM Adapter (`mel-svm`): eBPF Execution with SPL Token Emulation

#### 7.2.1 Overview and Architecture

The SVM adapter (MEL-SVM Module) implements Solana Virtual Machine compatibility using **`solana_rbpf`**, the official Rust implementation of the Solana eBPF (extended Berkeley Packet Filter) bytecode interpreter. This enables native execution of compiled Solana programs (`.so` shared object files compiled via `solana-bpf-tools`) within the InterLayer runtime.

The `SvmAdapter<S: MelStorage, G: GasConfig>` struct manages:
- **Compute budget enforcement**: Default limit of 1,400,000 compute units (CUs) per transaction, matching Solana mainnet limits.
- **Account model**: Solana's account-based state model where each account has an owner program, lamport balance, data vector (up to 10MB), and executable flag.
- **SPL Token emulation**: Full implementation of the SPL Token program, SPL Token-2022 (token extensions), and Associated Token Account (ATA) program through the `IlTokenSplProgram` module.

#### 7.2.2 Memory Layout and eBPF Execution

When executing a Solana program, the adapter constructs an `AlignedMemory` region with 8-byte alignment (matching the eBPF specification's `BPF_ALIGN`) containing:

| Region | Size | Purpose |
| :--- | :--- | :--- |
| Program Binary | Variable | Compiled eBPF `.so` bytecode |
| Stack | 64 KB | Program call stack |
| Heap | 32 KB | Dynamic memory allocation |
| Input Data | Variable | Serialized account parameters, instruction data |

The memory mapping is constructed using `solana_rbpf::memory_region::MemoryMapping` with separate `MemoryRegion` entries for each segment. The eBPF interpreter is instantiated via `solana_rbpf::program::BuiltinProgram` with a `FunctionRegistry` that registers syscall handlers.

#### 7.2.3 Syscall Implementation

The `syscalls` module provides custom Rust implementations of Solana's system calls, enabling programs to interact with the InterLayer runtime:

- **`sol_log`**: Logs UTF-8 messages to the Substrate runtime logger for debugging.
- **`sol_sha256`**, **`sol_keccak256`**: Cryptographic hash functions computed via `sp_io::hashing`.
- **`sol_create_program_address`**: Derives Solana Program Derived Addresses (PDAs) using SHA-256 hashing with bump seeds.
- **`sol_invoke_signed`**: Cross-Program Invocation (CPI) allowing programs to call other programs with signed authority.

#### 7.2.4 SPL Token-2022 Extensions (TLV Encoding)

The SVM adapter implements the full SPL Token-2022 extension system using Type-Length-Value (TLV) encoding. Extension data is appended after the base 165-byte SPL token account layout:

- **Offset 165**: Account type byte (`1` = Mint, `2` = Account)
- **Offset 166+**: TLV entries, each consisting of: 2-byte extension type (little-endian), 2-byte data length (little-endian), followed by the extension data payload.

Supported extensions include:
- `TransferFeeConfig` (type 1): Configurable transfer fees with authority and basis point settings.
- `TransferFeeAmount` (type 2): Per-account accumulated transfer fee amounts.
- `InterestBearingConfig` (type 10): Interest rate configuration for yield-bearing tokens.
- `PermanentDelegate` (type 12): Permanent delegate authority for token accounts.
- `TransferHook` (type 14): External program hooks invoked on token transfers.
- `MetadataPointer` (type 18): Pointer to on-chain token metadata.
- `TokenMetadata` (type 19): Embedded token metadata (name, symbol, URI, additional fields).

The adapter provides helper methods `read_tlv_entries()`, `write_tlv_entries()`, `upsert_tlv_entry()`, and `get_tlv_entry()` for safe TLV manipulation with bounds checking.

---

### 7.3 PolkaVM Adapter (`mel-polkavm`): RISC-V Native Execution

#### 7.3.1 Architecture

The PolkaVM adapter (MEL-PolkaVM Module) executes compiled RISC-V binaries using the official **`polkavm`** engine, bringing Polkadot's next-generation smart contract execution to InterLayer. PolkaVM programs are compiled from Rust (or any language targeting RISC-V) using `polkavm-linker` into position-independent `.polkavm` blobs.

The adapter struct `PolkaVMAdapter<S: MelStorage, G: GasConfig>` manages contract deployments, instance lifecycles, and host function dispatch.

#### 7.3.2 Host Function Linking

The adapter registers Substrate host functions into the PolkaVM instance through a host linker. These host functions allow RISC-V programs to interact with Substrate storage and runtime services:

- **`dispatch_psp22(selector, args)`**: Dispatches PSP-22 (fungible token standard) operations  `transfer`, `approve`, `total_supply`, `balance_of`, `allowance`.
- **`dispatch_psp34(selector, args)`**: Dispatches PSP-34 (non-fungible token standard) operations  `owner_of`, `transfer`, `approve`, `collection_id`.
- **`storage_read(key) -> value`**: Reads a value from the contract's dedicated storage partition in the Substrate trie via `MelStorage::get_polkavm_storage()`.
- **`storage_write(key, value)`**: Writes a value to contract storage via `MelStorage::set_polkavm_storage()`.
- **`caller() -> address`**: Returns the calling account's 32-byte address.
- **`value_transferred() -> u128`**: Returns the native `IL` tokens transferred with the call.
- **`emit_event(topics, data)`**: Emits a contract event recorded in the block's event log.

#### 7.3.3 Gas Metering

PolkaVM uses instruction-level gas metering where each RISC-V instruction consumes a configurable number of gas units. The gas calibration multiplier $\gamma_{\text{PolkaVM}} = 0.01$ means that 1 RISC-V cycle costs 0.01 standard gas units, reflecting the high computational efficiency of the RISC-V instruction set compared to EVM's stack-based architecture.

---

### 7.4 Move VM Adapter (`mel-move`): Linear Resource Execution

#### 7.4.1 Architecture and Dual-Mode Operation

The Move adapter (MEL-Move Module) executes Diem Move bytecode using **`move-vm-runtime`**, supporting Move's unique linear resource type system. The adapter operates in dual mode:

- **`std` mode** (native node binary): Full Move VM execution with bytecode verification, module publishing, script execution, and resource manipulation.
- **`no_std` mode** (WASM runtime): Lightweight verification shim that validates Move transaction signatures and basic format checks, deferring actual execution to the native host via host function calls.

#### 7.4.2 Resource Storage Model

Move's storage model is fundamentally different from other VMs. Instead of key-value slot mappings (like EVM), Move organizes state as typed resources owned by accounts:

- **Module Storage**: `MoveModules<T>`  A `StorageDoubleMap` indexed by `(address_bytes, module_name_bytes) -> bytecode_bytes`. When a Move module is published, its bytecode is stored under the publisher's address and module name.
- **Resource Storage**: `MoveResources<T>`  A `StorageDoubleMap` indexed by `(address_bytes, struct_tag_bytes) -> serialized_resource_data`. Resources are identified by their struct tag (module address + module name + struct name + type parameters), serialized using BCS (Binary Canonical Serialization).

The Move VM's `DataStore` trait is implemented over `MelStorage`, translating `get_resource(address, struct_tag)` and `publish_module(address, module_name, bytecode)` calls into Substrate storage operations.

#### 7.4.3 Bytecode Verification

Before any Move module can be executed, its bytecode undergoes verification through Move's built-in bytecode verifier, which checks:
- **Type safety**: All operations are well-typed according to Move's type system.
- **Resource safety**: Linear resources cannot be copied or implicitly dropped  they must be explicitly moved or destroyed.
- **Reference safety**: Mutable and immutable references follow strict borrowing rules.
- **Linking**: All module dependencies exist and have compatible interfaces.

---

### 7.5 CosmWasm Adapter (`mel-cosmwasm`): WebAssembly Actor Model

#### 7.5.1 Architecture

The CosmWasm adapter (MEL-CosmWasm Module) executes WebAssembly smart contracts using **`wasmi`**, a stack-based Wasm interpreter optimized for deterministic execution in blockchain contexts. Unlike `wasmtime` (which uses JIT compilation), `wasmi` provides fully deterministic instruction-by-instruction interpretation, ensuring identical execution across all validator nodes.

#### 7.5.2 JSON Envelope Processing

CosmWasm contracts communicate through JSON message envelopes. The adapter's JSON envelope parser translates between Substrate extrinsic payloads and CosmWasm's three standard entry points:

1. **`instantiate(msg)`**: Called once when a contract is first deployed. The `msg` JSON payload contains initialization parameters. The adapter deserializes the JSON, creates a new contract instance in storage, and invokes the Wasm `instantiate` export.

2. **`execute(msg)`**: Called for state-mutating operations. The `msg` JSON payload contains the action to perform and its parameters. The adapter routes the deserialized message to the Wasm `execute` export.

3. **`query(msg)`**: Called for read-only state queries. The `msg` JSON payload specifies what data to retrieve. The adapter invokes the Wasm `query` export and returns the JSON response without committing any state changes.

#### 7.5.3 Address Format and Bech32 Decoding

CosmWasm contracts use Bech32-encoded addresses with the `"il"` human-readable prefix. The adapter's Bech32 decoder converts between canonical 32-byte unified addresses and Bech32 string representations:

$$\text{Bech32Encode}(\text{prefix}=\text{"il"}, a_{\text{unified}}) \longrightarrow \text{"il1q2w3e4r...x8z9"}$$

#### 7.5.4 Host Import Implementations

The Wasm runtime environment provides host imports that CosmWasm contracts can call:
- **`db_read(key) -> value`**: Read from contract-scoped storage.
- **`db_write(key, value)`**: Write to contract-scoped storage.
- **`addr_validate(addr) -> bool`**: Validate a Bech32 address string.
- **`secp256k1_verify(hash, signature, pubkey) -> bool`**: ECDSA signature verification.
- **`ed25519_verify(message, signature, pubkey) -> bool`**: EdDSA signature verification.

---

## Chapter 8: Off-Chain Threshold MPC Signer Infrastructure (TSS)

![Figure 3: Threshold MPC Signing Process  Subset of t participants generate partial signatures using Lagrange interpolation, aggregated into a single threshold signature verified against the group public key.](images/mpc_threshold_signing.png)

This chapter details the off-chain Multi-Party Computation (MPC) threshold signing infrastructure implemented in the `mpc-executor` crate. The MPC signer network enables InterLayer to authorize cross-chain transactions (withdrawals, bridge operations) without any single entity holding a complete private key.

### 8.1 Cryptographic Foundation: secp256k1 Schnorr Signatures

The MPC signer uses **secp256k1 Schnorr signatures** implemented via the `k256` Rust crate. The choice of Schnorr over ECDSA for threshold signing was motivated by Schnorr's native support for linear signature aggregation  partial signatures can be summed directly without complex multi-round protocols.

Key cryptographic parameters:
- **Curve**: secp256k1 ($y^2 = x^3 + 7$ over $\mathbb{F}_p$, $p = 2^{256} - 2^{32} - 977$)
- **Generator**: $G$ (the standard secp256k1 base point)
- **Group order**: $q$ (the order of the secp256k1 curve group)
- **Hash function**: SHA-256 (used for challenge computation)

### 8.2 $(t, n)$ Threshold Key Generation via Polynomial Shamir Secret Sharing

The protocol uses a dealerless Feldman-style Distributed Key Generation (DKG) ceremony. No participant creates, reconstructs, or exports a master secret.

For every participant $j \in \{1,\dots,n\}$, sample a degree-$(t-1)$ polynomial

$$f_j(X)=a_{j,0}+a_{j,1}X+\dots+a_{j,t-1}X^{t-1} \pmod q$$

and broadcast commitments $C_{j,\ell}=a_{j,\ell}G$. Participant $j$ sends $f_j(i)$ to participant $i$ only over an authenticated confidential channel. Recipient $i$ verifies each received share by checking

$$f_j(i)G \stackrel{?}{=} \sum_{\ell=0}^{t-1} i^\ell C_{j,\ell}.$$

After complaint resolution, each participant holds $x_i=\sum_j f_j(i)$, its public verification share is $Y_i=x_iG$, and the group public key is $Y=\sum_j C_{j,0}$. The transcript commits to the participant set, threshold, epoch, commitments, complaints, and resolutions. The DKG output is rejected unless exactly one verified transcript is agreed for the epoch.

### 8.3 Threshold Signing Protocol (Schnorr)

Signing follows a two-round FROST-compatible threshold Schnorr flow. For a request digest $m$ and a canonical signing subset $T$ ($|T|\ge t$):

1. Each signer samples two secret nonces $(d_i,e_i)$ exactly once and publishes commitments $(D_i=d_iG,E_i=e_iG)$. Nonce material is erased after use, whether signing succeeds or fails.
2. The coordinator derives an ordered commitment list $\mathsf{com}$, a binding factor $\rho_i=H_{\rm bind}(i,\mathsf{com},m)$, and the group commitment $R=\sum_{i\in T}(D_i+\rho_iE_i)$. It rejects the identity point and any duplicate signer or commitment.
3. With the Lagrange coefficient $\lambda_i$, each signer computes $z_i=d_i+\rho_i e_i+\lambda_i x_i c\pmod q$, where $c=H_{\rm chal}(R,Y,m)$. The coordinator verifies every share before aggregation.
4. The aggregate is $z=\sum_{i\in T}z_i\pmod q$, yielding the signature $(R,z)$. The public verification equation is $zG\stackrel{?}{=}R+cY$.

The coordinator is not trusted: it cannot substitute a signer set, commitment list, request digest, epoch, destination, amount, or derivation path because each is included in $m$ and in the binding transcript. A one-round scheme that uses one signer's $R$ while summing all signers' scalars is not a valid threshold Schnorr signature and is expressly outside this specification.

### 8.4 Lagrange Interpolation Coefficients

The Lagrange coefficient $\lambda_i$ for participant $i$ within signing subset $T$ is computed as:

$$\lambda_i = \prod_{\substack{j \in T \\ j \neq i}} \frac{-j}{i - j} \pmod q$$

This coefficient reconstructs the group secret *in the exponent* without reconstructing it in any process: $\sum_{i \in T} \lambda_i x_i = x \pmod q$ and $Y=xG$. It is evaluated only over a canonical, duplicate-free signer set $T$.

### 8.5 Signature Verification

The verifier checks the fully encoded $R$ point, scalar range, even-$Y$ convention where BIP-340 encoding is used, the epoch public key, and standard Schnorr verification:

$$s \cdot G \stackrel{?}{=} R + e \cdot Y$$

where $e=H_{\rm chal}(R \,||\, Y \,||\, m)$, $R$ is the aggregate nonce point, $s$ is the aggregate scalar, and $Y$ is the group public key. The challenge and binding hashes use distinct tagged domain separators; raw SHA-256 concatenation without framing is not sufficient.

### 8.6 BIP-32/44 Hierarchical Deterministic Key Derivation

For deposit-address allocation, the protocol derives child *public keys* from an epoch key without reconstructing a private key. For a non-hardened index $i$, let $(I_L,I_R)=\text{HMAC-SHA512}(c_{\rm par},\operatorname{serP}(Y_{\rm par})\,||\,\operatorname{ser32}(i))$. The child public key is

$$Y_i = Y_{\rm par} + \operatorname{parse}_{256}(I_L)G,$$

and the chain code is $c_i=I_R$. Each signer derives the corresponding additive share locally, $x_{j,i}=x_j+\operatorname{parse}_{256}(I_L)\pmod q$, so no process learns the group scalar. Invalid tweaks and the point at infinity are rejected. Hardened derivation is permitted only through an explicit distributed derivation protocol; it MUST NOT be implemented by collecting key shares at a coordinator.

The allocated path binds the environment, external chain, account, and user index. The testnet profile reserves the BIP-44 coin-type path `m/44'/9999'/chain'/account'/0/i`; the exact `chain` and `account` components are recorded with the deposit-address allocation and signed into the withdrawal request.

---

## Chapter 9: Cryptographic Foundations & Quantum Signature Engine

### 9.1 Classical Cryptographic Primitive Suite

InterLayer employs a comprehensive suite of classical cryptographic primitives, each selected for its specific use case within the protocol:

#### 9.1.1 secp256k1 (ECDSA & Schnorr)
- **Usage**: EVM transaction signatures, Bitcoin-compatible TSS signing, Substrate ECDSA key pairs.
- **Implementation**: `k256` crate (pure Rust, constant-time operations).
- **Key size**: 256-bit private keys, 33-byte compressed public keys.
- **Signature format**: 64-byte `(r, s)` for ECDSA; 64-byte `(R_x, s)` for Schnorr.

#### 9.1.2 ed25519 (EdDSA)
- **Usage**: Solana SVM transaction signatures, Tendermint/CosmWasm validator signatures.
- **Implementation**: `ed25519-dalek` crate.
- **Key size**: 32-byte private seed, 32-byte public key.
- **Signature format**: 64-byte `(R, s)` encoding.

#### 9.1.3 sr25519 (Schnorrkel/Ristretto)
- **Usage**: Substrate native account keys, validator session keys.
- **Implementation**: `schnorrkel` crate over Ristretto255.
- **Features**: VRF (Verifiable Random Function) support for leader selection.

#### 9.1.4 BLS12-381 (Boneh-Lynn-Shacham)
- **Usage**: HotStuff consensus aggregate quorum certificates.
- **Implementation**: `w3f-bls` crate (W3F's BLS implementation with `TinyBLS381` mode).
- **Key size**: 48-byte public keys in $G_2$, 96-byte signatures in $G_1$.
- **Aggregation**: Linear signature aggregation  $n$ individual signatures compress to a single 96-byte aggregate, verified via a single pairing check.

### 9.2 Hash Functions

| Algorithm | Usage | Output Size |
| :--- | :--- | :--- |
| **Blake2b-256** | Substrate state root hashing, block header hashing | 32 bytes |
| **Keccak-256** | EVM address derivation, contract storage keys, Solidity ABI encoding | 32 bytes |
| **SHA-256** | Schnorr challenge computation, MPC nonce commitments, Solana PDA derivation | 32 bytes |
| **SHA3-256** | Move VM address derivation | 32 bytes |
| **RIPEMD-160** | Bitcoin address hashing (combined with SHA-256) | 20 bytes |

### 9.3 Post-Quantum Signature Engine

To prepare for the eventual threat of quantum computers breaking classical elliptic curve cryptography, InterLayer includes two post-quantum signature pallets:

#### 9.3.1 CRYSTALS-Dilithium (Lattice-Based)
- **Security Level**: Level 3 (equivalent to AES-192) and Level 5 (equivalent to AES-256).
- **Basis**: Module-LWE (Module Learning with Errors) problem over polynomial rings.
- **Public key size**: 1,952 bytes (Level 3), 2,592 bytes (Level 5).
- **Signature size**: 3,293 bytes (Level 3), 4,595 bytes (Level 5).
- **Verification**: $\text{Verify}_{\text{Dilithium}}(\text{PK}_{\text{pq}}, m_{\text{tx}}, \Sigma_{\text{pq}}) \longrightarrow \{\text{True}, \text{False}\}$

#### 9.3.2 SPHINCS+ (Hash-Based)
- **Security Level**: Level 3 and Level 5 (stateless hash-based signatures).
- **Basis**: Hash function security (SHA-256 or SHAKE-256 instantiation).
- **Public key size**: 48 bytes (f-variant) or 64 bytes (s-variant).
- **Signature size**: 16,224 bytes (128f) or 35,664 bytes (256f).
- **Use case**: Emergency fallback authority keys that remain secure even if lattice-based assumptions are broken.

---

## Chapter 10: Real-Yield Economic Model & Fee Distribution Routing

### 10.1 Economic Design Philosophy: Non-Inflationary Fee-Driven Yield

InterLayer's economic model fundamentally differs from most Proof-of-Stake networks. Traditional PoS chains (Ethereum, Cosmos, Polkadot) issue new tokens as staking rewards, creating perpetual inflation that dilutes non-staking token holders. InterLayer takes the opposite approach: **all validator, DA provider, and treasury rewards are generated strictly from transaction execution fees**, with zero token inflation outside explicit governance-approved supply changes.

This "real-yield" model means that staking rewards directly correlate with network usage and economic activity, rather than being an artificial subsidy. As transaction volume grows, fee revenue grows, and validator yields increase proportionally  creating a sustainable economic flywheel.

### 10.2 Fee Composition

Every transaction on InterLayer incurs fees composed of three components:

$$F_{\text{tx}} = F_{\text{base}} + F_{\text{priority}} + F_{\text{atomic\_premium}}$$

- **$F_{\text{base}}$**: The minimum fee determined by the current base gas price and the transaction's gas consumption. Follows EIP-1559 dynamics where the base fee adjusts up/down based on block fullness relative to a 75% target.
- **$F_{\text{priority}}$**: An optional tip paid by the user to incentivize faster inclusion. This tip goes directly to the block producer.
- **$F_{\text{atomic\_premium}}$**: An additional surcharge applied to cross-VM atomic bundles, reflecting the increased computational overhead of snapshot creation, multi-VM coordination, and potential rollback costs.

### 10.3 Block Fee Distribution Formulas

Total fees $F_{\text{total}}$ collected across all transactions $T_n$ in block $B_n$ are separated into a producer tip and a distributable fee pool:

$$F_{\text{total}} = \sum_{t \in T_n} \Big( F_{\text{base}}(t) + F_{\text{priority}}(t) + F_{\text{atomic\_premium}}(t) \Big)$$

$$F_{\text{tip}} = \sum_{t \in T_n}F_{\text{priority}}(t), \qquad F_{\text{dist}} = F_{\text{total}}-F_{\text{tip}}$$

The block producer receives $F_{\text{tip}}$. The fee-distribution pallet routes $F_{\text{dist}}$ using the fixed testnet policy vector $(0.30,0.30,0.25,0.15)$:

$$\begin{aligned}
\Delta R_{\text{validators}} &= 0.30 \cdot F_{\text{dist}} \quad \text{(30\% to active validators)} \\
\Delta R_{\text{da\_providers}} &= 0.30 \cdot F_{\text{dist}} \quad \text{(30\% to DA providers / LiteVerse watchers)} \\
\Delta R_{\text{treasury}} &= 0.25 \cdot F_{\text{dist}} \quad \text{(25\% to community treasury)} \\
\Delta B_{\text{burn}} &= 0.15 \cdot F_{\text{dist}} \quad \text{(15\% permanently removed from supply)}
\end{aligned}$$

Writing $\dot F_{\text{dist}}$ for the time-normalized distributable fee flow, the real-yield accounting equations are

$$\dot R_{\text{validators}}=0.30\dot F_{\text{dist}},\quad \dot R_{\text{DA}}=0.30\dot F_{\text{dist}},\quad \dot R_{\text{treasury}}=0.25\dot F_{\text{dist}},\quad \frac{dS}{dt}=-0.15\dot F_{\text{dist}}+\dot M_{\text{gov}}.$$

Here $S$ is total supply and $\dot M_{\text{gov}}$ is zero except for an explicitly authorized governance supply change. Integer rounding is deterministic: each block computes three floor allocations, assigns the residual to the burn balance, and emits the four amounts in the fee-distribution event.

### 10.4 Validator Reward Distribution

Within the validator pool, rewards are distributed proportionally to stake weight:

$$R_{\text{val}_i} = \Delta R_{\text{validators}} \times \frac{S_i}{\sum_{j \in \mathcal{V}} S_j}$$

where $S_i$ is the total stake (self-bond + delegated) of validator $i$, and $\mathcal{V}$ is the active validator set.

Each validator's reward is further split between the operator (validator node runner) and delegators based on the validator's commission rate $c_i$:

$$R_{\text{operator}_i} = R_{\text{val}_i} \times c_i$$
$$R_{\text{delegators}_i} = R_{\text{val}_i} \times (1 - c_i)$$

Delegator rewards within a validator's pool are distributed proportionally to each delegator's share of the total delegated stake.

### 10.5 Slashing Penalties

Validators that exhibit Byzantine behavior face stake slashing:
- **Double-signing** (signing two conflicting blocks at the same view): 10% of total stake slashed.
- **Prolonged downtime** (missing $> 50\%$ of blocks in an epoch): 1% of total stake slashed.
- **Invalid proposal** (proposing a block with invalid state transitions): 5% of total stake slashed.

Slashed funds are sent to the community treasury rather than burned, ensuring that total supply is unaffected by slashing events.

### 10.6 Dynamic Gas Pricing Engine

The gas calibration engine (`GasCalibrationConfig`) implements a sophisticated multi-dimensional pricing model:

- **Base gas costs** per VM: EVM = 21,000; PolkaVM = 25,000; SVM = 30,000; Move = 35,000; CosmWasm = 32,000.
- **Operation multipliers**: Transfer = 1x; ContractCall = 2x; ContractCreate = 3x; StorageRead = 1x; StorageWrite = 2x; CrossVmCall = 5x; AtomicOperation = 10x.
- **VM instruction weights**: Fine-grained per-instruction-class weights (arithmetic, comparison, memory, control flow, cryptographic, storage, network, system, cross-VM) that are independently configurable per VM.
- **Cross-VM overhead factors**: A full 5×5 matrix of overhead percentages for every VM-to-VM pair (e.g., EVM→SVM = 150%, EVM→Move = 180%, PolkaVM→EVM = 110%).
- **Dynamic adjustment**: Network performance metrics (congestion level, average TPS, per-VM error rates, storage latency, bandwidth utilization) feed into a `DynamicGasCalibrationEngine` that recalculates calibration factors every 50 blocks.

---

## Chapter 11: Comprehensive Substrate Runtime Pallet Architecture (All 38 Pallets Exhaustively Detailed with Call Indices, Storage Hashers & Parameter Types)

This chapter is the public testnet dispatch profile for all 38 custom FRAME runtime pallets composing the InterLayer Gravity Testnet runtime. Every pallet specification details its functional responsibility, storage layout with explicit hashers (`Blake2_128Concat`, `Twox64Concat`), call-indexed extrinsics (`#[pallet::call_index(N)]`), parameter types, required dispatch origins (`Signed`, `Root`), events, and error variants. A `StorageValue` has no key hasher; every keyed `StorageMap` and `StorageDoubleMap` declares one. Where `ValidateUnsigned` is named, it denotes an unsigned submission governed by explicit transaction validation rather than a dispatch origin.

Call indices are part of the testnet compatibility surface. A runtime upgrade may add a new call only at an unused index and must publish the corresponding metadata change; it MUST NOT repurpose an existing index.

---

### 11.1 Pallet `atomic-execution`

**Functional Responsibility**: Coordinates 5-phase atomic multi-VM transaction execution across EVM, SVM, PolkaVM, Move, and CosmWasm adapters. Manages pre-execution checkpointing (`restore_snapshot()`), isolated `CacheDB` diff staging, dependency validation, and atomic commit/rollback enforcement.

**Storage Items**:
- `ActiveBundles<T>: StorageMap<Blake2_128Concat, H256, AtomicBundle>`  Active atomic execution contexts keyed by Blake2b bundle ID.
- `BundlePhase<T>: StorageMap<Blake2_128Concat, H256, AtomicExecutionPhase>`  Current lifecycle phase (`Initialized`, `SourceExecution`, `TargetExecution`, `Validation`, `Committed`, `Rollback`, `Failed`).
- `ExecutionSnapshots<T>: StorageMap<Blake2_128Concat, H256, (Vec<u8>, Vec<u8>)>`  Staged pre-execution state roots for rollback.

**Extrinsics**:
- `#[pallet::call_index(0)] execute_atomic_bundle(origin: OriginFor<T>, bundle: AtomicBundle, deadline: u64)`  
  *Origin*: `Signed(who)` | *Weight*: `O(|bundle.transactions| * G_vm)`  
  Primary entry point. Validates bundle context, creates state snapshots $\mathcal{S}_0$, executes operations across target VM adapters, and commits staged diffs upon success or triggers rollback on error.
- `#[pallet::call_index(1)] force_rollback_bundle(origin: OriginFor<T>, bundle_id: H256)`  
  *Origin*: `Root` | *Weight*: `O(1)`  
  Emergency rollback override for expired or stuck atomic bundles.

**Events**:
- `AtomicExecutionInitiated { bundle_id: H256, source_vm: VmType, target_vm: VmType }`
- `AtomicExecutionCommitted { bundle_id: H256, gas_used: u64 }`
- `AtomicExecutionRolledBack { bundle_id: H256, reason: Vec<u8> }`

**Errors**:
- `BundleNotFound`, `DeadlineExpired`, `InvalidContext`, `ExecutionFailed`, `RollbackFailed`, `UnauthorizedCaller`.

---

### 11.2 Pallet `bridge-pallet`

**Functional Responsibility**: Manages legacy asset bridge compatibility shims, deposit tracking, and external chain lock/unlock event validation when interfacing with non-MPC legacy networks.

**Storage Items**:
- `BridgeDeposits<T>: StorageMap<Blake2_128Concat, H256, BridgeDepositRecord>`  Records of incoming cross-chain deposits.
- `SupportedAssets<T>: StorageMap<Twox64Concat, H256, AssetMetadata>`  Whitelisted bridge asset configurations.

**Extrinsics**:
- `#[pallet::call_index(0)] submit_bridge_deposit(origin: OriginFor<T>, chain_id: u32, tx_hash: H256, amount: u128, recipient: Vec<u8>)`  
  *Origin*: `Signed(who)` | *Weight*: `O(1)`
- `#[pallet::call_index(1)] update_asset_config(origin: OriginFor<T>, asset_id: H256, config: AssetMetadata)`  
  *Origin*: `Root`

**Events**: `DepositProcessed`, `AssetConfigUpdated` | **Errors**: `AssetNotSupported`, `DuplicateDeposit`.

---

### 11.3 Pallet `data-availability-hooks`

**Functional Responsibility**: Verifies and commits Data Availability (DA) roots submitted by external rollup sequencers and LiteVerse watcher nodes. Serves as InterLayer's high-throughput DA posting layer.

**Storage Items**:
- `RollupStateRoots<T>: StorageDoubleMap<Blake2_128Concat, u32, Blake2_128Concat, u64, H256>`  Maps `(rollup_id, l2_block_number)` to L2 state root hash.
- `DaCommitments<T>: StorageMap<Blake2_128Concat, H256, DaBlockCommitment>`  Posted Merkle roots of transaction data blobs.

**Extrinsics**:
- `#[pallet::call_index(0)] post_da_commitment(origin: OriginFor<T>, rollup_id: u32, block_number: u64, data_root: H256, size_bytes: u32)`  
  *Origin*: `Signed(sequencer)`
- `#[pallet::call_index(1)] verify_da_inclusion(origin: OriginFor<T>, data_root: H256, leaf: Vec<u8>, proof: Vec<H256>)`  
  *Origin*: `Signed(who)`

**Events**: `DaCommitmentPosted`, `InclusionVerified` | **Errors**: `InvalidProof`, `CommitmentExists`.

---

### 11.4 Pallet `delegated-staking`

**Functional Responsibility**: Implements delegated Proof-of-Stake (dPoS) for HotStuff validators. Handles candidate registration, nominator bond pooling, 28-day unbonding periods, and reward compounding.

**Storage Items**:
- `Validators<T>: StorageMap<Blake2_128Concat, T::AccountId, ValidatorConfig>`  Active validator candidate metadata and self-bond.
- `Delegations<T>: StorageDoubleMap<Blake2_128Concat, T::AccountId, Blake2_128Concat, T::AccountId, u128>`  Maps `(nominator, validator)` to staked balance.
- `TotalStaked<T>: StorageValue<u128>`  Global total staked `IL` token supply.

**Extrinsics**:
- `#[pallet::call_index(0)] register_validator(origin: OriginFor<T>, commission_bps: u32, bls_pubkey: Vec<u8>)`  
  *Origin*: `Signed(candidate)`
- `#[pallet::call_index(1)] delegate(origin: OriginFor<T>, validator: T::AccountId, amount: u128)`  
  *Origin*: `Signed(nominator)`
- `#[pallet::call_index(2)] undelegate(origin: OriginFor<T>, validator: T::AccountId, amount: u128)`  
  *Origin*: `Signed(nominator)`

**Events**: `ValidatorRegistered`, `Delegated`, `Undelegated`, `Unbonded` | **Errors**: `InsufficientBond`, `AlreadyValidator`, `UnbondingPeriodActive`.

---

### 11.5 Pallet `dex-pallet`

**Functional Responsibility**: Implements a native Automated Market Maker (AMM) constant-product ($x \cdot y = k$) liquidity pool engine allowing instant native token swaps across EVM, SVM, and Substrate token standards.

**Storage Items**:
- `Pools<T>: StorageMap<Blake2_128Concat, (H256, H256), LiquidityPool>`  Asset pair reserves, fee rate, LP token supply.
- `UserLpShares<T>: StorageDoubleMap<Blake2_128Concat, (H256, H256), Blake2_128Concat, T::AccountId, u128>`  User LP share balances.

**Extrinsics**:
- `#[pallet::call_index(0)] create_pool(origin: OriginFor<T>, asset_a: H256, asset_b: H256, fee_bps: u32)`  
  *Origin*: `Signed(who)`
- `#[pallet::call_index(1)] swap_exact_tokens(origin: OriginFor<T>, asset_in: H256, asset_out: H256, amount_in: u128, min_amount_out: u128)`  
  *Origin*: `Signed(trader)`
- `#[pallet::call_index(2)] add_liquidity(origin: OriginFor<T>, asset_a: H256, asset_b: H256, amount_a: u128, amount_b: u128)`  
  *Origin*: `Signed(provider)`

**Events**: `PoolCreated`, `SwapExecuted`, `LiquidityAdded`, `LiquidityRemoved` | **Errors**: `PoolExists`, `SlippageExceeded`, `InsufficientLiquidity`.

---

### 11.6 Pallet `dynamic-blocks`

**Functional Responsibility**: Adjusts block generation parameters dynamically based on network throughput, gas demand, and P2P propagation latency. Configures poll intervals between 10ms (high load) and 1000ms (idle).

**Storage Items**:
- `TargetBlockTime<T>: StorageValue<u64>`  Current target block time in milliseconds.
- `GasTargetUsage<T>: StorageValue<u32>`  Target gas usage percentage per block (default 75%).

**Extrinsics**:
- `#[pallet::call_index(0)] set_target_block_time(origin: OriginFor<T>, target_ms: u64)`  
  *Origin*: `Root`

**Events**: `BlockTimingUpdated` | **Errors**: `InvalidTarget`.

---

### 11.7 Pallet `faucet`

**Functional Responsibility**: Testnet token distribution faucet for developer onboarding. Enforces per-account and per-IP rate limits with unsigned transaction validation.

**Storage Items**:
- `LastClaimBlock<T>: StorageMap<Blake2_128Concat, T::AccountId, BlockNumberFor<T>>`  Last claim block number per account.
- `ClaimAmount<T>: StorageValue<u128>`  Default drip amount (10 `IL` tokens).

**Extrinsics**:
- `#[pallet::call_index(0)] request_tokens(origin: OriginFor<T>, recipient: T::AccountId)`  
  *Origin*: `ValidateUnsigned` or `Signed`

**Events**: `TokensDripped` | **Errors**: `RateLimitExceeded`, `FaucetDrained`.

---

### 11.8 Pallet `fee-distribution`

**Functional Responsibility**: Routes collected block transaction fees according to the real-yield economic model ($30\%$ validators, $30\%$ DA watchers, $25\%$ treasury, $15\%$ burnt) on `on_finalize`.

**Storage Items**:
- `CollectedFees<T>: StorageValue<u128>`  Accumulator for block transaction fees.
- `RewardPools<T>: StorageMap<Twox64Concat, RewardPoolType, u128>`  Pool balances for validators, DA, and treasury.

**Extrinsics**:
- `#[pallet::call_index(0)] claim_validator_rewards(origin: OriginFor<T>)`  
  *Origin*: `Signed(validator)`
- `#[pallet::call_index(1)] update_fee_distribution(origin: OriginFor<T>, validator_share: u32, da_share: u32, treasury_share: u32, burn_share: u32)`  
  *Origin*: `Root`

**Events**: `FeesDistributed`, `RewardsClaimed`, `TokensBurnt` | **Errors**: `RatioSumInvalid`, `NoBalanceToClaim`.

---

### 11.9 Pallet `fees-pallet`

**Functional Responsibility**: Manages base gas prices, priority fee tip calculation, and EIP-1559 style elastic gas limit adjustments per block.

**Storage Items**:
- `BaseGasPrice<T>: StorageValue<u128>`  Current base gas price in wei/lamports (default 1 Gwei).
- `ElasticMultiplier<T>: StorageValue<u32>`  Block fullness multiplier.

**Extrinsics**:
- `#[pallet::call_index(0)] set_base_gas_price(origin: OriginFor<T>, new_price: u128)`  
  *Origin*: `Root`

**Events**: `BaseGasPriceUpdated` | **Errors**: `PriceTooLow`.

---

### 11.10 Pallet `gas-sponsorship-pallet`

**Functional Responsibility**: Enables gasless transactions for end-user dApps through sponsor accounts. Applications pre-fund sponsorship pools to pay gas on behalf of user transactions.

**Storage Items**:
- `SponsorshipPools<T>: StorageMap<Blake2_128Concat, T::AccountId, SponsorshipPool>`  Sponsor account balances, whitelisted contracts, and daily spending caps.

**Extrinsics**:
- `#[pallet::call_index(0)] create_sponsorship_pool(origin: OriginFor<T>, initial_fund: u128, daily_cap: u128)`  
  *Origin*: `Signed(sponsor)`
- `#[pallet::call_index(1)] whitelist_contract(origin: OriginFor<T>, contract_address: Vec<u8>)`  
  *Origin*: `Signed(sponsor)`

**Events**: `SponsorshipPoolCreated`, `GasSponsored` | **Errors**: `PoolExhausted`, `ContractNotWhitelisted`.

---

### 11.11 Pallet `governance-pallet`

**Functional Responsibility**: On-chain democracy and proposal voting protocol. Token holders submit referenda, stake tokens to vote, and execute approved runtime calls via root origin.

**Storage Items**:
- `ReferendumCount<T>: StorageValue<u32>`  Counter for referendum IDs.
- `Referendums<T>: StorageMap<Blake2_128Concat, u32, ReferendumInfo>`  Proposal details, vote tallies (aye/nay), and execution threshold.

**Extrinsics**:
- `#[pallet::call_index(0)] propose(origin: OriginFor<T>, proposal_hash: H256, deposit: u128)`  
  *Origin*: `Signed(proposer)`
- `#[pallet::call_index(1)] vote(origin: OriginFor<T>, ref_index: u32, approve: bool, vote_amount: u128)`  
  *Origin*: `Signed(voter)`

**Events**: `Proposed`, `Voted`, `Passed`, `Executed` | **Errors**: `ReferendumIndexInvalid`, `InsufficientDeposit`.

---

### 11.12 Pallet `handles`

**Functional Responsibility**: Manages human-readable handle handles (e.g., `@bharath`) bound to multi-chain addresses. Handles support expiration, renewal, and auction-based namespace claims.

**Storage Items**:
- `HandlesMap<T>: StorageMap<Blake2_128Concat, BoundedVec<u8, ConstU32<64>>, HandleInfo>`  Handle metadata, owner, expiration block.

**Extrinsics**:
- `#[pallet::call_index(0)] claim_handle(origin: OriginFor<T>, handle: Vec<u8>)`  
  *Origin*: `Signed(who)`
- `#[pallet::call_index(1)] renew_handle(origin: OriginFor<T>, handle: Vec<u8>, duration_blocks: u32)`  
  *Origin*: `Signed(owner)`

**Events**: `HandleClaimed`, `HandleRenewed` | **Errors**: `HandleTaken`, `HandleExpired`.

---

### 11.13 Pallet `hotstuff-session`

**Functional Responsibility**: Manages HotStuff validator authority sessions, key rotation ceremonies, epoch transitions, and BLS12-381 public key aggregation.

**Storage Items**:
- `CurrentEpoch<T>: StorageValue<u64>`  Monotonically increasing epoch sequence number.
- `SessionAuthorities<T>: StorageValue<Vec<ValidatorAuthority>>`  Active BLS validator keys and indices for current epoch.

**Extrinsics**:
- `#[pallet::call_index(0)] set_session_keys(origin: OriginFor<T>, bls_key: Vec<u8>)`  
  *Origin*: `Signed(validator)`
- `#[pallet::call_index(1)] force_new_epoch(origin: OriginFor<T>)`  
  *Origin*: `Root`

**Events**: `NewEpochStarted`, `SessionKeysRotated` | **Errors**: `InvalidKeyFormat`.

---

### 11.14 Pallet `interlayer-token`

**Functional Responsibility**: Canonical native `IL` token ledger. Implements balance transfers, total supply tracking, minting (governance-only), and burn extrinsics.

**Storage Items**:
- `TotalSupply<T>: StorageValue<u128>`  Total circulating `IL` token supply.
- `Balances<T>: StorageMap<Blake2_128Concat, T::AccountId, u128>`  Account balance mapping.

**Extrinsics**:
- `#[pallet::call_index(0)] transfer(origin: OriginFor<T>, dest: T::AccountId, value: u128)`  
  *Origin*: `Signed(sender)`
- `#[pallet::call_index(1)] mint(origin: OriginFor<T>, to: T::AccountId, amount: u128)`  
  *Origin*: `Root`

**Events**: `Transfer`, `Mint`, `Burn` | **Errors**: `BalanceLow`, `PermissionDenied`.

---

### 11.15 Pallet `liteverse-pallet`

**Functional Responsibility**: LiteVerse DePIN watcher mesh coordinator. Verifies external chain block headers (BTC, ETH, SOL, DOT, TON), validates transaction Merkle proofs, manages watcher point rewards, and executes withdrawal requests.

**Storage Items**:
- `ChainHeaders<T>: StorageMap<Blake2_128Concat, ChainId, ChainHeader>`  Latest verified header per external chain.
- `VerifiedTransactions<T>: StorageMap<Blake2_128Concat, H256, TransactionProof>`  Confirmed deposit proofs.
- `LiteClientPoints<T>: StorageMap<Blake2_128Concat, T::AccountId, PointBalance>`  Watcher point reward tracking.
- `PendingWithdrawals<T>: StorageMap<Blake2_128Concat, u64, WithdrawalRequest>`  Active withdrawal queue.

**Extrinsics**:
- `#[pallet::call_index(0)] submit_chain_header(origin: OriginFor<T>, chain_id: ChainId, header: ChainHeader)`  
  *Origin*: `ValidateUnsigned` or `Signed(watcher)`
- `#[pallet::call_index(1)] verify_transaction(origin: OriginFor<T>, proof: TransactionProof)`  
  *Origin*: `Signed(watcher)`
- `#[pallet::call_index(2)] request_withdrawal(origin: OriginFor<T>, chain_id: ChainId, amount: u128, destination: Vec<u8>)`  
  *Origin*: `Signed(user)`
- `#[pallet::call_index(3)] redeem_points(origin: OriginFor<T>, points: u64)`  
  *Origin*: `Signed(watcher)`

**Events**: `ChainSynced`, `TransactionVerified`, `WithdrawalRequested`, `PointsRedeemed` | **Errors**: `InvalidHeader`, `ProofFailed`, `InsufficientPoints`.

---

### 11.16 Pallet `mel-bus-pallet`

**Functional Responsibility**: Provides cross-VM event bus dispatching. Subscribes to events emitted by EVM (`LOG0-4`), SVM (`msg!`), Move, and CosmWasm contracts and routes cross-VM messages (`CrossVmMessage`) between adapters.

**Storage Items**:
- `MessageQueue<T>: StorageMap<Blake2_128Concat, H256, CrossVmMessage>`  Queue of pending cross-VM messages.
- `SubscribedAdapters<T>: StorageValue<Vec<VmType>>`  Registered target VM adapters.

**Extrinsics**:
- `#[pallet::call_index(0)] publish_cross_vm_message(origin: OriginFor<T>, target_vm: VmType, payload: Vec<u8>)`  
  *Origin*: `Signed(contract)`

**Events**: `CrossVmMessageDispatched`, `MessageDelivered` | **Errors**: `TargetVmNotSupported`, `PayloadTooLarge`.

---

### 11.17 Pallet `mel-core-pallet`

**Functional Responsibility**: Core MEL execution engine configuration and adapter management. Maintains global `MelConfig` parameters (max gas, bundle timeout, enabled VM adapters).

**Storage Items**:
- `GlobalMelConfig<T>: StorageValue<MelConfig>`  Global MEL settings.
- `EnabledVms<T>: StorageValue<Vec<VmType>>`  List of currently active VM execution adapters.

**Extrinsics**:
- `#[pallet::call_index(0)] update_mel_config(origin: OriginFor<T>, config: MelConfig)`  
  *Origin*: `Root`
- `#[pallet::call_index(1)] toggle_vm_adapter(origin: OriginFor<T>, vm: VmType, enable: bool)`  
  *Origin*: `Root`

**Events**: `MelConfigUpdated`, `VmAdapterToggled` | **Errors**: `InvalidConfig`.

---

### 11.18 Pallet `mev-protection`

**Functional Responsibility**: Protects users against front-running and sandwich attacks. Enforces encrypted mempool transactions (timelock encryption) and commit-reveal transaction ordering.

**Storage Items**:
- `EncryptedTxQueue<T>: StorageMap<Blake2_128Concat, H256, EncryptedTx>`  Encrypted user transactions.
- `DecryptionKeys<T>: StorageMap<Blake2_128Concat, u64, Vec<u8>>`  Block decryption keys provided by validators.

**Extrinsics**:
- `#[pallet::call_index(0)] submit_encrypted_tx(origin: OriginFor<T>, encrypted_payload: Vec<u8>, reveal_block: u64)`  
  *Origin*: `Signed(user)`
- `#[pallet::call_index(1)] submit_decryption_key(origin: OriginFor<T>, block_number: u64, key: Vec<u8>)`  
  *Origin*: `Signed(validator)`

**Events**: `EncryptedTxSubmitted`, `TxDecryptedAndExecuted` | **Errors**: `InvalidDecryptionKey`.

---

### 11.19 Pallet `monitoring`

**Functional Responsibility**: Tracks network health metrics, VM adapter execution latencies, storage bloat rates, and validator uptime statistics.

**Storage Items**:
- `NodeMetrics<T>: StorageMap<Blake2_128Concat, T::AccountId, HealthMetrics>`  Per-node operational metrics.

**Extrinsics**:
- `#[pallet::call_index(0)] report_metrics(origin: OriginFor<T>, metrics: HealthMetrics)`  
  *Origin*: `Signed(node)`

**Events**: `HealthReported` | **Errors**: `MetricsInvalid`.

---

### 11.20 Pallet `multi-vm-governance`

**Functional Responsibility**: Multi-VM smart contract upgrade governance. Manages proposals to upgrade core EVM precompiles, SVM BPF loader settings, or Move system modules.

**Storage Items**:
- `VmUpgradeProposals<T>: StorageMap<Blake2_128Concat, H256, VmUpgradeProposal>`  Active contract/precompile upgrade proposals.

**Extrinsics**:
- `#[pallet::call_index(0)] propose_vm_upgrade(origin: OriginFor<T>, vm: VmType, target_address: Vec<u8>, new_bytecode: Vec<u8>)`  
  *Origin*: `Root`

**Events**: `VmUpgradeProposed`, `VmUpgradeExecuted` | **Errors**: `ProposalNotFound`.

---

### 11.21 Pallet `native-assets`

**Functional Responsibility**: Multi-asset token registry supporting multi-chain native tokens (BTC, ETH, SOL, DOT, TON) within the InterLayer runtime.

**Storage Items**:
- `Assets<T>: StorageMap<Blake2_128Concat, H256, AssetDetails>`  Asset metadata, total supply, decimals.
- `AssetBalances<T>: StorageDoubleMap<Blake2_128Concat, H256, Blake2_128Concat, T::AccountId, u128>`  User asset balances.

**Extrinsics**:
- `#[pallet::call_index(0)] create_asset(origin: OriginFor<T>, asset_id: H256, name: Vec<u8>, symbol: Vec<u8>, decimals: u8)`  
  *Origin*: `Signed(creator)`
- `#[pallet::call_index(1)] transfer_asset(origin: OriginFor<T>, asset_id: H256, target: T::AccountId, amount: u128)`  
  *Origin*: `Signed(sender)`

**Events**: `AssetCreated`, `AssetTransferred` | **Errors**: `AssetExists`, `BalanceTooLow`.

---

### 11.22 Pallet `pallet-agent`

**Functional Responsibility**: Autonomous AI agent contract execution pallet. Allows autonomous AI agents to sign transactions using registered agent keypairs under execution budget constraints.

**Storage Items**:
- `Agents<T>: StorageMap<Blake2_128Concat, T::AccountId, AgentProfile>`  Registered agent metadata, owner, compute budget.

**Extrinsics**:
- `#[pallet::call_index(0)] register_agent(origin: OriginFor<T>, agent_id: T::AccountId, max_daily_spend: u128)`  
  *Origin*: `Signed(owner)`
- `#[pallet::call_index(1)] execute_agent_tx(origin: OriginFor<T>, agent_id: T::AccountId, tx: MelTx)`  
  *Origin*: `Signed(agent)`

**Events**: `AgentRegistered`, `AgentTxExecuted` | **Errors**: `AgentBudgetExceeded`, `UnauthorizedAgent`.

---

### 11.23 Pallet `payment-channels-pallet`

**Functional Responsibility**: Layer-2 state channels for high-frequency micropayments between accounts with off-chain balance updates and on-chain dispute resolution.

**Storage Items**:
- `Channels<T>: StorageMap<Blake2_128Concat, H256, PaymentChannel>`  Channel deposit, participants, nonce, state.

**Extrinsics**:
- `#[pallet::call_index(0)] open_channel(origin: OriginFor<T>, recipient: T::AccountId, deposit: u128)`  
  *Origin*: `Signed(sender)`
- `#[pallet::call_index(1)] close_channel(origin: OriginFor<T>, channel_id: H256, final_balance_a: u128, final_balance_b: u128, sig_a: Vec<u8>, sig_b: Vec<u8>)`  
  *Origin*: `Signed(participant)`

**Events**: `ChannelOpened`, `ChannelClosed` | **Errors**: `ChannelNotFound`, `SignatureInvalid`.

---

### 11.24 Pallet `pq-signatures`

**Functional Responsibility**: Handles verification for post-quantum digital signature algorithms (CRYSTALS-Dilithium, Falcon-512, SPHINCS+).

**Storage Items**:
- `PqKeys<T>: StorageMap<Blake2_128Concat, T::AccountId, (SignatureScheme, Vec<u8>)>`  Registered post-quantum public keys per account.

**Extrinsics**:
- `#[pallet::call_index(0)] register_pq_key(origin: OriginFor<T>, scheme: SignatureScheme, pubkey: Vec<u8>)`  
  *Origin*: `Signed(user)`

**Events**: `PqKeyRegistered` | **Errors**: `SchemeNotSupported`.

---

### 11.25 Pallet `quantum-signatures`

**Functional Responsibility**: Hybrid classical + post-quantum dual signature verification. Combines ed25519/ECDSA with Dilithium for ultra-secure transaction authentication.

**Storage Items**:
- `HybridPolicies<T>: StorageMap<Blake2_128Concat, T::AccountId, HybridPolicy>`  Verification policy requirement per account.

**Extrinsics**:
- `#[pallet::call_index(0)] set_hybrid_policy(origin: OriginFor<T>, policy: HybridPolicy)`  
  *Origin*: `Signed(account)`

**Events**: `PolicyUpdated` | **Errors**: `InvalidPolicy`.

---

### 11.26 Pallet `rate-limit`

**Functional Responsibility**: Protects runtime RPC endpoints and extrinsics against denial-of-service (DoS) spam through dynamic rate limiting per account and IP address.

**Storage Items**:
- `AccountCallCounts<T>: StorageDoubleMap<Blake2_128Concat, T::AccountId, Blake2_128Concat, u16, u32>`  Maps `(account, call_index)` to current block count.

**Extrinsics**:
- `#[pallet::call_index(0)] update_rate_limit(origin: OriginFor<T>, call_index: u16, max_calls_per_block: u32)`  
  *Origin*: `Root`

**Events**: `RateLimitUpdated` | **Errors**: `ExceededLimit`.

---

### 11.27 Pallet `registry-pallet`

**Functional Responsibility**: Global registry for smart contract deployments across all 5 VMs. Maps contract addresses to bytecode hashes, developer identities, and audit records.

**Storage Items**:
- `ContractRegistry<T>: StorageMap<Blake2_128Concat, Vec<u8>, ContractMetadata>`  Registry of deployed contracts.

**Extrinsics**:
- `#[pallet::call_index(0)] register_contract(origin: OriginFor<T>, contract_address: Vec<u8>, vm: VmType, code_hash: H256)`  
  *Origin*: `Signed(developer)`

**Events**: `ContractRegistered` | **Errors**: `AlreadyRegistered`.

---

### 11.28 Pallet `session-management`

**Functional Responsibility**: Handles validator session transitions, block author assignment, and validator set rotation on epoch boundaries.

**Storage Items**:
- `ActiveSession<T>: StorageValue<u32>`  Current session index.

**Extrinsics**:
- `#[pallet::call_index(0)] force_rotate_session(origin: OriginFor<T>)`  
  *Origin*: `Root`

**Events**: `SessionRotated` | **Errors**: `RotationFailed`.

---

### 11.29 Pallet `settlement-pallet`

**Functional Responsibility**: Manages off-chain batch settlement processing for LiteVerse watcher deposits and MPC threshold signature verification.

**Storage Items**:
- `PendingSettlements<T>: StorageMap<Blake2_128Concat, H256, SettlementBatch>`  Queued settlement batches.

**Extrinsics**:
- `#[pallet::call_index(0)] submit_settlement_batch(origin: OriginFor<T>, batch: SettlementBatch)`  
  *Origin*: `Signed(validator)`

**Events**: `SettlementCompleted` | **Errors**: `BatchInvalid`.

---

### 11.30 Pallet `slashing`

**Functional Responsibility**: Enforces cryptoeconomic security by slashing validator stake for double-signing, equivocation, or prolonged offline downtime.

**Storage Items**:
- `SlashedOffenders<T>: StorageMap<Blake2_128Concat, T::AccountId, SlashRecord>`  Recorded slash events.

**Extrinsics**:
- `#[pallet::call_index(0)] report_equivocation(origin: OriginFor<T>, proof: EquivocationProof)`  
  *Origin*: `Signed(reporter)`

**Events**: `ValidatorSlashed` | **Errors**: `ProofInvalid`.

---

### 11.31 Pallet `smart-accounts`

**Functional Responsibility**: ERC-4337 style Account Abstraction for all VMs. Supports webauthn keys, session keys, and custom transaction validation logic.

**Storage Items**:
- `SmartAccountConfig<T>: StorageMap<Blake2_128Concat, T::AccountId, SmartAccountSettings>`  User AA settings.

**Extrinsics**:
- `#[pallet::call_index(0)] execute_user_op(origin: OriginFor<T>, user_op: UserOperation)`  
  *Origin*: `Signed(bundler)`

**Events**: `UserOpExecuted` | **Errors**: `UserOpValidationFailed`.

---

### 11.32 Pallet `staking-pallet`

**Functional Responsibility**: Manages validator bonding, minimum stake thresholds, and reward distribution calculations for validator nodes.

**Storage Items**:
- `StakingBonds<T>: StorageMap<Blake2_128Concat, T::AccountId, u128>`  Validator self-bonds.

**Extrinsics**:
- `#[pallet::call_index(0)] bond_extra(origin: OriginFor<T>, amount: u128)`  
  *Origin*: `Signed(validator)`

**Events**: `BondIncreased` | **Errors**: `InsufficientFunds`.

---

### 11.33 Pallet `treasury-liquidity`

**Functional Responsibility**: Manages the community treasury (receiving $25\%$ of block fees) and disbursements for ecosystem grants, AMM liquidity seeding, and audits.

**Storage Items**:
- `TreasuryBalance<T>: StorageValue<u128>`  Treasury funds pool balance.
- `GrantProposals<T>: StorageMap<Blake2_128Concat, u32, GrantProposal>`  Active proposals.

**Extrinsics**:
- `#[pallet::call_index(0)] propose_grant(origin: OriginFor<T>, beneficiary: T::AccountId, amount: u128)`  
  *Origin*: `Signed(proposer)`

**Events**: `GrantProposed`, `GrantApproved` | **Errors**: `TreasuryEmpty`.

---

### 11.34 Pallet `unified-address-registry`

**Functional Responsibility**: Maps human-readable handles (e.g., `@bharath`) to native VM addresses across EVM, SVM, PolkaVM, Move, CosmWasm, and Substrate.

**Storage Items**:
- `HandleRegistrations<T>: StorageMap<Blake2_128Concat, BoundedVec<u8, ConstU32<64>>, HandleRegistration>`  Registered handles.
- `AddressMappings<T>: StorageMap<Blake2_128Concat, (BoundedVec<u8, ConstU32<64>>, ChainDomain), AddressMapping>`  Target chain address mappings.

**Extrinsics**:
- `#[pallet::call_index(0)] register_handle(origin: OriginFor<T>, handle: Vec<u8>, domains: Vec<ChainDomain>)`  
  *Origin*: `Signed(user)`
- `#[pallet::call_index(1)] create_address_mapping(origin: OriginFor<T>, handle: Vec<u8>, domain: ChainDomain, address: Vec<u8>)`  
  *Origin*: `Signed(owner)`

**Events**: `HandleRegistered`, `AddressMappingCreated` | **Errors**: `HandleExists`, `Unauthorized`.

---

### 11.35 Pallet `unified-balance`

**Functional Responsibility**: Maintains unified balance accounting across all internal VM adapters, ensuring cross-VM balance conservation invariant ($\sum \text{Bal}_{\text{VM}} = \text{Balance}_{\text{Unified}}$).

**Storage Items**:
- `UnifiedBalances<T>: StorageMap<Blake2_128Concat, T::AccountId, UnifiedBalanceRecord>`  User balances per VM adapter.

**Extrinsics**:
- `#[pallet::call_index(0)] transfer_vm_balance(origin: OriginFor<T>, source_vm: VmType, target_vm: VmType, amount: u128)`  
  *Origin*: `Signed(user)`

**Events**: `VmBalanceTransferred` | **Errors**: `InsufficientVmBalance`.

---

### 11.36 Pallet `validator-wallet`

**Functional Responsibility**: Dedicated custody module for validator operational funds and node maintenance expenses.

**Storage Items**:
- `ValidatorWallets<T>: StorageMap<Blake2_128Concat, T::AccountId, u128>`  Operational balances.

**Extrinsics**:
- `#[pallet::call_index(0)] withdraw_operational_funds(origin: OriginFor<T>, amount: u128)`  
  *Origin*: `Signed(validator)`

**Events**: `OperationalFundsWithdrawn` | **Errors**: `OverdrawLimit`.

---

### 11.37 Pallet `vm-adapter-monitor`

**Functional Responsibility**: Real-time performance monitoring and fault isolation for the 5 VM adapters. Disables an adapter if panic rates exceed safety thresholds.

**Storage Items**:
- `VmFaultCounters<T>: StorageMap<Twox64Concat, VmType, u32>`  Recorded fault counts per VM.

**Extrinsics**:
- `#[pallet::call_index(0)] reset_fault_counter(origin: OriginFor<T>, vm: VmType)`  
  *Origin*: `Root`

**Events**: `VmFaultRecorded`, `VmAutoDisabled` | **Errors**: `VmNotRegistered`.

---

### 11.38 Pallet `zk-verification`

**Functional Responsibility**: Zero-Knowledge proof verification engine supporting Groth16, PLONK, and STARK proof validation for privacy-preserving atomic transactions.

**Storage Items**:
- `VerificationKeys<T>: StorageMap<Blake2_128Concat, H256, Vec<u8>>`  Registered ZK verification keys.

**Extrinsics**:
- `#[pallet::call_index(0)] verify_zk_proof(origin: OriginFor<T>, vk_id: H256, proof: Vec<u8>, public_inputs: Vec<u8>)`  
  *Origin*: `Signed(prover)`

**Events**: `ProofVerified` | **Errors**: `ProofInvalid`, `VkNotFound`.

---

## Chapter 12: Wire-Format & Binary Serialization Specifications (SCALE, RLP, Borsh, BCS, Wasm)

This chapter defines the normative byte-level serialization rules for all transaction envelopes, cross-VM messages, and VM-native payloads supported by InterLayer. A decoder MUST reject trailing bytes, an invalid discriminant, a non-canonical length, a field above its published bound, or a payload whose declared VM format does not match `vm_type`.

### 12.1 Canonical Encoding Conventions

- Unsigned fixed-width integers use little-endian encoding unless the native VM format specifies otherwise. `u64`, `u32`, and `u128` occupy 8, 4, and 16 bytes respectively.
- `Vec<T>` in SCALE is `Compact<u32>(count_or_byte_length) || encoded elements`. For values below $2^{30}$, the compact prefix is one to four bytes and MUST be minimally encoded.
- Fixed hashes are 32 raw bytes. Addresses and signatures are byte vectors so that VM-native encodings remain lossless; their permitted lengths are validated by the target adapter.
- Enumerations are encoded as a single `u8` discriminant. `VmType`: EVM=`0`, SVM=`1`, PolkaVM=`2`, Move=`3`, CosmWasm=`4`. `AuthScheme`: ECDSA=`0`, Ed25519=`1`, Sr25519=`2`, Schnorr=`3`, Native=`4`, Simulation=`5`.
- All signatures use an explicitly domain-separated preimage. Byte strings in examples are hexadecimal with a `0x` prefix; JSON numbers that can exceed IEEE-754 precision are decimal strings.

### 12.2 `MelTx` Universal Transaction Byte Layout

The canonical `MelTx` envelope is serialized as the following SCALE field sequence. `Compact<u32>` is abbreviated as `C<u32>` below.

```
+------------------+-----------------------+------------------------------------------+
| Field            | Encoding              | Semantics                                |
+------------------+-----------------------+------------------------------------------+
| from             | C<u32> || bytes       | Sender's VM-native address               |
| to               | C<u32> || bytes       | Recipient's VM-native address            |
| vm               | u8                    | VM discriminant                          |
| payload          | C<u32> || bytes       | VM-native calldata or program input      |
| gas_budget       | u64 LE                | Maximum calibrated gas                   |
| nonce            | u64 LE                | Sender replay-protection sequence        |
| chain_id         | u64 LE                | `9999` for the Gravity EVM profile       |
| vm_version       | u32 LE                | Target adapter compatibility version     |
| max_fee          | u128 LE               | Maximum fee authorized in base units     |
| deadline         | u64 LE                | Milliseconds since Unix epoch            |
| capabilities     | u8 || u8              | `cross_vm_call`, then `atomic` booleans  |
| signature        | C<u32> || bytes       | Authentication signature                 |
| access_list      | C<u32> || entries     | See access-list entry below              |
| auth_scheme      | u8                    | Authentication-scheme discriminant       |
| value            | u128 LE               | Native value transferred with the call   |
| memo             | C<u32> || bytes       | Opaque application metadata              |
+------------------+-----------------------+------------------------------------------+
```

Each access-list entry is `C<u32> || address_bytes || C<u32> || (C<u32> || storage_key_bytes)*`. The `signature` field is encoded as an empty vector when computing the signing preimage:

$$m_{\rm tx}=\texttt{"IL-MEL-TX-V1"}\,||\,\operatorname{SCALE}(\texttt{MelTx}\{\texttt{signature}=\emptyset\}).$$

The adapter validates the `from`, `to`, `payload`, `signature`, access-list, and memo bounds before execution. The testnet maximums are published in Appendix A; clients MUST obtain live limits from `interlayer_getNetworkParameters` rather than hard-code a release value.

### 12.3 `AtomicBundle` Wire Format Layout

```
+----------------+--------------------------+-------------------------------------------+
| Field          | Encoding                 | Semantics                                 |
+----------------+--------------------------+-------------------------------------------+
| id             | [u8; 32]                 | Canonical bundle identifier               |
| transactions   | C<u32> || MelTx*         | Ordered, non-empty MEL transaction vector |
| timeout        | u64 LE                   | Bundle deadline in Unix milliseconds      |
| source_vm      | u8                       | VM of the first operation                 |
| target_vm      | u8                       | VM expected to produce the initial effect |
+----------------+--------------------------+-------------------------------------------+
```

For identifier computation, `id` is zeroed and every nested `MelTx.signature` is empty. The bundle identifier is

$$\texttt{id}=\text{Blake2b-256}(\texttt{"IL-ATOMIC-BUNDLE-V1"}\,||\,\operatorname{SCALE}(\texttt{bundle}_{\texttt{id}=0})).$$

This prevents self-referential encoding and makes the ordered call sequence, deadlines, capabilities, maximum fees, and payloads tamper evident.

### 12.4 `CrossVmMessage` Wire Format Layout

`CrossVmMessage` uses SCALE in the following order:

```
+----------------+--------------------------+-------------------------------------------+
| Field          | Encoding                 | Semantics                                 |
+----------------+--------------------------+-------------------------------------------+
| id             | [u8; 32]                 | Message identifier                        |
| source_vm      | u8                       | Origin VM                                 |
| target_vm      | u8                       | Destination VM                            |
| source_address | C<u32> || bytes          | Origin contract address                   |
| target_address | C<u32> || bytes          | Destination contract address              |
| message_type   | u8                       | Call=0, Transfer=1, Sync=2, Event=3, Reply=4 |
| payload        | C<u32> || bytes          | Target-VM encoded payload                 |
| timestamp      | u64 LE                   | Creation time in Unix milliseconds        |
| signature      | C<u32> || bytes          | Authorizing signature                     |
+----------------+--------------------------+-------------------------------------------+
```

The signing preimage is `"IL-CROSS-VM-MSG-V1" || SCALE(message with signature empty)`. Receivers MUST verify source identity, target VM, adapter version, expiry, message identifier uniqueness, and signature before delivery. A message emitted within an `AtomicBundle` is staged with the enclosing state diff and becomes observable only after bundle commit.

### 12.5 Per-VM Serialization Protocols

| VM Standard | Serialization Protocol | Key Specifications |
| :--- | :--- | :--- |
| **EVM** | **RLP / EIP-2718** | Legacy transactions are one RLP list; typed transactions are `type_byte || rlp(body)`. Type `0x01` is EIP-2930, `0x02` EIP-1559, and `0x03` EIP-4844 when enabled. The adapter validates chain ID, signature parity, intrinsic gas, access list, and typed-envelope length before execution. |
| **SVM** | **Borsh / Solana shortvec** | Program instruction payloads use Borsh where the program ABI declares it: fixed integers are LE and vectors are `u32 LE || bytes`. A complete Solana-compatible transaction uses Solana shortvec for vector counts; Borsh and shortvec are not interchangeable. |
| **PolkaVM** | **SCALE / PolkaVM blob** | Calls use SCALE for outer MEL fields. Executable modules are validated PolkaVM blobs; an ELF file is a build input and is not a runtime wire format. |
| **Move VM** | **BCS** | BCS uses ULEB128 vector lengths and enum tags, fixed-width integers in LE, and canonical struct field order. Module bytecode, entry-function arguments, and resource bytes are each length-delimited. |
| **CosmWasm** | **Wasm / UTF-8 JSON** | A Wasm module begins `00 61 73 6d 01 00 00 00`; each section is `section_id || ULEB128(size) || contents`. Instantiate, execute, and query messages are UTF-8 JSON bytes that must conform to the contract schema. |

### 12.6 Native Payload Validation Matrix

| Adapter | Required pre-execution validation | Returned bytes |
| :--- | :--- | :--- |
| EVM | EIP-2718 envelope, RLP canonicality, ECDSA recovery, chain ID, gas fields | ABI return data and log records |
| SVM | Account-meta bounds, program ID, instruction serialization, signer/writable flags | Program return data and event log |
| PolkaVM | Blob validation, entrypoint selector, SCALE argument bounds | SCALE-encoded return value |
| Move | Module/entry-function identifier, BCS arguments, bytecode verification | BCS return values and events |
| CosmWasm | Wasm validation, import allowlist, UTF-8 JSON schema, gas limit | UTF-8 JSON response and events |

---

## Chapter 13: Exhaustive JSON-RPC Interface & API Specification (Core, MEL, LiteVerse, MPC)

InterLayer nodes expose a JSON-RPC 2.0 API for dApps, wallets, indexers, and watcher operators. The API is transport-neutral over HTTPS and WebSocket. A public endpoint MAY expose read methods only; submission and operator methods require the endpoint's configured authentication and rate-limit policy. Requests are case-sensitive and MUST use the exact method names below.

### 13.1 JSON-RPC Envelope and Common Schemas

Every request and response conforms to JSON-RPC 2.0:

```json
{
  "jsonrpc": "2.0",
  "id": "client-chosen-string-or-number",
  "method": "namespace_methodName",
  "params": ["positional parameters as specified for the method"]
}
```

`id` MUST be a string, number, or null. Notifications omit `id` and do not receive a response. Binary fields are `0x`-prefixed, even-length hexadecimal strings. Quantities are decimal strings unless a field explicitly says hexadecimal. A block reference is either `"latest"`, `"finalized"`, or a non-negative decimal block-number string.

```json
{
  "type": "object",
  "required": ["vm", "from", "to", "payload", "gas_budget", "nonce", "chain_id", "max_fee", "deadline_ms", "signature"],
  "properties": {
    "vm": {"enum": ["EVM", "SVM", "PolkaVM", "Move", "CosmWasm"]},
    "from": {"type": "string", "pattern": "^0x([0-9a-fA-F]{2})+$"},
    "to": {"type": "string", "pattern": "^0x([0-9a-fA-F]{2})+$"},
    "payload": {"type": "string", "pattern": "^0x([0-9a-fA-F]{2})*$"},
    "gas_budget": {"type": "string", "pattern": "^[0-9]+$"},
    "nonce": {"type": "string", "pattern": "^[0-9]+$"},
    "chain_id": {"type": "string", "pattern": "^[0-9]+$"},
    "max_fee": {"type": "string", "pattern": "^[0-9]+$"},
    "deadline_ms": {"type": "string", "pattern": "^[0-9]+$"},
    "signature": {"type": "string", "pattern": "^0x([0-9a-fA-F]{2})*$"},
    "auth_scheme": {"enum": ["Ecdsa", "Ed25519", "Sr25519", "Schnorr", "Native"]},
    "value": {"type": "string", "pattern": "^[0-9]+$"},
    "memo": {"type": "string"}
  }
}
```

The schema above is the JSON representation of `MelTx`; omitted optional fields use the current protocol defaults. RPC serialization uses native-address bytes for `from` and `to`; presentation-layer encodings such as Base58, SS58, or Bech32 are accepted only where a method explicitly permits them.

### 13.2 `interlayer_*` Core Namespace

| Method | Parameters | Result |
| :--- | :--- | :--- |
| `interlayer_submitAtomicBundle` | `[AtomicBundleRequest]` | `BundleAcceptance` |
| `interlayer_getBundle` | `[bundle_id]` | `BundleStatus` |
| `interlayer_getReceipt` | `[tx_or_bundle_id]` | `ExecutionReceipt` |
| `interlayer_getUnifiedAddress` | `[handle_or_address]` | `UnifiedAddressRecord` |
| `interlayer_getNetworkParameters` | `[]` | `NetworkParameters` |

#### 13.2.1 `interlayer_submitAtomicBundle`

Submits a signed multi-VM atomic bundle. Submission acknowledges admission to the transaction pool; it does not imply execution or finality.

**Request schema**: `AtomicBundleRequest` is `{ "transactions": [MelTx, ...], "deadline_ms": decimal-string, "source_vm": VmType, "target_vm": VmType }`. `transactions` MUST be non-empty and MUST NOT exceed `max_bundle_size`.

**Request example**:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "interlayer_submitAtomicBundle",
  "params": [{
    "transactions": [
      {
        "vm": "EVM",
        "from": "0x71C7656EC7ab88b098defB751B7401B5f6d8976F",
        "to": "0x1111111111111111111111111111111111111111",
        "payload": "0xa9059cbb00000000000000000000000022222222222222222222222222222222222222220000000000000000000000000000000000000000000000000de0b6b3a7640000",
        "gas_budget": 100000,
        "nonce": 12
      },
      {
        "vm": "SVM",
        "from": "7xK8mP9vN2qR5wT1yU3iO4pL6kJ8hG2fD1sA9zC7xB5v",
        "to": "9zC7xB5v7xK8mP9vN2qR5wT1yU3iO4pL6kJ8hG2fD1sA",
        "payload": "0x02000000010000000005f5e100",
        "gas_budget": 50000,
        "nonce": 4
      }
    ],
    "deadline_ms": 1786400000000
  }]
}
```

**Response schema**: `BundleAcceptance` is `{ "bundle_id": hex-32, "status": "Pending", "accepted_at_ms": decimal-string, "pool_position": decimal-string|null }`.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "bundle_id": "0x9f8e7d6c5b4a3f2e1d0c9b8a7f6e5d4c3b2a1f0e9d8c7b6a5f4e3d2c1b0a9f8e",
    "status": "Pending",
    "accepted_at_ms": "1786399700123",
    "pool_position": "14"
  }
}
```

#### 13.2.2 `interlayer_getBundle` and `interlayer_getReceipt`

`interlayer_getBundle` accepts one 32-byte hexadecimal `bundle_id`. Its `BundleStatus` result is `{ "bundle_id", "status", "block_number", "finalized", "gas_used", "rollback_reason" }`, where `status` is `Pending`, `Executing`, `Committed`, `RolledBack`, `Expired`, or `Rejected`. Fields not known in the current lifecycle state are `null`.

`interlayer_getReceipt` accepts a 32-byte transaction or bundle identifier. Its `ExecutionReceipt` result is `{ "id", "success", "vm", "gas_used", "block_number", "state_root", "return_data", "events", "error" }`. `events` is an array of `{ "emitter", "topic", "topics", "data", "source_vm", "target_vms" }`; `state_root` is present only for committed execution.

#### 13.2.3 `interlayer_getUnifiedAddress`

Resolves a handle or canonical address to the active, registry-committed native addresses across all five VMs. It never derives an address by hash truncation at query time.

**Request**: `{"jsonrpc":"2.0","id":2,"method":"interlayer_getUnifiedAddress","params":["@bharath"]}`  
**Response**:
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "handle": "@bharath",
    "canonical_address": "0x4a2b...8f1c",
    "vm_addresses": {
      "EVM": "0x71C7656EC7ab88b098defB751B7401B5f6d8976F",
      "SVM": "7xK8mP9vN2qR5wT1yU3iO4pL6kJ8hG2fD1sA9zC7xB5v",
      "PolkaVM": "5GrwvaEF5zXb26Fz9rcQpDWS5CTERHpNehXCPcNoHGKutQYd",
      "Move": "0x00000000000000000000000000000001",
      "CosmWasm": "il1q2w3e4r5t6y7u8i9o0p"
    }
  }
}
```

#### 13.2.4 `interlayer_getNetworkParameters`

Returns `{ "chain_id", "runtime_spec_version", "finalized_block", "max_gas_per_tx", "max_bundle_size", "max_cross_vm_message_size", "fee_policy", "enabled_vms" }`. `fee_policy` is `{ "validator_bps", "watcher_bps", "treasury_bps", "burn_bps" }`, whose four values MUST sum to `10000`.

### 13.3 `mel_*` Execution Namespace

| Method | Parameters | Result |
| :--- | :--- | :--- |
| `mel_simulateTransaction` | `[MelTx, block_ref?]` | `SimulationResult` |
| `mel_estimateGas` | `[MelTx, block_ref?]` | `GasEstimate` |
| `mel_getVmStatus` | `[VmType]` | `VmStatus` |
| `mel_getCrossVmMessage` | `[message_id]` | `CrossVmMessageRecord` |
| `mel_listAdapters` | `[]` | `[VmStatus]` |

`mel_simulateTransaction` MUST execute against an isolated snapshot and MUST NOT enqueue a transaction, alter state, emit a consensus event, or consume a nonce. Its result is `{ "success", "gas_used", "return_data", "events", "error", "state_root" }`.

`mel_estimateGas` returns `{ "native_gas", "calibrated_gas", "coordination_gas", "max_fee", "warnings" }`. A result is an estimate, not a fee guarantee; the node calculates admission from the signed transaction's declared budget and fee cap.

`mel_getVmStatus` and `mel_listAdapters` return `{ "vm", "enabled", "adapter_version", "last_finalized_block", "fault_count", "max_payload_bytes" }`. `mel_getCrossVmMessage` returns `{ "message", "status", "bundle_id", "delivered_block", "error" }`, where `message` follows the JSON form of the Chapter 12 layout.

**Simulation request and response example**:

```json
{
  "jsonrpc": "2.0",
  "id": "sim-7",
  "method": "mel_simulateTransaction",
  "params": [{"vm":"Move","from":"0x01","to":"0x02","payload":"0x","gas_budget":"200000","nonce":"9","chain_id":"9999","max_fee":"200000000000000","deadline_ms":"1786400000000","signature":"0x"}, "finalized"]
}
```

```json
{"jsonrpc":"2.0","id":"sim-7","result":{"success":true,"gas_used":"48123","return_data":"0x","events":[],"error":null,"state_root":"0x6d...c1"}}
```

### 13.4 `liteverse_*` DePIN Watcher Namespace

| Method | Parameters | Result |
| :--- | :--- | :--- |
| `liteverse_getDepositAddress` | `[owner, chain]` | `DepositAddressRecord` |
| `liteverse_getDepositStatus` | `[chain, tx_hash]` | `DepositStatus` |
| `liteverse_getWithdrawal` | `[withdrawal_id]` | `WithdrawalStatus` |
| `liteverse_getWatcherSet` | `[chain, epoch?]` | `WatcherSet` |
| `liteverse_getSupportedChains` | `[]` | `[ChainCapability]` |

#### 13.4.1 `liteverse_getDepositAddress`
Retrieves the user's unique per-chain deposit address monitored by LiteVerse watchers.

**Request**: `{"jsonrpc":"2.0","id":3,"method":"liteverse_getDepositAddress","params":["@bharath", "Bitcoin"]}`  
**Response**: `{"jsonrpc":"2.0","id":3,"result":{"chain":"Bitcoin","deposit_address":"bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh","derivation_epoch":"12","threshold":"2-of-3","active":true}}`

`DepositStatus` is `{ "chain", "tx_hash", "status", "confirmations", "required_confirmations", "credited_amount", "unified_recipient", "observed_at_ms", "finalized_block" }`, where `status` is `Unknown`, `Observed`, `ProofSubmitted`, `Confirmed`, `Credited`, `Rejected`, or `Reverted`.

`WithdrawalStatus` is `{ "withdrawal_id", "chain", "amount", "destination", "status", "signing_epoch", "external_tx_hash", "error" }`. The RPC API exposes status only: it does not expose private signing shares or an unrestricted signing endpoint.

`WatcherSet` is `{ "chain", "epoch", "threshold", "watchers": [{"id", "public_key", "active", "points"}] }`. `ChainCapability` is `{ "chain", "enabled", "address_formats", "required_confirmations", "max_proof_bytes" }`.

### 13.5 `mpc_*` Threshold-Signer Namespace

| Method | Parameters | Result |
| :--- | :--- | :--- |
| `mpc_getSignerStatus` | `[signer_id?]` | `SignerStatus` or `[SignerStatus]` |
| `mpc_getKeyInfo` | `[epoch]` | `EpochKeyInfo` |
| `mpc_getSigningRequest` | `[request_id]` | `SigningRequestStatus` |
| `mpc_listEpochKeys` | `[from_epoch?, limit?]` | `[EpochKeyInfo]` |
| `mpc_getDerivationPolicy` | `[]` | `DerivationPolicy` |

`SignerStatus` is `{ "signer_id", "epoch", "online", "last_heartbeat_ms", "supported_chains", "public_key" }`. `EpochKeyInfo` is `{ "epoch", "group_public_key", "threshold", "participants", "dkg_transcript_hash", "active_from_block", "retired_at_block" }`.

`SigningRequestStatus` is `{ "request_id", "kind", "request_digest", "epoch", "status", "selected_signers", "signature", "external_tx_hash", "error" }`, where `status` is `Queued`, `CommitmentRound`, `ShareRound`, `Completed`, `Expired`, or `Rejected`. It MUST NOT return nonce material, secret shares, or signer-to-signer transport messages.

`DerivationPolicy` is `{ "coin_type", "path_template", "non_hardened_only", "chain_binding", "epoch_binding" }`.

**MPC status example**:

```json
{"jsonrpc":"2.0","id":4,"method":"mpc_getKeyInfo","params":["12"]}
```

```json
{"jsonrpc":"2.0","id":4,"result":{"epoch":"12","group_public_key":"0x02...af","threshold":"2","participants":"3","dkg_transcript_hash":"0x8f...14","active_from_block":"142000","retired_at_block":null}}
```

### 13.6 Error Model

All failures use the JSON-RPC `error` object: `{ "code": integer, "message": string, "data": object|null }`. `data` MAY contain `field`, `limit`, `expected`, `actual`, `bundle_id`, or `retry_after_ms`, but MUST NOT contain key material, stack traces, database details, or unredacted third-party payloads.

| Code | Name | Meaning |
| ---: | :--- | :--- |
| `-32700` | Parse error | Invalid JSON document. |
| `-32600` | Invalid request | Invalid JSON-RPC envelope. |
| `-32601` | Method not found | Unknown or disabled method. |
| `-32602` | Invalid params | Schema, encoding, or range violation. |
| `-32603` | Internal error | Unexpected node failure; details are redacted. |
| `-32000` | Execution rejected | Transaction or bundle failed admission. |
| `-32001` | Invalid signature | Authentication or signing preimage is invalid. |
| `-32002` | Nonce conflict | Nonce is stale, duplicated, or has an invalid gap. |
| `-32003` | Gas or fee cap | Calibrated gas or maximum fee is insufficient. |
| `-32004` | Deadline expired | Transaction, message, or bundle deadline elapsed. |
| `-32005` | VM unavailable | Requested VM adapter is disabled or unhealthy. |
| `-32006` | Proof rejected | External-chain proof or finality evidence failed. |
| `-32007` | Not found | Canonical object identifier is unknown. |
| `-32008` | Rate limited | Endpoint policy rejected the request. |
| `-32009` | Unauthorized | Caller lacks the required operator permission. |

Example: `{"jsonrpc":"2.0","id":1,"error":{"code":-32003,"message":"gas or fee cap insufficient","data":{"field":"max_fee","expected":"180000","actual":"150000"}}}`.

---

## Chapter 14: Multi-VM Smart Contract & Cross-VM Developer Integration Guide

This chapter provides complete production-grade code examples for deploying and executing smart contracts across all five supported Virtual Machine standards on InterLayer.

### 14.1 EVM Smart Contract Development (Solidity & Ethers.js)

#### Solidity Smart Contract (`InterLayerVault.sol`)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract InterLayerVault {
    mapping(address => uint256) public balances;
    event Deposit(address indexed sender, uint256 amount);
    event CrossVmWithdrawal(address indexed sender, string targetVm, uint256 amount);

    function deposit() external payable {
        require(msg.value > 0, "Amount must be positive");
        balances[msg.sender] += msg.value;
        emit Deposit(msg.sender, msg.value);
    }

    function withdrawToSvm(uint256 amount, bytes32 svmPubkey) external {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        emit CrossVmWithdrawal(msg.sender, "SVM", amount);
    }
}
```

#### Ethers.js Deployment & Cross-VM Call Script
```javascript
const { ethers } = require("ethers");

async function main() {
    // Connect to InterLayer EVM RPC endpoint
    const provider = new ethers.JsonRpcProvider("https://rpc.interlayer.one");
    const wallet = new ethers.Wallet(process.env.PRIVATE_KEY, provider);

    console.log("Deploying contract from:", wallet.address);
    const VaultFactory = await ethers.getContractFactory("InterLayerVault", wallet);
    const vault = await VaultFactory.deploy();
    await vault.waitForDeployment();
    console.log("Vault deployed to:", await vault.getAddress());

    // Execute Cross-VM Deposit
    const tx = await vault.deposit({ value: ethers.parseEther("1.0") });
    await tx.wait();
    console.log("Deposited 1.0 IL to EVM Vault");
}
main();
```

### 14.2 SVM Solana Program Development (Rust & Anchor)

#### Anchor Program (`interlayer_token_vault.rs`)
```rust
use anchor_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

#[program]
pub mod interlayer_token_vault {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
        let vault = &mut ctx.accounts.vault;
        vault.owner = *ctx.accounts.owner.key;
        vault.total_deposits = 0;
        Ok(())
    }

    pub fn deposit_spl(ctx: Context<DepositSpl>, amount: u64) -> Result<()> {
        let vault = &mut ctx.accounts.vault;
        vault.total_deposits += amount;
        msg!("Deposited {} lamports to SVM vault", amount);
        Ok(())
    }
}

#[account]
pub struct VaultState {
    pub owner: Pubkey,
    pub total_deposits: u64,
}

#[derive(Accounts)]
pub struct Initialize<'info> {
    #[account(init, payer = owner, space = 8 + 32 + 8)]
    pub vault: Account<'info, VaultState>,
    #[account(mut)]
    pub owner: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct DepositSpl<'info> {
    #[account(mut)]
    pub vault: Account<'info, VaultState>,
    pub owner: Signer<'info>,
}
```

### 14.3 PolkaVM Contract Development (Rust / ink! Style)

PolkaVM calls use SCALE-encoded selectors and arguments. Contracts should expose a narrow, deterministic interface and authorize the MEL caller before mutating state.

```rust
#[ink::contract]
mod interlayer_escrow {
    use ink::storage::Mapping;

    #[ink(storage)]
    pub struct InterlayerEscrow {
        deposits: Mapping<AccountId, Balance>,
    }

    impl InterlayerEscrow {
        #[ink(constructor)]
        pub fn new() -> Self { Self { deposits: Mapping::default() } }

        #[ink(message, payable)]
        pub fn deposit(&mut self) {
            let caller = self.env().caller();
            let value = self.env().transferred_value();
            let prior = self.deposits.get(caller).unwrap_or(0);
            self.deposits.insert(caller, &(prior.saturating_add(value)));
        }

        #[ink(message)]
        pub fn balance_of(&self, owner: AccountId) -> Balance {
            self.deposits.get(owner).unwrap_or(0)
        }
    }
}
```

### 14.4 Move Module Development

Move resources preserve asset linearity. The following module declares a vault resource and exposes a deposit entry function; deployment and invocation arguments use BCS as defined in Chapter 12.

```move
module interlayer::vault {
    use std::signer;

    struct Vault has key {
        balance: u64,
    }

    public entry fun initialize(account: &signer) {
        move_to(account, Vault { balance: 0 });
    }

    public entry fun deposit(account: &signer, amount: u64) acquires Vault {
        let addr = signer::address_of(account);
        let vault = borrow_global_mut<Vault>(addr);
        vault.balance = vault.balance + amount;
    }

    public fun balance(owner: address): u64 acquires Vault {
        borrow_global<Vault>(owner).balance
    }
}
```

### 14.5 CosmWasm Contract Development (Rust)

CosmWasm messages are UTF-8 JSON envelopes. The contract must validate its inputs, return a deterministic response, and leave cross-VM effects to an enclosing MEL bundle rather than creating an external asynchronous bridge.

```rust
#[cw_serde]
pub enum ExecuteMsg {
    Deposit { amount: Uint128 },
}

pub fn execute(
    deps: DepsMut,
    _env: Env,
    info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response, ContractError> {
    match msg {
        ExecuteMsg::Deposit { amount } => {
            let key = format!("vault/{}", info.sender);
            let current = BALANCES.may_load(deps.storage, key.clone())?.unwrap_or_default();
            BALANCES.save(deps.storage, key, &(current.checked_add(amount)?))?;
            Ok(Response::new().add_attribute("action", "deposit"))
        }
    }
}
```

### 14.6 Cross-VM Atomic Integration (`@interlayer/sdk`)

The following TypeScript snippet demonstrates constructing, signing, simulating, and submitting an atomic bundle. The SDK emits the same canonical structures defined in Chapters 12 and 13; it does not invent a separate cross-VM message format.

```typescript
import { InterLayerSDK, VmType } from "@interlayer/sdk";

async function executeCrossVmWorkflow() {
    const sdk = new InterLayerSDK({ rpcUrl: "https://rpc.interlayer.one" });
    const signer = sdk.getSigner(process.env.PRIVATE_KEY!);

    // Each call carries a VM-native payload but shares one canonical fee and deadline policy.
    const evmTx = sdk.buildTx({
        vm: VmType.EVM,
        to: "0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D",
        data: "0x38ed1739...", // swapExactTokensForTokens ABI bytes
        gasBudget: 150000
    });

    const svmTx = sdk.buildTx({
        vm: VmType.SVM,
        to: "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",
        data: Buffer.from([1, 0, 0, 0, 0, 200, 23, 0]), // Instruction payload
        gasBudget: 80000
    });

    const polkavmTx = sdk.buildTx({
        vm: VmType.PolkaVM,
        to: process.env.ESCROW_CONTRACT!,
        data: sdk.scale.encodeCall("deposit", []),
        gasBudget: 60000
    });

    const moveTx = sdk.buildTx({
        vm: VmType.Move,
        to: "0x1::vault::deposit",
        data: sdk.bcs.encode(["u64"], [1_000_000n]),
        gasBudget: 70000
    });

    const cosmwasmTx = sdk.buildTx({
        vm: VmType.CosmWasm,
        to: process.env.COSMWASM_VAULT!,
        data: new TextEncoder().encode(JSON.stringify({ deposit: { amount: "1000000" } })),
        gasBudget: 90000
    });

    const bundle = sdk.createAtomicBundle({
        transactions: [evmTx, svmTx, polkavmTx, moveTx, cosmwasmTx],
        timeoutMs: 300000 // 5 minutes
    });

    const signedBundle = await sdk.signAtomicBundle(bundle, signer);
    const simulation = await sdk.simulateAtomicBundle(signedBundle, "finalized");
    if (!simulation.success) throw new Error(simulation.error ?? "simulation failed");

    const receipt = await sdk.submitAtomicBundle(signedBundle);
    console.log("Bundle accepted:", receipt.bundleId);
    const final = await sdk.waitForBundle(receipt.bundleId, { finality: "finalized" });
    if (final.status !== "Committed") throw new Error(final.rollbackReason ?? final.status);
}
executeCrossVmWorkflow();
```

---

## Chapter 15: Formal Invariants, Verification Theorems & Architectural Mapping

### 15.1 Theorem 2 (Atomicity Invariant)

> **Statement**: For any valid atomic bundle $\mathcal{B} = [C_1, C_2, \dots, C_N]$, let $\Delta_i$ be the staged state diff produced by $C_i$. The resulting state is either $\operatorname{Apply}(\sigma,\Delta_1\circ\dots\circ\Delta_N)$ or exactly $\sigma$; no strict prefix of the diff sequence becomes visible in committed state.

$$\sigma' \in \left\{\operatorname{Apply}(\sigma,\Delta_1\circ\dots\circ\Delta_N),\;\sigma\right\}$$

**Proof**: The atomic execution state machine creates $\mathcal{S}_0=\operatorname{Snapshot}(\sigma)$ before executing any call. Every adapter writes only to its staged overlay, and every cross-VM message remains staged with that overlay. If any $C_i$ fails, the deadline expires, validation fails, or commit preparation fails, the state machine discards every overlay and restores $\mathcal{S}_0$. If every call and final validation succeeds, the runtime applies the complete ordered diff set in one state transition and then publishes staged messages and events. There is no transition that applies a strict prefix, so partial committed state is unreachable. $\blacksquare$

### 15.2 Theorem 3 (No Double Spend)

> **Statement**: Let $\text{Tx}_1,\text{Tx}_2$ be transactions from unified address $a$ that each require at least $D=U+F_{\max}$ available balance, where $F_{\max}$ is the transaction's declared fee cap. If $\text{Bal}(a)<2D$, no credit to $a$ occurs after $\text{Tx}_1$, and $\text{Tx}_1$ is finalized in $B_n$, then $\text{Tx}_2$ cannot pass balance-and-nonce validation in any later finalized state.

**Proof**: Admission reserves at most $D$ and execution debits the value plus the charged fee, never exceeding the declared cap. After $\text{Tx}_1$ commits, the available balance is strictly less than $D$ because the starting balance was less than $2D$ and no later credit occurs. Therefore $\text{Tx}_2$ fails balance validation. If it attempts to reuse $\text{Tx}_1$'s nonce, it independently fails nonce validation. HotStuff finality makes the committed debit and nonce advancement immutable under the assumed fault bound. $\blacksquare$

### 15.3 Theorem 4 (Non-Inflationary Fee Invariant)

> **Statement**: Fee routing never mints tokens. For fee flow $\dot F_{\text{dist}}\ge0$, the total-supply growth rate is $\frac{dS}{dt}=-0.15\dot F_{\text{dist}}+\dot M_{\text{gov}}$, where $\dot M_{\text{gov}}=0$ unless an explicitly authorized governance supply change executes. Thus ordinary protocol operation is non-inflationary and fee-bearing operation is deflationary.

**Proof**: Root-authorized governance is the sole origin permitted to authorize a supply change. Fee distribution transfers 30% of the distributable pool to validators, 30% to DA providers, and 25% to the treasury; these transfers conserve supply. It removes the remaining 15% through a burn, yielding $\Delta S=-0.15F_{\text{dist}}$ for a block absent governance minting. Summing over time gives the stated differential equation. $\blacksquare$

---

### 15.4 Comprehensive Architectural Symbol Mapping Table

| Mathematical Symbol | Core Architecture Layer | Implementation Interface | System Scope |
| :--- | :--- | :--- | :--- |
| $\sigma$ (Global State) | MEL Core Kernel | `trait MelStorage` | Multi-VM Core Execution Kernel |
| $\mathcal{A}$ (Atomic Operator) | MEL Core Kernel | `AtomicExecutionEngineImpl::execute_atomic_bundle_advanced` | MEL Atomic Transaction Engine |
| $\mathcal{R}$ (Rollback) | MEL Core Kernel | `AtomicExecutionEngineImpl::rollback_execution` | MEL Atomic Rollback Engine |
| $\Phi_g$ (Gas Calibration) | MEL Core Kernel | `GasCalibrationEngine`, `DynamicGasCalibrationEngine` | Gas Calibration Engine |
| $C_i$ (Contract Call) | MEL Core Kernel | `struct ContractCall { vm_type, contract_address, method, args, gas_limit }` | Multi-VM Core Execution Kernel |
| $\mathcal{B}$ (Atomic Bundle) | MEL Core Kernel | `struct AtomicBundle { id, transactions, deadline }` | Multi-VM Core Execution Kernel |
| `MelTx` (Transaction) | MEL Core Kernel | `struct MelTx { from, to, vm, payload, gas_budget, nonce, ... }` | Multi-VM Core Execution Kernel |
| `VmType` (VM Enum) | MEL Core Kernel | `enum VmType { EVM, SVM, PolkaVM, Move, CosmWasm }` | Multi-VM Core Execution Kernel |
| $\text{QC}$ (Quorum Cert) | HotStuff Consensus | `struct QuorumCertificate { block_hash, view, signatures }` | HotStuff BFT Consensus Module |
| `HotStuffEngine` | HotStuff Consensus | `struct HotStuffEngine { validators, timeout, verifier, current_view, locked_block, locked_view, ... }` | HotStuff BFT Consensus Engine |
| 3-Chain Finalization | HotStuff Consensus | `record_imported_qc()`  checks `recent_qcs[v], [v-1], [v-2]` | HotStuff 3-Chain Finality Protocol |
| $\text{SafetyProof}$ | HotStuff Consensus | `struct SafetyProof` | Consensus Safety Protocol |
| $\text{TSS}_{\text{signer}}$ | MPC Executor | `struct ThresholdSigner { key_share, threshold }` | Threshold MPC Signer Subsystem |
| DKG Key Gen | MPC Executor | `fn generate_key_shares(threshold, total) -> Vec<KeyShare>` | Off-Chain DKG Key Generation Module |
| Lagrange $\lambda_i$ | MPC Executor | `fn lagrange_coefficient(id, participants) -> Scalar` | Lagrange Interpolation Engine |
| Schnorr Verify | MPC Executor | `fn verify_signature(message, signature, public_key) -> bool` | Threshold Signature Verification |
| $\text{MEL}_{\text{EVM}}$ | MEL EVM Adapter | `struct EvmAdapter<S, G> { chain_id, state_db, precompiles, ... }` | EVM Adapter Subsystem |
| `SubstrateEvmDb` | MEL EVM Adapter | `impl DatabaseRef for SubstrateEvmDb<S>` | EVM Database Interface |
| $\text{MEL}_{\text{SVM}}$ | MEL SVM Adapter | `struct SvmAdapter<S, G>` with `solana_rbpf` execution | SVM Execution Adapter |
| SPL Token-2022 TLV | MEL SVM Adapter | `read_tlv_entries()`, `write_tlv_entries()`, `upsert_tlv_entry()` | SVM Adapter Subsystem |
| $\text{MEL}_{\text{PolkaVM}}$ | MEL PolkaVM Adapter | `struct PolkaVMAdapter<S, G>` | PolkaVM Execution Adapter |
| $\text{MEL}_{\text{Move}}$ | MEL Move Adapter | `MoveModules<T>`, `MoveResources<T>` storage | Move VM Execution Adapter |
| $\text{MEL}_{\text{CosmWasm}}$ | MEL CosmWasm Adapter | `struct CosmWasmAdapter<S, G>` with `wasmi` | CosmWasm Execution Adapter |
| CrossVmMessage | MEL Core Kernel | `struct CrossVmMessage { id, source_vm, target_vm, payload, ... }` | Cross-VM Event Bus Channel |
| $f_{\text{map}}$ (Address Resolution) | Unified Address Registry | `Pallet::resolve_address` | Unified Address Registry Pallet |
| $\Delta R$ (Fee Routing) | Fee Distribution Engine | `Pallet::distribute_block_fees` | Fee Distribution Pallet |

## Appendix A: Testnet Deployment Configuration

**Network Parameters** (Gravity Testnet):
- **Chain ID**: `9999` (EVM), custom (Substrate)
- **Block time target**: ~1 second (10ms poll interval, view-change timeout configurable)
- **Consensus**: HotStuff 3-chain BFT with `w3f-bls` BLS12-381 signatures
- **Maximum gas per transaction**: 30,000,000 (configurable via `GasCalibrationConfig`)
- **Base gas price**: 1 Gwei (1,000,000,000 wei)
- **SVM compute budget**: 1,400,000 CUs per transaction
- **SVM max account data**: 10 MB
- **DA target usage**: 75% block fullness
- **Dynamic pricing update interval**: Every 50 blocks
- **MPC threshold**: Configurable $(t, n)$, default $(2, 3)$ for testnet
- **Unbonding period**: 28 days (delegated staking)
- **MEL max bundle size**: 100 operations per atomic bundle
- **MEL timeout blocks**: 300 blocks
- **Atomic execution max timeout**: 30 minutes (1,800,000 ms)
- **Atomic execution base timeout**: 5 minutes (300,000 ms)
- **Cross-VM message max size**: 65,536 bytes
- **Merkle proof max size**: 16,384 bytes

**Live Infrastructure**:
- Portal: `portal.interlayer.one`
- Explorer: `explorer.interlayer.one`
- Wallet: `wallet.interlayer.one`
- Faucet: `faucet.interlayer.one`
- Governance: `gov.interlayer.one`

---

## Appendix B: List of Figures

| Figure | Description | Location |
| :--- | :--- | :--- |
| **Figure 1** | MEL Multi-VM Architecture — 5 execution adapters connected through unified orchestration layer | Chapter 3, after §3.1 heading |
| **Figure 2** | Atomic Cross-VM Execution Flow — 5-phase pipeline with commit/rollback branching | Chapter 3, after §3.2 SVG |
| **Figure 3** | Threshold MPC Signing Process — Lagrange interpolation aggregation flow | Chapter 8, after heading |
| **SVG 1** | Multi-VM Execution Layer Architecture (inline SVG) | Chapter 1, §1.3 |
| **SVG 2** | Global State Tuple $\sigma$ Structure & Merkle Root $H(\sigma)$ | Chapter 2, §2.4 |
| **SVG 3** | Atomic Execution Pipeline with Commit/Rollback | Chapter 3, §3.2 |
| **SVG 4** | Unified Address Bijection $f_{\text{map}}$ Mapping | Chapter 4, §4.2 |
| **SVG 5** | Liquidity Inflow/Outflow Flowchart | Chapter 5, §5.2 |
| **SVG 6** | HotStuff Pipelined Consensus Phases | Chapter 6, §6.1 |

---

## Appendix C: Public Runtime Composition Index

This is the public composition index for the 38 InterLayer-specific pallets specified in Chapter 11. It deliberately identifies protocol components rather than local source modules or implementation ordering. The authoritative callable pallet index and call indices for a deployed testnet instance are the SCALE runtime metadata returned by that instance; clients MUST use the metadata version that they connect to.

| No. | Pallet | No. | Pallet |
| ---: | :--- | ---: | :--- |
| 1 | `atomic-execution` | 20 | `multi-vm-governance` |
| 2 | `bridge-pallet` | 21 | `native-assets` |
| 3 | `data-availability-hooks` | 22 | `pallet-agent` |
| 4 | `delegated-staking` | 23 | `payment-channels-pallet` |
| 5 | `dex-pallet` | 24 | `pq-signatures` |
| 6 | `dynamic-blocks` | 25 | `quantum-signatures` |
| 7 | `faucet` | 26 | `rate-limit` |
| 8 | `fee-distribution` | 27 | `registry-pallet` |
| 9 | `fees-pallet` | 28 | `session-management` |
| 10 | `gas-sponsorship-pallet` | 29 | `settlement-pallet` |
| 11 | `governance-pallet` | 30 | `slashing` |
| 12 | `handles` | 31 | `smart-accounts` |
| 13 | `hotstuff-session` | 32 | `staking-pallet` |
| 14 | `interlayer-token` | 33 | `treasury-liquidity` |
| 15 | `liteverse-pallet` | 34 | `unified-address-registry` |
| 16 | `mel-bus-pallet` | 35 | `unified-balance` |
| 17 | `mel-core-pallet` | 36 | `validator-wallet` |
| 18 | `mev-protection` | 37 | `vm-adapter-monitor` |
| 19 | `monitoring` | 38 | `zk-verification` |

The runtime also depends on standard framework components for system state, timestamps, balances, transaction payment, utility operations, and governance dispatch. Their concrete pallet indices are intentionally not frozen by this document.

---

## Appendix D: Canonical Protocol Type Definitions

The following Rust-like definitions are normative presentation types for the data structures used in Chapters 2–13. They describe the public encoding contract, not local implementation paths. Field order is serialization order where Chapter 12 specifies SCALE.

### D.1 `MelTx`  Universal Transaction Envelope


```rust
pub struct MelTx {
    pub from: Vec<u8>,              // VM-native sender address
    pub to: Vec<u8>,                // VM-native recipient address
    pub vm: VmType,                 // Target adapter discriminant
    pub payload: Vec<u8>,           // VM-native encoded call data
    pub gas_budget: u64,            // Maximum calibrated gas units
    pub nonce: u64,                 // Replay-protection sequence number
    pub chain_id: u64,              // Network / EIP-155 domain identifier
    pub vm_version: u32,            // Target adapter compatibility version
    pub max_fee: u128,              // Maximum fee authorized in base units
    pub deadline: u64,              // Unix timestamp in milliseconds
    pub capabilities: MelTxCapabilities,
    pub signature: Vec<u8>,         // Signature over the empty-signature preimage
    pub access_list: Vec<AccessListEntry>,
    pub auth_scheme: AuthScheme,    // Signature type selector
    pub value: u128,                // Native value transferred with the call
    pub memo: Vec<u8>,              // Opaque application metadata
}
```

```rust
pub struct MelTxCapabilities {
    pub cross_vm_call: bool,        // Permits a cross-VM effect within a bundle
    pub atomic: bool,               // Requires all-or-nothing bundle execution
}

pub struct AccessListEntry {
    pub address: Vec<u8>,
    pub storage_keys: Vec<Vec<u8>>,
}
```

### D.2 `VmType`  Virtual Machine Discriminant Enum


```rust
pub enum VmType {
    EVM,        // Ethereum Virtual Machine (revm engine)
    SVM,        // Solana Virtual Machine (solana_rbpf eBPF interpreter)
    PolkaVM,    // Polkadot PolkaVM (RISC-V native execution)
    Move,       // Move Virtual Machine (Aptos/Sui linear resources)
    CosmWasm,   // Cosmos WebAssembly (wasmi interpreter)
}
```

### D.3 `ExecutionResult`  Transaction Receipt


```rust
pub struct ExecutionResult {
    pub success: bool,              // True if execution completed without error
    pub gas_used: u64,              // Actual gas consumed
    pub from: Vec<u8>,              // Sender address in the target VM format
    pub to: Vec<u8>,                // Recipient address in the target VM format
    pub return_data: Vec<u8>,       // Return value from contract execution
    pub events: Vec<MelEvent>,      // Emitted events (cross-VM capable)
    pub error_message: Option<Vec<u8>>, // Adapter error bytes if success == false
}
```

### D.4 `MelConfig`  Global MEL Configuration


```rust
pub struct MelConfig {
    pub max_gas_per_tx: u64,            // Default: 10_000_000
    pub max_bundle_size: u32,           // Default: 100 (max operations per atomic bundle)
    pub enable_atomic_executions: bool, // Default: true
    pub enable_cross_vm_calls: bool,    // Default: true
    pub timeout_blocks: u32,            // Default: 300 blocks
}
```

### D.5 `AtomicBundle`  Cross-VM Atomic Transaction Bundle


```rust
pub struct AtomicBundle {
    pub id: H256,                   // Unique bundle identifier (Blake2b-256)
    pub transactions: Vec<MelTx>,   // Ordered vector of operations
    pub timeout: u64,               // Deadline in milliseconds
    pub source_vm: VmType,          // Source VM for the first operation
    pub target_vm: VmType,          // Target VM for cross-VM operations
}
```

### D.6 `MelEvent`  Cross-VM Event Structure


```rust
pub struct MelEvent {
    pub emitter: Vec<u8>,           // Address of the emitting contract
    pub topic: Vec<u8>,             // Primary event topic (e.g., keccak256 of event signature)
    pub topics: Vec<Vec<u8>>,       // Indexed parameters (up to 3 for EVM compat)
    pub data: Vec<u8>,              // Non-indexed ABI-encoded data payload
    pub source_vm: VmType,          // VM that emitted the event
    pub target_vms: Vec<VmType>,    // Target VMs for cross-VM event routing
}
```

### D.7 `VmAdapter` Trait  Universal VM Interface


```rust
pub trait VmAdapter {
    fn vm_type(&self) -> VmType;
    fn execute(&mut self, tx: &MelTx) -> Result<ExecutionResult, MelError>;
    fn validate_tx(&self, tx: &MelTx) -> Result<(), MelError>;
    fn estimate_gas(&self, tx: &MelTx) -> Result<u64, MelError>;
    fn get_balance(&self, address: &[u8], asset_id: &H256) -> Result<u128, MelError>;
    fn get_nonce(&self, address: &[u8]) -> Result<u64, MelError>;
    fn get_code_hash(&self, address: &[u8]) -> Result<Option<H256>, MelError>;
}
```

### D.8 `MelError`  Comprehensive Error Enumeration


```rust
pub enum MelError {
    VmNotSupported,                     // Requested VM adapter not available
    InvalidTransaction,                 // Malformed transaction data
    InsufficientBalance,                // Sender lacks funds for gas + value
    InvalidNonce,                       // Nonce mismatch (replay or gap)
    GasLimitExceeded,                   // Gas consumption exceeds budget
    DeadlinePassed,                     // Atomic bundle deadline expired
    CrossVmCallNotAllowed,              // Cross-VM calls disabled in config
    AtomicBundleFailed,                 // Bundle-level failure
    InvalidSignature,                   // Signature verification failed
    VmExecutionFailed(String),          // VM-specific execution error with details
    InternalError,                      // Internal MEL engine error
    AtomicExecutionFailed,              // Atomic execution phase failure
    UnauthorizedAtomicExecution,        // Unauthorized atomic bundle submission
    InvalidAtomicBundle,                // Invalid atomic bundle structure
    AtomicExecutionTimeout,             // Atomic execution exceeded time limit
}
```

### D.9 `CrossVmMessage`  Inter-VM Communication Message


```rust
pub struct CrossVmMessage {
    pub id: H256,                       // Message identifier
    pub source_vm: VmType,              // Originating VM
    pub target_vm: VmType,              // Destination VM
    pub source_address: Vec<u8>,        // Sender contract address
    pub target_address: Vec<u8>,        // Recipient contract address
    pub message_type: MessageType,      // ContractCall | AssetTransfer | StateSync | EventNotification | Response
    pub payload: Vec<u8>,               // Serialized message data
    pub timestamp: u64,                 // Creation timestamp
    pub signature: Vec<u8>,             // Sender signature for authentication
}
```

### D.10 VM Account Structures

#### `EvmAccountInfo` ()
```rust
pub struct EvmAccountInfo {
    pub nonce: u64,                     // Transaction count
    pub balance: [u8; 32],              // U256 big-endian (32 bytes for full EVM compatibility)
    pub code_hash: H256,                // Keccak-256 of deployed bytecode
    pub storage_root: H256,             // Root hash of account's storage trie
}
```

#### `SvmAccount` ()
```rust
pub struct SvmAccount {
    pub pubkey: Vec<u8>,                // 32-byte ed25519 public key
    pub balance: u64,                   // Lamport balance (u64 to match Solana's model)
    pub data: Vec<u8>,                  // Account data blob (up to 10MB)
    pub executable: bool,               // True if this account is a program
    pub owner: Vec<u8>,                 // Program ID that owns this account
    pub rent_exempt: bool,              // True if balance exceeds rent-exempt minimum
    pub is_writable: bool,              // Writable flag for transaction accounts
}
```

#### `PolkaVmAccount` ()
```rust
pub struct PolkaVmAccount {
    pub nonce: u64,                     // Transaction sequence number
    pub balance: u128,                  // Balance (u128 for Substrate compat)
    pub tier: AccountTier,              // Basic | Standard | Premium (gas pricing tier)
    pub storage_root: H256,             // Storage trie root
    pub code_hash: Option<H256>,        // None for EOAs, Some(hash) for contracts
}
```

#### `MoveAccount` ()
```rust
pub struct MoveAccount {
    pub nonce: u64,                     // Sequence number
    pub balance: u128,                  // Balance in smallest denomination
    pub is_initialized: bool,           // False until first transaction
}
```

---

## Appendix E: Gas Calibration Constants & Cross-VM Overhead Matrix

The values below are the normative Gravity Testnet defaults. They are protocol parameters, not implementation-local constants. A governance update MUST publish the new values, activation block, and runtime metadata version before it takes effect.

### E.1 Base Gas Costs per VM

| VM Type | Base Gas Cost | Calibration Factor | Memory Cost Factor | Storage Cost Factor | Bandwidth Cost Factor | Complexity Scaling |
| :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| **EVM** | 21,000 | 100 | 100 | 100 | 100 | 100 |
| **PolkaVM** | 25,000 | 120 | 80 | 90 | 85 | 110 |
| **SVM** | 30,000 | 150 | 120 | 110 | 130 | 140 |
| **Move** | 35,000 | 180 | 90 | 95 | 105 | 160 |
| **CosmWasm** | 32,000 | 130 | 100 | 100 | 100 | 145 |

### E.2 Operation Multipliers

| Operation Type | Multiplier | Description |
| :--- | ---: | :--- |
| `Transfer` | 1× | Simple value transfer between accounts |
| `ContractCall` | 2× | Invoking an existing smart contract |
| `ContractCreate` | 3× | Deploying new contract bytecode |
| `StorageRead` | 1× | Reading from persistent storage |
| `StorageWrite` | 2× | Writing to persistent storage |
| `CrossVmCall` | 5× | Calling a contract on a different VM |
| `AtomicOperation` | 10× | Operations within an atomic cross-VM bundle |

### E.3 Per-VM Instruction Weight Table

Weights are relative cost multipliers per instruction category. Higher values indicate more expensive operations.

| Instruction Category | EVM | PolkaVM | SVM | Move | CosmWasm |
| :--- | ---: | ---: | ---: | ---: | ---: |
| **Arithmetic** (add, sub, mul, div) | 1 | 1 | 2 | 1 | 1 |
| **Comparison** (eq, lt, gt) | 1 | 1 | 2 | 1 | 1 |
| **Memory** (load, store, alloc) | 3 | 2 | 4 | 3 | 3 |
| **Control Flow** (jump, branch, call) | 2 | 1 | 3 | 2 | 2 |
| **Cryptographic** (hash, verify) | 10 | 8 | 12 | 15 | 11 |
| **Storage** (sload, sstore) | 20 | 15 | 25 | 30 | 24 |
| **Network** (send, receive) | 50 | 40 | 60 | 70 | 55 |
| **System** (spawn, kill) | 100 | 80 | 120 | 150 | 110 |
| **Cross-VM** (bridge operations) | 200 | 180 | 250 | 300 | 210 |

### E.4 Cross-VM Overhead Factor Matrix

When executing an atomic bundle that spans two different VMs, an overhead factor is applied to account for the additional cost of state snapshot creation, cross-VM message serialization, and potential rollback coordination. The following matrix shows the pairwise overhead factors:

| Source ↓ \ Target → | EVM | PolkaVM | SVM | Move | CosmWasm |
| :--- | ---: | ---: | ---: | ---: | ---: |
| **EVM** |  | 120 | 150 | 180 | 170 |
| **PolkaVM** | 110 |  | 130 | 160 | 155 |
| **SVM** | 140 | 125 |  | 170 | 165 |
| **Move** | 160 | 140 | 155 |  | 175 |
| **CosmWasm** | 150 | 145 | 160 | 170 |  |. Overhead factors are expressed as percentages of the base operation cost (100 = no overhead).

### E.5 Global Gas Calibration Constants

```
max_gas_per_transaction:      30,000,000    //
gas_price:                    1,000,000,000 // 1 Gwei
enable_dynamic_pricing:       true
pricing_update_interval:      50 blocks
target_gas_usage_per_block:   75%
```

### E.6 Atomic Execution Timeout Constants


```
base_timeout:                 300,000 ms    // 5 minutes base
per_call_factor:              60,000 ms     // +1 minute per contract call
per_million_gas_factor:       1,000 ms      // +1 second per 1M gas
max_timeout:                  1,800,000 ms  // 30 minutes absolute maximum
minimum_time_remaining:       60,000 ms     // 1 minute minimum before deadline
```

The adaptive timeout formula is: $T_{\text{adaptive}} = \min\Big(300{,}000 + |C| \times 60{,}000 + \frac{G_{\text{limit}}}{10^6}\times1{,}000,\; 1{,}800{,}000\Big) \text{ ms}$

where $|C|$ is the number of contract calls in the bundle and $G_{\text{limit}}$ is the gas limit.

### E.7 Gas Estimation per VM for Atomic Bundles

| VM Type | Per-Call Gas Estimate | Notes |
| :--- | ---: | :--- |
| EVM | 21,000 | Matches Ethereum's base transaction cost |
| SVM | 18,000 | Lower due to eBPF efficiency |
| PolkaVM | 15,000 | RISC-V native compilation advantage |
| Move | 12,000 | Optimized bytecode verification |
| CosmWasm | 15,000 | WebAssembly interpretation overhead |
| Cross-VM coordination overhead | +10,000 | Applied once per bundle |

---

## Appendix F: MPC Threshold Signer Protocol Listings

This appendix gives normative pseudocode for the dealerless DKG and FROST-compatible threshold signing profile. It is intentionally language-independent. An implementation MUST use a cryptographically secure random source, authenticated encrypted signer channels, transcript persistence, and durable nonce-state protection.

### F.1 Distributed Key Generation (DKG)

```text
INPUT: threshold t, ordered participant identifiers P = [1..n], epoch E
REJECT unless 2 <= t <= n and all identifiers are unique.

For each participant j in P, independently:
  sample non-zero coefficients a[j,0..t-1] from F_q
  set f_j(X) = sum(a[j,l] * X^l for l in 0..t-1)
  broadcast commitments C[j,l] = a[j,l] * G, bound to ("IL-DKG-V1", E, P, t)
  confidentially send share s[j,i] = f_j(i) to every participant i

For each recipient i:
  verify s[j,i] * G == sum(i^l * C[j,l] for l in 0..t-1) for every j
  lodge a signed complaint for every failed verification

Resolve complaints; exclude an accused participant only according to the epoch's
governance rules. Abort if fewer than t verified contributors remain.
Each i stores x_i = sum(s[j,i]) and Y_i = x_i * G in protected storage.
Publish Y = sum(C[j,0]), the transcript hash, threshold, participant list, and epoch.
```

### F.2 Nonce Commitment Round

```text
INPUT: approved request digest m, epoch E, canonical signing subset T
REJECT if |T| < t, T has duplicates, E is not active, or m is outside policy.

Signer i samples fresh non-zero nonce scalars (d_i, e_i) exactly once.
Signer i records the nonce state as reserved before network transmission.
Signer i sends (i, D_i = d_i*G, E_i = e_i*G) signed for ("IL-FROST-COMMIT-V1", E, T, m).
The coordinator forms one canonical, identifier-sorted commitment list com.
Every signer verifies com includes its own unchanged commitment before producing a share.
```

### F.3 Signature Share and Aggregation

```text
For each i in T:
  rho_i = H_bind("IL-FROST-BIND-V1", E, T, com, m, i)
  R_i = D_i + rho_i * E_i
R = sum(R_i for i in T)
REJECT if R is the identity point.
c = H_chal("IL-FROST-CHAL-V1", E, T, R, Y, m)

Signer i computes lambda_i over the exact canonical set T and returns
  z_i = d_i + rho_i*e_i + lambda_i*x_i*c mod q.
The signer marks (d_i,e_i) consumed and erases its secret nonce scalars.

The coordinator verifies z_i*G == R_i + c*lambda_i*Y_i for every i.
REJECT on any invalid share. Otherwise set z = sum(z_i for i in T) mod q
and output signature (R,z), epoch E, signer identifiers T, and transcript hash.
```

### F.4 Signature Verification

```text
INPUT: (R, z), request digest m, epoch key Y, signer set T, epoch E
REJECT if R is invalid or identity, z is not in [1, q-1], or E/T is not canonical.
c = H_chal("IL-FROST-CHAL-V1", E, T, R, Y, m)
ACCEPT iff z*G == R + c*Y.
```

For a BIP-340-compatible x-only encoding, the aggregate nonce is normalized to even $Y$ before challenge calculation and the scalar is negated when normalization negates $R$. Implementations MUST reject a non-canonical point or scalar rather than attempting ambiguous recovery.

### F.5 Lagrange Coefficient Computation

For signer identifier $i$ in canonical signer set $T$, compute

$$\lambda_i = \prod_{\substack{j \in T \\ j \neq i}} \frac{x_j}{x_j-x_i} \pmod q = \prod_{\substack{j \in T \\ j \neq i}}\frac{-j}{i-j}\pmod q.$$

The signer set MUST contain unique non-zero identifiers, so every denominator is invertible. The coordinator and every signer recompute the coefficient independently; it is never accepted as unverified input.

### F.6 Transcript and Failure Handling

Every DKG and signing transcript includes a protocol domain tag, network identifier, epoch, participant set, threshold, request digest, and canonical serialization version. A signer MUST erase nonce secrets after the first signing attempt and MUST NOT reuse a commitment after a timeout, coordinator failure, or rejected request. Missing commitments, equivocation, invalid shares, and exhausted deadlines produce a failed request; they never produce a partial signature suitable for later reuse.

---

## Appendix G: HotStuff Consensus Protocol Reference

### G.1 Consensus State

Each validator persists the following safety-critical state across restart: `{ validator_set_id, current_view, highest_qc, locked_qc, last_voted_view, finalized_block }`. It also maintains a block store indexed by block hash and parent hash, a QC store indexed by certified block hash, and a bounded pending-message queue. A process MUST write its vote intent durably before transmitting a vote and MUST NOT vote twice in one view.

### G.2 Linked 3-Chain Finalization Rule

```text
on_receive_qc(qc):
  verify validator-set identifier, quorum cardinality, unique signer set,
         domain-separated message, aggregate BLS signature, and certified block
  retrieve certified block b; reject if b is unknown or its parent linkage is invalid
  store qc and update highest_qc if qc.view is greater

  let b2 = b.parent
  let b1 = b2.parent
  if qc(b) exists AND qc(b2) exists AND qc(b1) exists
       AND b.parent == b2.hash AND b2.parent == b1.hash
       AND qc(b1).view < qc(b2).view < qc(b).view:
    finalize b1 and every unfinalized ancestor of b1

on_proposal(p):
  verify p extends its justify-QC block and justify-QC is valid
  vote only if p extends locked_qc.block OR p.justify_qc.view > locked_qc.view
  persist vote intent, then sign and broadcast the domain-separated vote
```

The parent-link conditions are mandatory. Three unrelated QCs at neighbouring views are insufficient for finality.

### G.3 BLS12-381 Quorum Certificate

A quorum certificate is `{ block_hash, view, validator_set_id, aggregate_signature, signers }`. The `signers` collection is canonical, duplicate-free, and identifies exactly the public keys aggregated in the pairing equation. The public profile uses a signature in $G_1$ (48-byte compressed) and a public key in $G_2$ (96-byte compressed):

$$e(\sigma_{\rm agg},g_2)=e\left(H_{G_1}(m),\sum_{i\in Q}Y_i\right).$$

The message $m$ includes `IL-HOTSTUFF-V1`, network identifier, validator-set identifier, phase, view, height, and block hash. A QC with an unknown validator set, stale epoch, missing signer identity, duplicated signer, or signer cardinality below $\lceil2n/3\rceil$ is invalid.

### G.4 Consensus Messages

| Message | Required fields | Acceptance conditions |
| :--- | :--- | :--- |
| Proposal | block, view, parent hash, justify-QC, proposer identity | Correct leader, valid parent, valid justify-QC, valid block transition. |
| Vote | phase, view, block hash, validator-set ID, signer ID, BLS signature | Current active set, one vote per signer/view/phase, valid signature. |
| QC | block hash, view, validator-set ID, aggregate signature, canonical signer set | Valid linked block, quorum cardinality, aggregate pairing verification. |
| Timeout | view, highest QC, signer ID, BLS signature | Valid timed-out view and signature; used for leader recovery only. |

---

## Appendix H: Atomic Execution State Machine Reference

### H.1 `AtomicExecutionState`  Execution Lifecycle Struct


```rust
let mut state = AtomicExecutionState {
    context: context.clone(),               // The AtomicExecutionContext input
    phase: AtomicExecutionPhase::Initialized, // Current phase in the state machine
    source_snapshot: None,                  // Pre-execution snapshot of source VM state
    target_snapshot: None,                  // Pre-execution snapshot of target VM state
    source_gas_used: 0,                     // Gas consumed by source VM operations
    target_gas_used: 0,                     // Gas consumed by target VM operations
    source_events: vec![],                  // Events emitted by source VM
    target_events: vec![],                  // Events emitted by target VM
    rollback_reason: None,                  // Human-readable rollback reason (if any)
};
```

### H.2 Phase Progression Table

The atomic execution engine transitions through five committed-state-safe phases:

| Phase | Enum Variant | Description | On Error |
| :--- | :--- | :--- | :--- |
| **1** | `Initialized` | Validate canonical bundle bytes, signatures, nonces, budgets, and deadline; create one global overlay | Return `InvalidContext` |
| **2** | `SourceExecution` | Execute the ordered prefix of calls in isolated adapter overlays | → Rollback |
| **3** | `TargetExecution` | Execute remaining calls and stage cross-VM messages/events | → Rollback |
| **4** | `Validation` | Check final balances, invariants, gas charge, and all adapter diffs | → Rollback |
| **5** | `Committed` | Apply every staged diff and publish staged messages/events | No transition back to execution |
| *Error* | `Rollback` | Discard all overlays and secret transient state | Return rolled-back result |
| *Error* | `Failed` | Fail closed before any persistent apply | Return error |

### H.3 Rollback Implementation

```text
rollback(state, reason):
  REQUIRE no persistent state write has been applied.
  discard every VM overlay, buffered storage diff, message, event, and fee reservation.
  zeroize unneeded transient payload and signing material.
  record the canonical rollback reason in the receipt only.
  set state.phase = Rollback and return the original committed state root.

If an adapter cannot prove that its changes remain isolated, mark the adapter failed,
abort the enclosing block transition, and do not produce a commit or rollback receipt.
```

### H.4 Validation Rules for Atomic Context

The `AtomicContextValidator::validate_comprehensive()` method enforces the following invariants before execution begins:

1. **Non-zero bundle ID**: `context.bundle_id.is_zero()` → `InvalidContext`
2. **Non-empty addresses**: Both `source_address` and `target_address` must be non-empty
3. **VM capabilities**: Every referenced adapter is enabled, healthy, and compatible with the declared `vm_version`; same-VM bundles are permitted only when `atomic=true` and follow the same overlay rules.
4. **Valid contract calls**: Each call must have non-empty `contract_address` and `method`
5. **Valid state dependencies**: Each dependency must have non-empty `address` and `key`

---

## Appendix I: Glossary of Terms

| Term | Definition |
| :--- | :--- |
| **MEL** | Multi-VM Execution Layer  the orchestration engine coordinating all 5 VM adapters |
| **Atomic Bundle** | A set of contract calls across multiple VMs executed as a single atomic unit |
| **Unified Address** | A canonical 32-byte account identifier mapped to native addresses in all 5 VMs |
| **LiteVerse** | DePIN watcher mesh network monitoring external chains for deposit verification |
| **DePIN** | Decentralized Physical Infrastructure Network |
| **MPC TSS** | Multi-Party Computation Threshold Signature Scheme |
| **DKG** | Distributed Key Generation  the ceremony producing threshold key shares |
| **QC** | Quorum Certificate  an aggregate BLS signature from ≥2/3 validators |
| **3-Chain Rule** | HotStuff finalization rule: a block is finalized when 3 consecutive views have formed QCs |
| **Real-Yield** | Fee-driven (non-inflationary) staking rewards model |
| **SPL Token-2022** | Solana's extended token standard with TLV-encoded extensions |
| **PSP-22/34** | Polkadot Smart Contract standards for fungible (PSP-22) and non-fungible (PSP-34) tokens |
| **BCS** | Binary Canonical Serialization  Move VM's binary encoding format |
| **CU** | Compute Unit  Solana's instruction cost metering unit |
| **secp256k1** | Elliptic curve used for EVM (ECDSA) and MPC (Schnorr) signatures |
| **ed25519** | Edwards curve used for Solana SVM transaction signatures |
| **sr25519** | Schnorrkel/Ristretto255 scheme used for native Substrate account keys |
| **BLS12-381** | Bilinear pairing curve used for HotStuff consensus aggregate signatures |
| **Blake2b-256** | Hash function used for Substrate state root hashing and block header hashing |
| **Keccak-256** | Hash function used for EVM address derivation and storage keys |
| **CRYSTALS-Dilithium** | Lattice-based post-quantum digital signature scheme (NIST PQC standard) |
| **SPHINCS+** | Stateless hash-based post-quantum signature scheme (NIST PQC standard) |
| **Lagrange Interpolation** | Polynomial reconstruction technique used to combine threshold signature shares |
| **Shamir Secret Sharing** | Scheme for splitting a secret into $n$ shares where $t$ are needed to reconstruct |

---

*End of InterLayer 2.0 Formal Specification & Protocol Blueprint  InterLayer 2.0 (Gravity Testnet)*
*Authored by Bharath B R ([@Bharathcoorg](https://forum.polkadot.network/u/Bharathcoorg))*
*Publication Date: August 2026*
*Revision: 2.0-TESTNET-GREY-PAPER-v3*
