# BRIXL Protocol Design Research

> **Objective:** Design a decentralized staking/lending protocol for BRIXL — simple, forkable, Bitcoin-native.
> **Date:** March 2026

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Protocol Landscape Analysis](#2-protocol-landscape-analysis)
3. [Deep Dives: Candidate Protocols](#3-deep-dives-candidate-protocols)
4. [Architecture Comparison Matrix](#4-architecture-comparison-matrix)
5. [Recommended Architecture for BRIXL](#5-recommended-architecture-for-brixl)
6. [Protocol Design Specification](#6-protocol-design-specification)
7. [Implementation Roadmap](#7-implementation-roadmap)
8. [Open Questions & Risks](#8-open-questions--risks)

---

## 1. Executive Summary

BRIXL is a Bitcoin-backed lending protocol where users deposit BTC as collateral and receive stablecoins (USDC/USDT) or fiat. The current implementation is a Next.js landing page with a loan calculator, deployed on Base L2. The goal is to evolve this into a **decentralized protocol** — one that minimizes trust assumptions, is simple to build and audit, and can operate without centralized intermediaries.

After analyzing 8+ protocols across the Bitcoin DeFi landscape, this research recommends a **hybrid architecture** that combines:

- **Liquity V1's simplicity** (Trove-based CDP model, Stability Pool, algorithmic fees) as the smart contract backbone
- **Firefish's non-custodial Bitcoin collateral model** (3-of-3 multisig escrow on Bitcoin L1) for true BTC custody
- **Babylon's staking primitives** (optional yield layer for locked BTC)
- **Deployed on Base L2** (as already planned) for low-cost EVM operations

The result: a protocol where BTC stays on Bitcoin (non-custodial), loan logic runs on Base (cheap + composable), and the system is governance-minimal and immutable.

---

## 2. Protocol Landscape Analysis

### 2.1 Categories of Bitcoin DeFi Protocols

| Category | Examples | Approach |
|----------|----------|----------|
| **EVM Lending (Pool-based)** | Aave, Compound | Pooled liquidity, variable rates, complex governance |
| **EVM Lending (CDP-based)** | Liquity, MakerDAO | Individual vaults, stablecoin minting, simpler |
| **EVM Lending (AMM-based)** | Curve/crvUSD | LLAMMA soft-liquidation, complex math |
| **Bitcoin-Native P2P** | Firefish | Multisig escrow on BTC L1, P2P matching |
| **Bitcoin-Native DLC** | Lava Loans, Liquidium, Lygos | Discreet Log Contracts, oracle-dependent |
| **Bitcoin Sidechain** | Sovryn Zero (RSK) | Liquity fork on Bitcoin sidechain |
| **Bitcoin Liquid Staking** | Babylon, Lombard | Stake BTC for PoS security, earn yield |

### 2.2 What BRIXL Needs

Based on the existing PRD and product design:

- **Non-custodial BTC collateral** — no rehypothecation, user retains keys
- **Stablecoin disbursement** — USDC on Base (already planned)
- **Three LTV tiers** — SAVER (30%), STANDARD (50%), FLEX (65%)
- **Liquidation engine** — margin calls at 65/72/77%, liquidation at 80%
- **Simplicity** — minimal governance, auditable, forkable
- **Regulatory compatibility** — MiCA-friendly, KYC integration possible

---

## 3. Deep Dives: Candidate Protocols

### 3.1 Liquity V1 — The Simplest CDP Protocol

**Why it matters for BRIXL:** Liquity V1 is the gold standard for simple, forkable lending protocols. It has been forked 35+ times with aggregate $1B+ TVL across forks.

**Architecture:**
```
┌─────────────────────────────────────────────────┐
│                   Liquity V1                     │
├─────────────────────────────────────────────────┤
│                                                  │
│  User ──→ BorrowerOperations.sol                │
│            │  - openTrove()                      │
│            │  - adjustTrove()                    │
│            │  - closeTrove()                     │
│            ▼                                     │
│         TroveManager.sol                         │
│            │  - liquidate()                      │
│            │  - redeemCollateral()               │
│            ▼                                     │
│         StabilityPool.sol                        │
│            │  - provideToSP()                    │
│            │  - withdrawFromSP()                 │
│            ▼                                     │
│         PriceFeed.sol (Chainlink + Tellor)       │
│                                                  │
│  Tokens: LUSD (stablecoin), LQTY (rewards)      │
└─────────────────────────────────────────────────┘
```

**Key Design Decisions:**
- **110% minimum collateralization** — extremely capital efficient
- **No interest rate** — one-time borrowing fee (0.5-5%, algorithmically adjusted)
- **No governance** — fully immutable after deployment
- **Stability Pool** — depositors absorb liquidated debt, receive collateral at discount
- **Redemption mechanism** — anyone can swap LUSD → ETH at face value, maintaining peg

**What to borrow from Liquity:**
- Trove/vault model for individual loan positions
- Stability Pool concept for liquidation absorption
- Algorithmic fee model (no ongoing interest = simpler)
- Immutable, governance-free deployment
- Price feed with fallback oracle

**What to change:**
- Replace ETH collateral with wrapped BTC representation (or bridge to Bitcoin multisig)
- Adjust MCR from 110% to match BRIXL tiers (130-200% depending on tier, inverse of LTV)
- Replace LUSD minting with USDC lending from a pool
- Add KYC gating layer (optional, at application level not protocol level)

**Source:** [Liquity V1 GitHub](https://github.com/liquity/dev) | [Liquity Docs](https://docs.liquity.org/liquity-v1) | License: GPL v3

---

### 3.2 Firefish — Non-Custodial Bitcoin Collateral

**Why it matters for BRIXL:** Firefish solves the hardest problem — keeping BTC on Bitcoin L1 while enabling lending. Their multisig escrow model is production-proven and MiCA-compliant.

**Architecture:**
```
┌────────────────────────────────────────────────────────────┐
│                    Firefish Protocol                        │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  1. Borrower sends BTC to PREFUND ADDRESS                  │
│     (3-of-3 multisig: Borrower + PriceOracle + PayOracle)  │
│     (+ 7-day timelock fallback to Borrower alone)          │
│                                                             │
│  2. Oracles sign ESCROW TX + all CLOSING TXs               │
│     (repayment TX, default TX, liquidation TX)             │
│                                                             │
│  3. Borrower VERIFIES all pre-signed transactions          │
│     Then signs escrow TX and DISCARDS escrow private key   │
│                                                             │
│  4. Escrow TX broadcast — BTC locked on Bitcoin L1         │
│     Can ONLY be spent via pre-signed closing TXs           │
│                                                             │
│  5. Loan resolution:                                        │
│     - Repaid → PaymentOracle signs repayment TX            │
│     - Default → PaymentOracle signs default TX             │
│     - Price drop → PriceOracle signs liquidation TX        │
│                                                             │
│  Crypto: PSBT + MuSig2 signatures                          │
│  Audit: Ackee Blockchain                                    │
│  Compliance: MiCA (ESMA registered)                         │
└────────────────────────────────────────────────────────────┘
```

**Key Design Decisions:**
- **Bitcoin-native** — no sidechains, no wrapping, no bridges
- **Deterministic escrow** — once locked, BTC can ONLY go to borrower OR lender/liquidator
- **Key destruction** — borrower discards escrow key after signing, preventing unauthorized spending
- **P2P marketplace** — lenders and borrowers matched on platform
- **Oracle-dependent** — PriceOracle and PaymentOracle currently operated by Firefish

**What to borrow from Firefish:**
- The 3-of-3 multisig escrow model for BTC custody
- PSBT + MuSig2 for transaction construction
- Key destruction pattern for escrow security
- The prefund → escrow → closing transaction lifecycle
- MiCA compliance approach

**What to change:**
- Decentralize the oracles (currently centralized to Firefish)
- Replace P2P matching with pooled lending (Stability Pool / USDC pool)
- Add on-chain loan registry on Base for transparency
- Automate margin calls using Chainlink price feeds

**License Warning:** Firefish's code ([GitHub](https://github.com/Firefish-io/firefish-protocol)) is **source-available but NOT open-source**. Their proprietary license explicitly prohibits forking to build competing products, commercial use, modification, and redistribution. **We cannot fork their code — we can only study their architecture and independently implement similar patterns (multisig escrow, PSBT, MuSig2).** These are standard Bitcoin cryptographic primitives, not patented inventions.

**Source:** [Firefish Protocol Docs](https://docs.firefish.io/firefish-protocol) | [Firefish.io](https://firefish.io/) | [Firefish GitHub](https://github.com/Firefish-io/firefish-protocol)

---

### 3.3 Curve/crvUSD LLAMMA — Soft Liquidation Innovation

**Why it matters for BRIXL:** Curve's LLAMMA introduces "soft liquidation" — gradually converting collateral to stablecoins as price drops, and buying it back on recovery. This could eliminate the brutal 80% LTV hard liquidation.

**Architecture:**
```
┌──────────────────────────────────────────────────┐
│              Curve Lending (LLAMMA)                │
├──────────────────────────────────────────────────┤
│                                                    │
│  Vault (ERC-4626)                                 │
│    │  - Lenders deposit USDC/crvUSD               │
│    │  - Earn interest from borrowers               │
│    ▼                                               │
│  Controller                                        │
│    │  - create_loan(collateral, debt, N_bands)    │
│    │  - repay() / add_collateral()                │
│    ▼                                               │
│  LLAMMA (AMM)                                      │
│    │  - Collateral split across N price bands      │
│    │  - As price drops: collateral → stablecoin   │
│    │  - As price rises: stablecoin → collateral   │
│    │  - Arbitrageurs keep bands balanced           │
│    ▼                                               │
│  Oracle (Curve pool price_oracle())               │
│                                                    │
│  Written in: Vyper                                 │
│  Key innovation: No sudden liquidation             │
└──────────────────────────────────────────────────┘
```

**What to borrow from Curve:**
- Soft liquidation concept (gradual de-risking instead of sudden liquidation)
- ERC-4626 vault standard for lender deposits
- Permissionless market creation pattern

**What NOT to borrow:**
- Vyper codebase (BRIXL stack is Solidity)
- Complex AMM band math (adds significant complexity)
- Dependency on Curve pool oracles (BRIXL uses Chainlink)

**Yield Basis (by Curve founder, Sept 2025):** A companion protocol that eliminates impermanent loss for BTC liquidity provision using 2x leveraged liquidity. Pairs BTC with crvUSD, received $60M crvUSD credit line from Curve DAO, had $130M+ in BTC deposits by Dec 2025. Demonstrates that Curve's architecture is being actively adapted for Bitcoin yield. Worth studying for BRIXL Phase 3 yield features.

**Verdict:** The soft liquidation concept is brilliant but adds too much complexity for a v1. The LLAMMA AMM math is non-trivial and requires deep expertise. **Consider for BRIXL v2** — start with simple hard liquidation (like Liquity) for v1.

**License Note:** Curve DAO contracts are MIT-licensed, but Curve recently (March 2026) took enforcement action against PancakeSwap for code copying. Individual repos may carry different licenses — always verify before forking. The LLAMMA/crvUSD contracts need individual license verification.

**Source:** [Curve Lending Docs](https://docs.curve.finance/lending/overview/) | [MixBytes LLAMMA Analysis](https://mixbytes.io/blog/modern-defi-lending-protocols-how-its-made-curve-llamalend) | [Llama Risk Primer](https://www.llamarisk.com/research/curve-lending) | [Yield Basis](https://blockworks.co/news/michael-egorov-yield-basis-protocol)

---

### 3.4 Sovryn Zero — Liquity Fork on Bitcoin Sidechain

**Why it matters for BRIXL:** Sovryn Zero is literally a Liquity fork adapted for Bitcoin. It proves the Liquity model works for BTC-collateralized lending.

**Architecture:**
- Built on RSK (Rootstock) — Bitcoin sidechain, merged-mined with BTC
- Uses RBTC (RSK's native BTC) as collateral
- Mints ZUSD stablecoin (like LUSD)
- 0% ongoing interest, one-time origination fee
- Lines of Credit (LoC) = Troves
- Stability Pool for liquidation absorption
- SOV governance token for fee sharing

**What to borrow from Sovryn:**
- Validation that Liquity model works for BTC lending
- Their adaptations for Bitcoin-specific considerations
- Open-source codebase with 4 security audits (Trail of Bits x2, Coinspect, Chainsulting)

**What to change:**
- Deploy on Base instead of RSK (better ecosystem, more composable)
- Use actual BTC custody (Firefish model) instead of sidechain BTC
- USDC instead of minting a new stablecoin

**Source:** [Sovryn Zero](https://sovryn.com/zero) | [Sovryn Wiki](https://wiki.sovryn.com/en/sovryn-dapp/subprotocols/zero-zusd) | [GitHub](https://github.com/DistributedCollective)

---

### 3.5 Babylon — Bitcoin Staking for Yield

**Why it matters for BRIXL:** If BRIXL locks BTC as collateral, that BTC is idle. Babylon enables locked BTC to earn yield by providing security to PoS chains — without moving the BTC off Bitcoin L1.

**Architecture:**
```
┌──────────────────────────────────────────────────┐
│                Babylon Protocol                    │
├──────────────────────────────────────────────────┤
│                                                    │
│  1. BTC locked in Taproot output on Bitcoin L1    │
│  2. UTXO delegated to Finality Providers          │
│  3. FPs sign blocks on PoS chains (BSNs)          │
│  4. Slashing via EOTS (key exposed if double-sign)│
│  5. Unbonding: 301 blocks (~50 hours)             │
│                                                    │
│  Key properties:                                   │
│    - Self-custodial (BTC never leaves Bitcoin)     │
│    - Partial slashing (0.1% of stake)             │
│    - Multi-staking across multiple BSNs            │
│    - BABY token for governance + gas               │
│                                                    │
│  Built with: Cosmos SDK                            │
│  Yield source: PoS security rewards               │
└──────────────────────────────────────────────────┘
```

**Relevance to BRIXL:**
- BRIXL collateral BTC could be simultaneously staked via Babylon
- This would generate yield on idle collateral, reducing effective borrowing cost
- Lombard Finance (LBTC) already does this with $2B TVL

**Integration approach:**
- Phase 1: No Babylon integration (keep it simple)
- Phase 2: Allow BRIXL collateral to be staked via Babylon through Lombard (LBTC)
- Phase 3: Direct Babylon staking integration

**Source:** [Babylon Labs](https://babylonlabs.io/) | [Babylon Docs](https://docs.babylonlabs.io/guides/overview/) | [Lombard Architecture](https://docs.lombard.finance/technical-documentation/protocol-architecture)

---

### 3.6 DLCs (Discreet Log Contracts) — Bitcoin-Native Smart Contracts

**Why it matters for BRIXL:** DLCs enable conditional Bitcoin transactions without wrapping or bridging. They're the most "Bitcoin-native" approach to lending.

**How DLCs work:**
```
┌──────────────────────────────────────────────────┐
│           DLC-Based Lending Flow                   │
├──────────────────────────────────────────────────┤
│                                                    │
│  1. Borrower + Lender create 2-of-2 multisig      │
│  2. Pre-sign ALL possible outcome transactions:    │
│     - Repayment TX (BTC → Borrower)               │
│     - Default TX (BTC → Lender)                    │
│     - Liquidation TXs (for each price level)       │
│  3. Oracle publishes signed price attestation       │
│  4. Correct outcome TX becomes spendable           │
│                                                    │
│  Protocols using DLCs:                             │
│    - Lava Loans (trustless BTC collateral)         │
│    - Liquidium (Ordinals/Runes lending)            │
│    - Lygos Finance (Atomic Finance acquisition)    │
│    - Cadena Bitcoin                                │
│                                                    │
│  Pros: Fully Bitcoin-native, no bridges            │
│  Cons: Oracle dependency, limited composability     │
│        Pre-signing all outcomes = complexity        │
└──────────────────────────────────────────────────┘
```

**Verdict:** DLCs are elegant but add complexity for BRIXL v1. The need to pre-sign transactions for every possible price outcome creates combinatorial explosion. **Consider for BRIXL v2/v3** as Bitcoin scripting matures.

**Source:** [DLC Wiki](https://dlc.wiki/) | [Bitcoin Optech DLC](https://bitcoinops.org/en/topics/discreet-log-contracts/) | [Lava Loans](https://bitcoinmagazine.com/technical/lava-loans-protocol-v2-dlc-based-bitcoin-collateralized-loans)

---

## 4. Architecture Comparison Matrix

| Criteria | Liquity V1 | Firefish | Curve LLAMMA | Sovryn Zero | Babylon | DLCs |
|----------|-----------|----------|-------------|-------------|---------|------|
| **Simplicity** | ★★★★★ | ★★★★ | ★★ | ★★★★ | ★★★ | ★★★ |
| **BTC Native Custody** | ✗ (ETH) | ★★★★★ | ✗ | ★★★ (RSK) | ★★★★★ | ★★★★★ |
| **Forkability** | ★★★★★ | ★★★ | ★★ | ★★★★ | ★★ | ★★ |
| **Battle-tested** | ★★★★★ | ★★★ | ★★★★ | ★★★ | ★★★ | ★★ |
| **Decentralization** | ★★★★★ | ★★★ | ★★★★ | ★★★★ | ★★★★ | ★★★★ |
| **Liquidation Safety** | ★★★ | ★★★ | ★★★★★ | ★★★ | N/A | ★★★ |
| **Composability (DeFi)** | ★★★★ | ★★ | ★★★★★ | ★★ | ★★★ | ★ |
| **Regulatory Fit** | ★★★ | ★★★★★ | ★★★ | ★★★ | ★★★ | ★★★ |
| **Open Source** | GPL v3 | Partial | Vyper/MIT | GPL | Apache 2.0 | Various |
| **Audit Count** | 3+ | 1 (Ackee) | 3+ | 4 | 2+ | Varies |

---

## 5. Recommended Architecture for BRIXL

### 5.1 The Hybrid Approach

```
┌─────────────────────────────────────────────────────────────────┐
│                    BRIXL PROTOCOL ARCHITECTURE                   │
│                     "Simple. Secure. Sovereign."                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  LAYER 1: BITCOIN (Collateral Custody)                          │
│  ┌───────────────────────────────────────┐                      │
│  │  Firefish-style Multisig Escrow       │                      │
│  │  • 3-of-3: Borrower + Oracle + BRIXL  │                      │
│  │  • PSBT + MuSig2                      │                      │
│  │  • Key destruction after escrow       │                      │
│  │  • 7-day timelock safety fallback     │                      │
│  │  • BTC never leaves Bitcoin L1        │                      │
│  └──────────────┬────────────────────────┘                      │
│                 │ Proof of lock                                   │
│                 ▼                                                 │
│  LAYER 2: BASE (Loan Logic + Liquidity)                         │
│  ┌───────────────────────────────────────┐                      │
│  │                                       │                      │
│  │  BrixlVault.sol (Liquity-inspired)    │                      │
│  │  • openLoan() — create position       │                      │
│  │  • adjustLoan() — modify collateral   │                      │
│  │  • closeLoan() — repay + release BTC  │                      │
│  │  • Tier logic: SAVER/STANDARD/FLEX    │                      │
│  │                                       │                      │
│  │  BrixlPool.sol (Stability Pool)       │                      │
│  │  • USDC deposits from lenders         │                      │
│  │  • Absorbs liquidated positions       │                      │
│  │  • Lenders earn liquidation gains     │                      │
│  │                                       │                      │
│  │  BrixlLiquidator.sol                  │                      │
│  │  • Chainlink BTC/USD price feed       │                      │
│  │  • Margin calls at 65/72/77% LTV     │                      │
│  │  • Hard liquidation at 80% LTV        │                      │
│  │  • 30-min grace period                │                      │
│  │  • 4% fee (3% protocol, 1% caller)   │                      │
│  │                                       │                      │
│  │  BrixlOracle.sol                      │                      │
│  │  • Chainlink primary                  │                      │
│  │  • Pyth Network fallback              │                      │
│  │  • Staleness check (1hr max)          │                      │
│  │                                       │                      │
│  └───────────────────────────────────────┘                      │
│                                                                  │
│  CROSS-LAYER BRIDGE:                                            │
│  ┌───────────────────────────────────────┐                      │
│  │  BTC Lock Verifier                    │                      │
│  │  • Verifies BTC escrow on Bitcoin     │                      │
│  │  • Relayer submits proof to Base      │                      │
│  │  • Unlocks USDC lending on Base       │                      │
│  │  • Options:                           │                      │
│  │    a) Simple relayer + multisig       │                      │
│  │    b) Bitcoin light client on Base    │                      │
│  │    c) tBTC-style threshold signing    │                      │
│  └───────────────────────────────────────┘                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 5.2 Why This Architecture

| Decision | Rationale |
|----------|-----------|
| **Liquity's Trove model** | Simplest battle-tested CDP system. 35+ successful forks. GPL v3 licensed. |
| **Firefish's BTC escrow** | Production-proven non-custodial BTC custody. MiCA compliant. No wrapping needed. |
| **Base L2** | Already in BRIXL's stack. Low gas. EVM composable. Growing ecosystem. |
| **USDC (not minting stablecoin)** | Avoids stablecoin bootstrapping problem. Immediate liquidity. Regulatory clarity. |
| **Stability Pool** | Decentralized liquidation absorption. Lenders earn yield. No centralized liquidator needed. |
| **No governance token (v1)** | Reduces complexity. Avoids securities concerns. Can add later if needed. |
| **Chainlink + Pyth oracles** | Industry standard. Redundant. Both available on Base. |

### 5.3 Simplification Decisions

Things we're **intentionally NOT doing** in v1:

1. **No custom stablecoin** — Use USDC, don't mint BRIXL-USD
2. **No AMM/LLAMMA** — Hard liquidation is simpler and well-understood
3. **No DLCs** — Too early, limited tooling
4. **No Babylon staking** — Keep collateral idle for now, add yield later
5. **No governance** — Immutable parameters, upgradeable via proxy only for critical fixes
6. **No cross-chain** — Base only for v1
7. **No rehypothecation** — Collateral is locked, period

---

## 6. Protocol Design Specification

### 6.1 Smart Contract Architecture

```
contracts/
├── core/
│   ├── BrixlVault.sol          # Loan position management (inspired by BorrowerOperations.sol)
│   ├── BrixlLoanManager.sol    # Loan state machine (inspired by TroveManager.sol)
│   ├── BrixlPool.sol           # USDC lending pool (inspired by StabilityPool.sol)
│   └── BrixlLiquidator.sol     # Liquidation engine
├── oracles/
│   ├── BrixlPriceFeed.sol      # Chainlink + Pyth fallback
│   └── BrixlBTCVerifier.sol    # Bitcoin escrow verification
├── periphery/
│   ├── BrixlRouter.sol         # User-facing entry point
│   └── BrixlFeeCollector.sol   # Protocol fee management
└── interfaces/
    ├── IBrixlVault.sol
    ├── IBrixlPool.sol
    └── IBrixlPriceFeed.sol
```

### 6.2 Core Data Structures

```solidity
// Loan position (equivalent to Liquity's Trove)
struct Loan {
    address borrower;           // Wallet address
    uint256 btcCollateralSats;  // BTC amount in satoshis
    uint256 debtUSDC;           // USDC borrowed (6 decimals)
    uint256 btcPriceAtOpen;     // BTC/USD price at origination
    uint256 liquidationPrice;   // Price at which loan is liquidated
    uint8 tier;                 // 0=SAVER, 1=STANDARD, 2=FLEX
    uint16 aprBps;              // APR in basis points (500, 750, 900-1200)
    uint32 termEnd;             // Unix timestamp of term end
    bytes32 btcEscrowTxId;      // Bitcoin escrow transaction ID
    LoanStatus status;          // PENDING, ACTIVE, REPAID, LIQUIDATED
}

enum LoanStatus { PENDING, ACTIVE, MARGIN_CALL, LIQUIDATED, REPAID, DEFAULTED }

// Tier configuration
struct TierConfig {
    uint16 maxLtvBps;           // Max LTV in basis points (3000, 5000, 6500)
    uint16 aprBps;              // APR in basis points
    uint16 liquidationLtvBps;   // Liquidation threshold (8000 = 80%)
    uint16 marginCall1Bps;      // First margin call (6500 = 65%)
    uint16 marginCall2Bps;      // Second margin call (7200 = 72%)
    uint16 marginCall3Bps;      // Third margin call (7700 = 77%)
}
```

### 6.3 Loan Lifecycle

```
                    ┌─────────┐
                    │ PENDING │ ← Loan created, awaiting BTC escrow proof
                    └────┬────┘
                         │ BTC escrow verified on Bitcoin
                         ▼
                    ┌─────────┐
              ┌────│ ACTIVE  │────┐
              │    └────┬────┘    │
              │         │         │
        Price drops    │    Borrower repays
              │         │         │
              ▼         │         ▼
     ┌──────────────┐   │   ┌─────────┐
     │ MARGIN_CALL  │   │   │ REPAID  │ → BTC released from escrow
     │ (65/72/77%)  │   │   └─────────┘
     └──────┬───────┘   │
            │           │
    LTV hits 80%   Term expires without repayment
            │           │
            ▼           ▼
     ┌──────────────┐  ┌───────────┐
     │ LIQUIDATED   │  │ DEFAULTED │ → BTC sent to liquidator/lender
     └──────────────┘  └───────────┘
```

### 6.4 Stability Pool (USDC Lending Pool)

Inspired by Liquity's Stability Pool, this is where USDC lenders deposit funds:

```
Lender deposits 10,000 USDC to BrixlPool
    → Pool now has 10,000 USDC available for loans

Borrower opens STANDARD loan:
    → Locks 1 BTC in Firefish-style escrow on Bitcoin
    → BrixlVault verifies escrow proof
    → BrixlPool disburses 30,000 USDC to borrower (at 50% LTV, BTC=$60k)

Borrower repays 30,000 USDC + interest:
    → Interest distributed pro-rata to pool depositors
    → BTC escrow released back to borrower

If liquidation occurs:
    → Pool absorbs the debt (USDC burned from pool)
    → Pool depositors receive BTC collateral at discount
    → Net gain for depositors (BTC worth more than debt absorbed)
```

### 6.5 Bitcoin Escrow Verification

The bridge between Bitcoin L1 and Base L2:

**Option A: Relayer + Committee (Simplest, v1)**
```
1. Borrower locks BTC in 3-of-3 multisig on Bitcoin
2. BRIXL Relayer monitors Bitcoin for escrow transactions
3. Relayer submits proof (txid, amount, escrow address) to Base
4. Committee of 3-5 signers validates the proof
5. BrixlVault accepts the proof and activates the loan
```

**Option B: Bitcoin Light Client (More decentralized, v2)**
```
1. Deploy Bitcoin SPV light client on Base
2. Submit Bitcoin block headers to verify escrow TX inclusion
3. Merkle proof validates the specific escrow transaction
4. Fully trustless verification, no committee needed
```

**Option C: tBTC-style Threshold (Most decentralized, v3)**
```
1. Threshold network of signers (like Keep Network/tBTC v2)
2. Random selection of signers for each escrow
3. Staked collateral from signers ensures honest behavior
4. Most decentralized but most complex to implement
```

**Recommendation:** Start with Option A (relayer + small committee), migrate to Option B as the protocol matures.

### 6.6 Oracle Design

```solidity
contract BrixlPriceFeed {
    // Primary: Chainlink BTC/USD on Base
    AggregatorV3Interface public chainlinkFeed;

    // Fallback: Pyth Network BTC/USD
    IPyth public pythFeed;

    // Staleness threshold: 1 hour
    uint256 public constant MAX_PRICE_AGE = 3600;

    function getPrice() external view returns (uint256 price, bool isStale) {
        // Try Chainlink first
        (, int256 answer, , uint256 updatedAt, ) = chainlinkFeed.latestRoundData();

        if (block.timestamp - updatedAt <= MAX_PRICE_AGE && answer > 0) {
            return (uint256(answer), false);
        }

        // Fallback to Pyth
        PythStructs.Price memory pythPrice = pythFeed.getPrice(BTC_USD_PRICE_ID);
        if (block.timestamp - pythPrice.publishTime <= MAX_PRICE_AGE) {
            return (convertPythPrice(pythPrice), false);
        }

        // Both stale — return last known + flag
        return (uint256(answer), true);
    }
}
```

### 6.7 Fee Structure

| Fee | Amount | Recipient |
|-----|--------|-----------|
| Origination fee | 0.75% of loan | Protocol treasury |
| Liquidation fee | 4% total (3% protocol, 1% liquidation caller) | Split |
| Early repayment | 0% (no penalty) | — |
| Interest (SAVER) | 5.0% APR | Pool depositors |
| Interest (STANDARD) | 7.5% APR | Pool depositors |
| Interest (FLEX) | 9-12% APR | Pool depositors |

---

## 7. Implementation Roadmap

### Phase 1: Core Protocol (Weeks 1-6)

**Smart Contracts:**
1. `BrixlPriceFeed.sol` — Chainlink + Pyth oracle with staleness checks
2. `BrixlPool.sol` — USDC deposit/withdraw, share tracking (ERC-4626)
3. `BrixlVault.sol` — Loan creation, modification, repayment
4. `BrixlLoanManager.sol` — Loan state machine, tier logic
5. `BrixlLiquidator.sol` — Margin calls, liquidation execution

**Bitcoin Layer:**
6. Escrow address generation (PSBT construction)
7. Relayer service for monitoring Bitcoin escrow TXs
8. Basic committee verification (3-of-5 multisig on Base)

**Testing:**
9. Unit tests for all contracts (Hardhat/Foundry)
10. Integration tests with forked Base
11. Testnet deployment (Base Sepolia)

### Phase 2: Production Hardening (Weeks 7-10)

12. Security audit (1-2 firms)
13. Bug bounty program (Immunefi)
14. Frontend integration (connect to existing Next.js app)
15. KYC integration at application layer (not protocol layer)
16. Mainnet deployment (Base)

### Phase 3: Decentralization & Yield (Weeks 11-16)

17. Bitcoin SPV light client on Base (replace committee)
18. Babylon staking integration for idle collateral
19. Additional collateral types (ETH, stETH)
20. Governance framework (if needed)

---

## 8. Open Questions & Risks

### 8.1 Critical Design Questions

| # | Question | Options | Recommendation |
|---|----------|---------|----------------|
| 1 | **How to verify BTC escrow on Base?** | Relayer + committee / SPV light client / tBTC threshold | Start with relayer + committee (simplest) |
| 2 | **Mint stablecoin or use USDC?** | Mint BRIXL-USD / Use USDC from pool | Use USDC (avoids peg risk, regulatory burden) |
| 3 | **Who provides liquidity?** | Protocol treasury / Public pool / P2P matching | Public USDC pool (Stability Pool model) |
| 4 | **How to handle BTC price feed cross-chain?** | Chainlink on Base / Bitcoin oracle + relay | Chainlink on Base (already there) |
| 5 | **Governance or immutable?** | Immutable / Upgradeable proxy / DAO | Upgradeable proxy with timelock (compromise) |
| 6 | **KYC at protocol or app level?** | On-chain KYC gate / Off-chain app layer | App layer (protocol stays permissionless) |
| 7 | **Interest model?** | Fixed per tier / Utilization-based / Algorithmic | Fixed per tier (simpler, predictable) |

### 8.2 Technical Risks

| Risk | Severity | Mitigation |
|------|----------|------------|
| BTC escrow verification is centralized (relayer) | High | Migrate to SPV light client in Phase 3 |
| Oracle manipulation (Chainlink) | Medium | Dual oracle (Chainlink + Pyth), staleness checks, TWAP |
| Smart contract bugs | High | Multiple audits, formal verification, bug bounty |
| Bitcoin script limitations | Medium | Use well-tested PSBT patterns (Firefish-proven) |
| Bridge risk (BTC ↔ Base) | High | No actual bridge — BTC stays on Bitcoin, only proof crosses |
| Liquidity bootstrapping | Medium | Seed pool from protocol treasury, offer competitive APY |
| Regulatory risk (lending) | Medium | KYC at app layer, MiCA compliance, legal review |

### 8.3 Competitive Positioning

```
                    Decentralization
                         ▲
                         │
         Babylon ●       │       ● Aave
                         │
    Firefish ●           │           ● Compound
                         │
              BRIXL ◆────┼───────────────────▶ Simplicity
                (target) │
         Sovryn ●        │       ● Curve/LLAMMA
                         │
       DLC Loans ●       │   ● MakerDAO
                         │
```

BRIXL targets the sweet spot: **more decentralized than Firefish** (adding Stability Pool + on-chain logic), **simpler than Aave/Curve** (fixed rates, no governance, single collateral), and **more Bitcoin-native than EVM lending** (BTC stays on Bitcoin L1).

---

## Appendix A: Key Source Code References

### Liquity V1 (Primary Fork Target)
- **Repository:** https://github.com/liquity/dev
- **Key contracts:** `BorrowerOperations.sol`, `TroveManager.sol`, `StabilityPool.sol`, `PriceFeed.sol`
- **License:** GPL v3 (fully forkable)
- **Audit reports:** Trail of Bits, Coinspect

### Firefish (BTC Custody Model)
- **Protocol docs:** https://docs.firefish.io/firefish-protocol
- **Client code:** https://protocol.firefish.io
- **Audit:** Ackee Blockchain

### Sovryn Zero (Liquity Fork for BTC)
- **Repository:** https://github.com/DistributedCollective
- **Wiki:** https://wiki.sovryn.com/en/sovryn-dapp/subprotocols/zero-zusd
- **Audits:** Trail of Bits x2, Coinspect, Chainsulting

### Curve Lending (Soft Liquidation Reference)
- **Docs:** https://docs.curve.finance/lending/overview/
- **Key innovation:** LLAMMA band-based soft liquidation

---

## Appendix B: Glossary

| Term | Definition |
|------|-----------|
| **CDP** | Collateralized Debt Position — a loan backed by locked collateral |
| **LTV** | Loan-to-Value ratio — loan amount / collateral value |
| **MCR** | Minimum Collateralization Ratio — inverse of max LTV |
| **Trove** | Liquity term for an individual CDP |
| **Stability Pool** | Pool of stablecoins that absorbs liquidated debt |
| **PSBT** | Partially Signed Bitcoin Transaction |
| **MuSig2** | Multi-signature scheme for Bitcoin |
| **DLC** | Discreet Log Contract — conditional BTC transaction using oracle attestations |
| **EOTS** | Extractable One-Time Signature — Babylon's slashing mechanism |
| **LLAMMA** | Lending-Liquidating AMM Algorithm — Curve's soft liquidation system |
| **ERC-4626** | Tokenized vault standard for yield-bearing tokens |
| **SPV** | Simplified Payment Verification — lightweight Bitcoin block validation |

---

*This research document is a living artifact. It will be updated as protocol design decisions are finalized and implementation progresses.*
