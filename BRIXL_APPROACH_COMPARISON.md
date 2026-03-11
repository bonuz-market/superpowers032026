# BRIXL Approach Comparison: v1 (Smart Contracts) vs v2 (Simplified)

> **Date:** March 2026
> **Purpose:** Side-by-side comparison of the two architectures researched for BRIXL.

---

## Executive Summary

| | **v1: Custom Smart Contracts** | **v2: Off-Chain + Bitcoin Escrow** |
|---|---|---|
| **Philosophy** | Trustless, on-chain, decentralized from day 1 | Ship fast, prove the market, decentralize later |
| **Inspiration** | Liquity V1 fork + Firefish escrow | Firefish operating model |
| **Smart contracts** | 5+ Solidity contracts on Ethereum L1 | 0 smart contracts |
| **BTC custody** | Multisig escrow on Bitcoin L1 | Multisig escrow on Bitcoin L1 (same) |
| **Loan logic** | On-chain (BrixlVault.sol, BrixlLoanManager.sol) | Off-chain (Next.js API + PostgreSQL) |
| **Liquidation** | On-chain keeper bots calling BrixlLiquidator.sol | Off-chain cron job monitoring prices |
| **USDC source** | On-chain Stability Pool (BrixlPool.sol) | Treasury wallet (Phase 2: lender pool) |
| **Time to MVP** | 3-6 months | 4-6 weeks |
| **Audit cost** | $200K-500K | $20-50K (backend security review) |
| **Recommendation** | Phase 4+ (when scale demands trustlessness) | **v1 product (ship now)** |

---

## 1. Architecture Comparison

### v1: Smart Contract Architecture (Previous Research)

```
┌──────────────────────────────────────────────┐
│             ETHEREUM L1                       │
│                                               │
│  BrixlVault.sol ←──── Liquity BorrowerOps    │
│  BrixlLoanManager.sol ←── Liquity TroveManager│
│  BrixlPool.sol ←──── Liquity StabilityPool   │
│  BrixlLiquidator.sol ←── Custom              │
│  BrixlPriceFeed.sol ←── Chainlink + Pyth     │
│  BrixlBTCVerifier.sol ←── BTC proof relay    │
│  BrixlRouter.sol ←── User entry point        │
│  BrixlFeeCollector.sol ←── Fee mgmt          │
│                                               │
│  + Interfaces, libraries, test suite          │
├──────────────────────────────────────────────┤
│             BITCOIN L1                        │
│  3-of-3 Multisig Escrow (PSBT + MuSig2)     │
├──────────────────────────────────────────────┤
│             CROSS-LAYER                       │
│  Relayer + Committee (v1)                    │
│  → SPV Light Client (v2)                     │
│  → tBTC Threshold (v3)                       │
├──────────────────────────────────────────────┤
│             APPLICATION                       │
│  Next.js Frontend + KYC + Wallet Connect     │
└──────────────────────────────────────────────┘
```

### v2: Simplified Architecture (Current Recommendation)

```
┌──────────────────────────────────────────────┐
│             BITCOIN L1                        │
│  3-of-3 Multisig Escrow (PSBT + MuSig2)     │
│  Pre-signed closing TXs                      │
│  (Same escrow model as v1)                   │
├──────────────────────────────────────────────┤
│             BACKEND                           │
│  Next.js API Routes (Node.js)                │
│  PostgreSQL (loan state)                     │
│  Redis (price cache, sessions, queues)       │
│  BullMQ (async jobs)                         │
│  Price monitor (cron, every 60s)             │
│  Liquidation bot (event-driven)              │
├──────────────────────────────────────────────┤
│             FRONTEND                          │
│  Next.js 14 + React 18 (already built)      │
│  + Borrower dashboard (TODO)                 │
│  + Wallet connect (TODO)                     │
│  + Loan flow (TODO)                          │
└──────────────────────────────────────────────┘
```

---

## 2. Component-by-Component Comparison

### 2.1 BTC Custody

| Aspect | v1 (Smart Contracts) | v2 (Simplified) |
|--------|---------------------|-----------------|
| Model | 3-of-3 multisig (Firefish-style) | 3-of-3 multisig (Firefish-style) |
| Keys | Borrower + Oracle + BRIXL | Borrower + Oracle + BRIXL |
| Tech | PSBT + MuSig2 | PSBT + MuSig2 |
| Difference | **Identical** | **Identical** |

**Verdict: No difference.** BTC custody is the same in both approaches. The Bitcoin escrow layer doesn't change.

### 2.2 Loan Logic

| Aspect | v1 (Smart Contracts) | v2 (Simplified) |
|--------|---------------------|-----------------|
| Where | BrixlVault.sol + BrixlLoanManager.sol on Ethereum | Next.js API + PostgreSQL |
| Transparency | Fully public, verifiable on-chain | Database records, auditable via API |
| Modifiability | Immutable (or upgradeable proxy + timelock) | Hotfix deployable in minutes |
| Gas cost | Users pay gas for every loan operation | Zero gas — API calls are free |
| Bug risk | Immutable bug = lost funds | Fixable bug = deploy patch |
| Trust model | Trustless — code is law | Trust BRIXL the company |

**Verdict: v2 is faster to build and easier to fix. v1 is more trustless.**

### 2.3 Liquidation

| Aspect | v1 (Smart Contracts) | v2 (Simplified) |
|--------|---------------------|-----------------|
| Trigger | BrixlLiquidator.sol called by keeper bots | Cron job checks prices every 60s |
| Oracle | Chainlink + Pyth on-chain feeds | Chainlink API + CoinGecko fallback |
| Margin calls | On-chain events | Email/SMS/push notifications |
| Execution | On-chain TX (gas cost) + BTC PSBT broadcast | API call + BTC PSBT broadcast |
| Latency | ~12s (Ethereum block time) + keeper latency | ~60s (cron interval) |
| Manipulation risk | Oracle manipulation possible | Same (both use Chainlink) |

**Verdict: Functionally equivalent.** The 60s vs 12s latency difference is negligible for BTC loans with 65-80% LTV thresholds (price doesn't move that fast).

### 2.4 USDC Liquidity

| Aspect | v1 (Smart Contracts) | v2 (Simplified) |
|--------|---------------------|-----------------|
| Source | On-chain Stability Pool (BrixlPool.sol) | BRIXL treasury wallet |
| Lender UX | Deposit USDC to pool, earn interest | Phase 2: lender pool via app |
| Yield | Pro-rata interest + liquidation gains | Same, but managed off-chain |
| Bootstrapping | Need initial pool deposits | Need treasury capital |
| Composability | Pool token could be used in other DeFi | No DeFi composability |

**Verdict: v1 has better composability and trustlessness for lenders. v2 is simpler to bootstrap.** For a v1 product where BRIXL provides initial liquidity, there's no practical difference.

### 2.5 Price Feeds

| Aspect | v1 (Smart Contracts) | v2 (Simplified) |
|--------|---------------------|-----------------|
| Primary | Chainlink BTC/USD on-chain feed | Chainlink API (off-chain) |
| Fallback | Pyth Network on-chain feed | CoinGecko / Binance API |
| Staleness | 1hr max (checked on-chain) | 1hr max (checked in code) |
| Cost | Reads are free (view functions) | API calls are free |

**Verdict: Equivalent reliability.** Same data source (Chainlink), different delivery method.

### 2.6 KYC / Compliance

| Aspect | v1 (Smart Contracts) | v2 (Simplified) |
|--------|---------------------|-----------------|
| Layer | App layer (not protocol) | App layer |
| Provider | Sumsub | Sumsub |
| Enforcement | Frontend gates + optional on-chain allowlist | API middleware |
| MiCA | Compliant (KYC before loan) | Compliant (KYC before loan) |

**Verdict: Identical.** KYC is always an app-layer concern.

---

## 3. Cost Comparison

| Cost Category | v1 (Smart Contracts) | v2 (Simplified) |
|---------------|---------------------|-----------------|
| **Development** | 3-6 months, 2-3 Solidity devs | 4-6 weeks, 1-2 fullstack devs |
| **Security audit** | $200K-500K (smart contract audit) | $20-50K (backend security review) |
| **Bug bounty** | $50K-200K (Immunefi) | $10-20K |
| **Infrastructure** | Ethereum gas + servers | Servers only |
| **Gas per loan** | ~$50-200 per operation (Ethereum L1) | $0 (API calls) |
| **Maintenance** | Contract upgrades require governance | Standard deployments |
| **Total est. cost** | **$500K-1M+** | **$50-100K** |

---

## 4. Risk Comparison

| Risk | v1 (Smart Contracts) | v2 (Simplified) |
|------|---------------------|-----------------|
| **Smart contract exploit** | HIGH — immutable bugs = lost funds | N/A — no smart contracts |
| **Platform trust** | LOW — code is law, trustless | HIGH — trust BRIXL the company |
| **Regulatory** | MEDIUM — on-chain = harder to comply | LOW — off-chain = easier KYC/AML |
| **Operational** | LOW — autonomous once deployed | MEDIUM — needs 24/7 monitoring |
| **Scalability** | HIGH — Ethereum handles scale | MEDIUM — need to scale backend |
| **Rug pull risk** | LOW — funds in smart contract | HIGHER — funds in treasury wallet |
| **Time-to-market** | HIGH risk of delays | LOW — simple tech, fast shipping |

---

## 5. Protocol Comparison: BRIXL vs Existing

### Previous Research (v1) — Compared Against

| Protocol | What BRIXL v1 borrowed | Status |
|----------|----------------------|--------|
| **Liquity V1** | CDP/Trove model, Stability Pool, algorithmic fees, immutable deployment | Smart contract backbone — GPL v3 forkable |
| **Firefish** | 3-of-3 multisig BTC escrow, PSBT, MuSig2, key destruction, MiCA compliance | BTC custody model — can't fork code (proprietary license), can reimplement patterns |
| **Curve/crvUSD** | Soft liquidation concept (LLAMMA) | Deferred to v2 — too complex |
| **Sovryn Zero** | Validated Liquity model works for BTC lending on RSK | Reference implementation |
| **Babylon** | BTC staking yield for idle collateral | Deferred to Phase 3 |
| **DLCs** | Fully Bitcoin-native conditional transactions | Deferred to v2/v3 — tooling too early |

### New Research (v2) — Simplified Take

| Protocol | Relevance to BRIXL v2 | What we actually use |
|----------|----------------------|---------------------|
| **Firefish** | **Primary model** — proves BTC loans work without smart contracts | Escrow pattern, business model, compliance approach |
| **Liquity V1** | Reference for when we add on-chain components (Phase 4) | Stability Pool concept for future lender pool |
| **Aave** | Not needed — pool-based lending is overkill for BTC loans | Nothing for v1 |
| **Curve/crvUSD** | Not needed — soft liquidation adds massive complexity | Nothing for v1 |
| **Sovryn Zero** | Validates the market, but RSK has low adoption | Market validation only |
| **Babylon** | Future yield source for locked BTC collateral | Phase 3+ integration |
| **DLCs** | Future upgrade path for fully trustless escrow | Phase 4+ when tooling matures |

---

## 6. What Changed and Why

### The Key Insight

> **You don't need smart contracts to lend against Bitcoin.**
> Firefish processes $50M+ in BTC loans with off-chain logic and Bitcoin multisig escrow.
> The loan logic doesn't need to be on-chain — it needs to be correct.

### What Stayed the Same
- BTC escrow model (3-of-3 multisig, PSBT, MuSig2)
- Loan tiers (SAVER/STANDARD/FLEX)
- LTV thresholds (30/50/65% max, 80% liquidation)
- Margin call levels (65/72/77%)
- Fee structure (0.75% origination, 5-12% APR, 4% liquidation)
- KYC integration (Sumsub, app layer)
- Frontend (Next.js, already built)

### What Changed
| Before (v1) | After (v2) | Why |
|-------------|-----------|-----|
| 8+ Solidity smart contracts | 0 smart contracts | Not needed for v1 |
| Ethereum L1 for loan logic | PostgreSQL for loan logic | Faster, cheaper, fixable |
| On-chain Stability Pool | Treasury wallet | Simpler bootstrapping |
| $200K+ audit | $20-50K security review | 10x cheaper |
| 3-6 month timeline | 4-6 week timeline | 4x faster |
| Relayer + committee bridge | No bridge needed | BTC stays on Bitcoin, USDC on Base/Ethereum |
| On-chain price feed | API-based price feed | Same data, simpler delivery |
| Keeper bot liquidation | Cron-based liquidation | Functionally identical |

### The Upgrade Path

```
v2 (Now)          → v2.5 (6 months)        → v3 (12+ months)
─────────────────────────────────────────────────────────────
Off-chain logic     On-chain loan registry    Full smart contract protocol
Treasury wallet     Lender pool (off-chain)   On-chain Stability Pool
Manual ops          Automated everything      Autonomous / governance-free
Trust BRIXL         Trust BRIXL + audits      Trustless (code is law)
```

---

## 7. Decision Matrix

| Criteria | Weight | v1 Score | v2 Score | Winner |
|----------|--------|----------|----------|--------|
| **Time to market** | 25% | 2/5 | 5/5 | v2 |
| **Cost** | 20% | 1/5 | 5/5 | v2 |
| **Security risk** | 20% | 2/5 | 4/5 | v2 |
| **Trustlessness** | 15% | 5/5 | 2/5 | v1 |
| **Simplicity** | 10% | 2/5 | 5/5 | v2 |
| **Scalability** | 10% | 4/5 | 3/5 | v1 |
| **Weighted Total** | 100% | **2.45** | **4.30** | **v2** |

---

## 8. Final Recommendation

**Build v2 (simplified) now. Add smart contracts later when:**
1. The product has proven market demand
2. Loan volume justifies $200K+ audit costs
3. Trustlessness becomes a competitive requirement
4. The team has Solidity expertise and audit budget

**The Firefish model is validated.** They've processed real loans, real BTC, real money — without smart contracts. BRIXL can do the same, faster and cheaper.

**What to build today:**
1. Bitcoin escrow service (PSBT + multisig)
2. Backend API (loan management)
3. Price monitor + liquidation bot
4. Borrower dashboard (frontend)
5. KYC integration

**That's it. Ship in 4-6 weeks.**

---

*See also: [BRIXL_PROTOCOL_RESEARCH.md](./BRIXL_PROTOCOL_RESEARCH.md) (original v1 research) | [BRIXL_ARCHITECTURE_V2.md](./BRIXL_ARCHITECTURE_V2.md) (new simplified architecture)*
