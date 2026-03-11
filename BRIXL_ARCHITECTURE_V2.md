# BRIXL Architecture v2 — Simplified BTC Loans

> **Objective:** Build the simplest possible BTC-backed lending product. No custom smart contracts.
> **Date:** March 2026
> **Status:** Replaces smart-contract-heavy architecture from v1 research.

---

## 1. Core Thesis

BRIXL is a **Bitcoin loan product**. Users lock BTC as collateral, receive USDC. That's it.

Firefish already proved this model works — non-custodial BTC custody, off-chain loan logic, MiCA-compliant. They did it without smart contracts. We should too.

**The question isn't "what smart contract framework?" — it's "what do we actually need to build?"**

---

## 2. What Actually Needs to Be Built

### 2.1 Full Component List

| # | Component | Purpose | Smart Contract? | Complexity |
|---|-----------|---------|----------------|------------|
| 1 | **BTC Escrow (Bitcoin Scripts)** | Lock borrower's BTC on Bitcoin L1 | No — Bitcoin multisig | Medium |
| 2 | **Backend API** | Loan management, business logic | No — Node.js/Next.js | Medium |
| 3 | **Database** | Loan state, user records, history | No — PostgreSQL | Low |
| 4 | **Price Feed Service** | Monitor BTC/USD for margin calls | No — Chainlink API / CoinGecko | Low |
| 5 | **Liquidation Bot** | Trigger liquidation when LTV > 80% | No — cron job / event listener | Low |
| 6 | **USDC Treasury** | Pool of USDC for disbursement | No — treasury wallet | Low |
| 7 | **Frontend** | User interface for borrowing | No — already built (Next.js) | Done |
| 8 | **KYC Integration** | Identity verification | No — Sumsub API | Low |
| 9 | **Notification System** | Margin call alerts | No — email/SMS/push | Low |
| 10 | **Admin Dashboard** | Loan monitoring, risk management | No — internal tool | Low |

**Total custom smart contracts needed: 0**

### 2.2 Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     BRIXL ARCHITECTURE v2                        │
│                  "No Smart Contracts Required"                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────────────────────────┐                       │
│  │         BITCOIN L1 (Collateral)      │                       │
│  │                                      │                       │
│  │  3-of-3 Multisig Escrow              │                       │
│  │  • Borrower key                      │                       │
│  │  • BRIXL key                         │                       │
│  │  • Oracle/Arbitrator key             │                       │
│  │                                      │                       │
│  │  Pre-signed closing transactions:    │                       │
│  │  • Repayment TX → BTC to borrower    │                       │
│  │  • Default TX → BTC to BRIXL         │                       │
│  │  • Liquidation TX → BTC to BRIXL     │                       │
│  │                                      │                       │
│  │  Tech: PSBT + MuSig2                 │                       │
│  │  Timelock: 7-day safety fallback     │                       │
│  └──────────────┬───────────────────────┘                       │
│                 │                                                │
│                 │ Escrow confirmation                            │
│                 ▼                                                │
│  ┌──────────────────────────────────────┐                       │
│  │          BACKEND (Loan Logic)         │                       │
│  │                                       │                       │
│  │  Next.js API Routes / Node.js         │                       │
│  │                                       │                       │
│  │  POST /api/loans/create               │                       │
│  │    → Generate escrow address          │                       │
│  │    → Create pre-signed TXs            │                       │
│  │    → Wait for BTC confirmation        │                       │
│  │    → Disburse USDC from treasury      │                       │
│  │                                       │                       │
│  │  POST /api/loans/repay                │                       │
│  │    → Verify USDC received             │                       │
│  │    → Sign repayment TX                │                       │
│  │    → Release BTC to borrower          │                       │
│  │                                       │                       │
│  │  GET /api/loans/:id                   │                       │
│  │    → Loan status, LTV, margin status  │                       │
│  │                                       │                       │
│  │  CRON: Price Monitor (every 1 min)    │                       │
│  │    → Fetch BTC/USD from Chainlink     │                       │
│  │    → Check all active loans           │                       │
│  │    → Trigger margin calls at 65/72/77%│                       │
│  │    → Trigger liquidation at 80%       │                       │
│  │                                       │                       │
│  └──────────────┬───────────────────────┘                       │
│                 │                                                │
│                 ▼                                                │
│  ┌──────────────────────────────────────┐                       │
│  │         DATABASE (PostgreSQL)         │                       │
│  │                                       │                       │
│  │  Tables:                              │                       │
│  │  • users (wallet, kyc_status, email)  │                       │
│  │  • loans (id, btc_amount, usdc_amount,│                       │
│  │          tier, apr, status, escrow_tx, │                       │
│  │          liquidation_price, term_end)  │                       │
│  │  • margin_calls (loan_id, level, ts)  │                       │
│  │  • transactions (loan_id, type, hash) │                       │
│  │  • price_history (btc_usd, timestamp) │                       │
│  │                                       │                       │
│  └──────────────────────────────────────┘                       │
│                                                                  │
│  ┌──────────────────────────────────────┐                       │
│  │        USDC TREASURY                  │                       │
│  │                                       │                       │
│  │  • Treasury wallet (multisig)         │                       │
│  │  • Disbursement on loan approval      │                       │
│  │  • Receives repayments + interest     │                       │
│  │  • Phase 2: Lender pool (earn yield)  │                       │
│  │                                       │                       │
│  │  Chain: Base (low fees) or Ethereum   │                       │
│  └──────────────────────────────────────┘                       │
│                                                                  │
│  ┌──────────────────────────────────────┐                       │
│  │         FRONTEND (Already Built)      │                       │
│  │                                       │                       │
│  │  • Next.js 14 + React 18             │                       │
│  │  • Loan calculator (done)             │                       │
│  │  • Landing page (done)                │                       │
│  │  • TODO: Borrower dashboard           │                       │
│  │  • TODO: Loan application flow        │                       │
│  │  • TODO: Wallet connect (wagmi/viem)  │                       │
│  │  • TODO: Repayment interface          │                       │
│  │                                       │                       │
│  └──────────────────────────────────────┘                       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. BTC Escrow Design (Bitcoin Layer)

The only "protocol" code — standard Bitcoin multisig patterns.

### 3.1 Escrow Flow

```
Step 1: PREFUND
  Borrower sends BTC to a prefund address (single-sig, borrower's key)
  → 7-day timelock: if loan setup fails, borrower reclaims BTC automatically

Step 2: ESCROW CONSTRUCTION
  Backend generates 3-of-3 multisig address:
    Key 1: Borrower
    Key 2: BRIXL platform
    Key 3: Independent oracle/arbitrator

  Backend constructs ALL closing PSBTs:
    • Repayment PSBT → sends BTC back to borrower
    • Default PSBT → sends BTC to BRIXL treasury
    • Liquidation PSBT → sends BTC to BRIXL treasury (minus fees)

Step 3: VERIFICATION & SIGNING
  Borrower verifies all PSBTs (shown in UI)
  Borrower signs escrow TX → BTC moves from prefund to multisig
  Borrower DISCARDS escrow private key (Firefish pattern)

Step 4: LOAN ACTIVE
  BTC locked in multisig on Bitcoin L1
  Can ONLY be spent via pre-signed closing transactions
  USDC disbursed to borrower's wallet

Step 5: RESOLUTION
  A) Repayment: Borrower repays USDC → BRIXL signs repayment PSBT → BTC released
  B) Default: Term expires → BRIXL + Oracle sign default PSBT → BTC to BRIXL
  C) Liquidation: LTV > 80% → BRIXL + Oracle sign liquidation PSBT → BTC to BRIXL
```

### 3.2 Libraries & Tools

| Tool | Purpose |
|------|---------|
| **bitcoinjs-lib** | PSBT construction, multisig address generation |
| **@noble/secp256k1** | Key generation, MuSig2 |
| **Blockstream Esplora API** | Monitor Bitcoin transactions |
| **mempool.space API** | Fee estimation, TX broadcasting |

---

## 4. Backend API Design

### 4.1 API Routes

```
Authentication:
  POST /api/auth/nonce          → Generate SIWE nonce
  POST /api/auth/verify         → Verify wallet signature
  POST /api/auth/logout         → Clear session

KYC:
  POST /api/kyc/start           → Start Sumsub verification
  POST /api/kyc/webhook         → Receive Sumsub callbacks
  GET  /api/kyc/status          → Check KYC status

Loans:
  POST /api/loans/estimate      → Calculate loan terms (no auth)
  POST /api/loans/create        → Create new loan application
  GET  /api/loans               → List user's loans
  GET  /api/loans/:id           → Get loan details
  POST /api/loans/:id/repay     → Initiate repayment
  GET  /api/loans/:id/escrow    → Get escrow details & PSBTs

Price:
  GET  /api/price/btc           → Current BTC/USD price
  GET  /api/price/history       → Price history

Admin (internal):
  GET  /api/admin/loans         → All loans dashboard
  GET  /api/admin/health        → System health check
  POST /api/admin/liquidate/:id → Manual liquidation trigger
```

### 4.2 Loan State Machine

```
  CREATE → AWAITING_ESCROW → ESCROW_CONFIRMED → USDC_DISBURSED → ACTIVE
                                                                    │
                                    ┌───────────────────────────────┤
                                    │               │               │
                              MARGIN_CALL_1   MARGIN_CALL_2   MARGIN_CALL_3
                              (LTV ≥ 65%)     (LTV ≥ 72%)     (LTV ≥ 77%)
                                    │               │               │
                                    └───────────────┴───────┬───────┘
                                                            │
                                              ┌─────────────┼─────────────┐
                                              │             │             │
                                           REPAID      LIQUIDATED    DEFAULTED
                                        (BTC released) (BTC seized) (term expired)
```

### 4.3 Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Runtime | Node.js 20 | Already using Next.js |
| Framework | Next.js 14 API routes | Already set up, full-stack |
| Database | PostgreSQL + Prisma ORM | Relational data, ACID transactions |
| Cache | Redis | Price caching, rate limiting, sessions |
| Queue | BullMQ (Redis-backed) | Async jobs: escrow monitoring, margin calls |
| Auth | SIWE (Sign-In with Ethereum) | Wallet-based auth |
| KYC | Sumsub SDK | Already in PRD |
| Bitcoin | bitcoinjs-lib + Esplora | PSBT, multisig, chain monitoring |
| Hosting | Vercel (frontend) + Railway/Fly.io (backend) | Simple, scalable |
| Monitoring | Sentry + Datadog | Error tracking, uptime |

---

## 5. Loan Product Config

Same tiers from the PRD, just managed in backend config (not a smart contract):

```typescript
const LOAN_TIERS = {
  SAVER: {
    maxLtvPercent: 30,
    aprPercent: 5.0,
    minTermMonths: 12,
    maxTermMonths: 60,
    marginCall1: 65,  // % LTV
    marginCall2: 72,
    marginCall3: 77,
    liquidationLtv: 80,
    originationFeePercent: 0.75,
    minLoanUSDC: 500,
    maxLoanUSDC: 1_000_000,
  },
  STANDARD: {
    maxLtvPercent: 50,
    aprPercent: 7.5,
    minTermMonths: 6,
    maxTermMonths: 36,
    marginCall1: 65,
    marginCall2: 72,
    marginCall3: 77,
    liquidationLtv: 80,
    originationFeePercent: 0.75,
    minLoanUSDC: 500,
    maxLoanUSDC: 1_000_000,
  },
  FLEX: {
    maxLtvPercent: 65,
    aprPercent: 9.0,  // 9-12% variable
    minTermMonths: 3,
    maxTermMonths: 12,
    marginCall1: 65,
    marginCall2: 72,
    marginCall3: 77,
    liquidationLtv: 80,
    originationFeePercent: 0.75,
    minLoanUSDC: 500,
    maxLoanUSDC: 500_000,
  },
} as const;
```

---

## 6. Price Monitoring & Liquidation

### 6.1 Price Feed

```typescript
// Price service — runs every 60 seconds
async function checkPrices() {
  const btcPrice = await getBTCPrice(); // Chainlink or CoinGecko

  const activeLoans = await db.loan.findMany({
    where: { status: { in: ['ACTIVE', 'MARGIN_CALL_1', 'MARGIN_CALL_2', 'MARGIN_CALL_3'] } }
  });

  for (const loan of activeLoans) {
    const currentLtv = calculateLTV(loan.debtUSDC, loan.btcCollateralSats, btcPrice);

    if (currentLtv >= loan.tier.liquidationLtv) {
      await triggerLiquidation(loan);
    } else if (currentLtv >= loan.tier.marginCall3) {
      await sendMarginCall(loan, 3);
    } else if (currentLtv >= loan.tier.marginCall2) {
      await sendMarginCall(loan, 2);
    } else if (currentLtv >= loan.tier.marginCall1) {
      await sendMarginCall(loan, 1);
    }
  }
}
```

### 6.2 Liquidation Process

```
1. LTV crosses 80% threshold
2. Backend marks loan as LIQUIDATED
3. BRIXL + Oracle sign the pre-signed liquidation PSBT
4. Liquidation PSBT broadcast to Bitcoin network
5. BTC received by BRIXL treasury
6. 4% liquidation fee: 3% to protocol, 1% to operations
7. Remaining BTC value (above debt) returned to borrower
8. Borrower notified via email/push
```

---

## 7. Security Considerations

| Risk | Mitigation |
|------|-----------|
| **BRIXL key compromise** | Hardware security module (HSM) for platform key. Multisig treasury. |
| **Oracle manipulation** | Multiple price sources (Chainlink + CoinGecko + Binance). Median price. |
| **Database compromise** | Encrypted at rest. No private keys in DB. Keys in HSM/Vault. |
| **Escrow front-running** | All closing TXs pre-signed before escrow. Cannot be altered. |
| **API abuse** | Rate limiting, authentication, input validation. |
| **Regulatory** | KYC at app layer. MiCA compliance. Legal review. |

---

## 8. Implementation Phases

### Phase 1: MVP (4-6 weeks)
1. Database schema + Prisma models
2. Bitcoin escrow service (PSBT generation, multisig)
3. Core API routes (create loan, repay, status)
4. Price monitoring service
5. Borrower dashboard (frontend)
6. Manual USDC disbursement from treasury wallet

### Phase 2: Production (weeks 7-10)
7. KYC integration (Sumsub)
8. Automated USDC disbursement
9. Liquidation bot
10. Email/SMS notifications for margin calls
11. Admin dashboard
12. Security audit (backend + Bitcoin scripts)

### Phase 3: Scale (weeks 11-16)
13. Lender pool (let others provide USDC liquidity, earn yield)
14. Automated escrow monitoring (Esplora webhooks)
15. Mobile-responsive borrower dashboard
16. Analytics and reporting
17. Multi-currency support (USDT, EUR stablecoins)

### Phase 4: Decentralize (future)
18. On-chain loan registry (transparency, optional)
19. Decentralized oracle for escrow arbitration
20. Babylon staking for idle BTC collateral
21. DLC-based escrow (fully trustless, no platform key)

---

## 9. Why NOT Smart Contracts (for v1)

| Reason | Details |
|--------|---------|
| **Audit cost** | $200K-500K for a proper audit. Backend security review is $20-50K. |
| **Development time** | 3-6 months for contracts + testing. Backend MVP in 4-6 weeks. |
| **Smart contract risk** | Bugs = lost funds, immutable. Backend bugs = fixable hotfix. |
| **Not needed** | BTC custody is on Bitcoin (multisig). Loan logic doesn't need to be on-chain. |
| **Firefish proves it** | $50M+ in BTC loans processed without EVM smart contracts. |
| **Regulatory** | Off-chain logic = easier to comply with KYC/AML/MiCA requirements. |
| **Iteration speed** | Can ship updates daily. Smart contracts require redeployment + migration. |

**When to ADD smart contracts (Phase 4+):**
- When BRIXL needs a trustless lender pool (Stability Pool)
- When the protocol should operate without BRIXL the company
- When on-chain transparency becomes a competitive requirement
- When audit budget allows ($200K+)

---

*This document replaces the smart-contract-heavy architecture from BRIXL_PROTOCOL_RESEARCH.md for the v1 product.*
