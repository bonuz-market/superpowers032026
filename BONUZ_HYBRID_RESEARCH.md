# BONUZ HYBRID RESEARCH — The Ultimate Comparative & Build Guide

> Purpose: Compare v1 Master Context vs Manifesto, extract the best of each, map the Migration Protocol, and provide concrete build-time estimates for every product type bonuz can ship.
> Date: 2026-03-11
> Based on: Deep analysis of all 6 codebases + founder manifesto

---

## PART 1: v1 MASTER CONTEXT vs MANIFESTO — HEAD-TO-HEAD

### What Each Document Does Well

| Dimension | v1 Master Context (Winner?) | Manifesto (Winner?) |
|---|---|---|
| **Technical precision** | **v1 wins.** Contract addresses, SDK versions, chain lists, code patterns. Actionable for builders. | Manifesto has zero technical detail. |
| **Strategic framing** | v1 is crypto-native: "Human Layer between blockchains and people" | **Manifesto wins.** "Infrastructure for human outcomes" — doesn't lead with crypto. Works for governments, enterprises, non-crypto brands. |
| **Audience range** | v1 speaks to: crypto investors, Web3 devs, crypto-native brands | **Manifesto wins.** Speaks to: everyone. B2B, B2C, B2G, enterprise, non-crypto brands, Web2 companies |
| **Design principles** | v1 has none explicitly stated | **Manifesto wins.** 7 clear principles that guide every decision |
| **Category definition** | v1: "protocol + distribution in one system" (good but niche) | **Manifesto wins.** "Modular human-centered infrastructure layer" — universal category |
| **Value model** | v1 describes products (what bonuz does) | **Manifesto wins.** Describes value per stakeholder (Individual / Community / Brand) — better for sales, pitches, partnerships |
| **Product detail** | **v1 wins.** Route groups, feature flags, SDK components, RBAC roles — everything needed to build | Manifesto has no product detail |
| **Entity/legal structure** | **v1 wins.** Clear Bonuz Inc. vs Bonuz Technology DMCC split | Manifesto doesn't address this |
| **Codebase guidance** | **v1 wins.** Monorepo structure, Zustand patterns, authentication flows | Manifesto is architecture-agnostic |
| **Roadmap / future** | v1 mentions Social Oracle briefly | **Manifesto wins.** Frames the entire future: AI agents, commerce, programmable journeys, universal interface layer |
| **Tone rules** | **v1 wins.** Specific writing rules, gas wording, naming conventions | Manifesto is a vision document, not a style guide |
| **Business model** | **v1 wins.** SaaS + Enterprise + Protocol fees | Manifesto doesn't cover revenue |
| **White-label guidance** | **v1 wins.** Section 14 with what to customize vs what stays shared | Manifesto doesn't cover implementation |
| **Migration path (new projects)** | Neither covers this | Neither covers this |
| **Speed-to-build estimates** | Neither covers this | Neither covers this |

### Verdict

**Neither document alone is sufficient.** The v1 is a builder's manual but positions bonuz too narrowly as crypto infrastructure. The Manifesto is a universal strategic framework but can't help anyone build anything.

**The hybrid must combine:**
- Manifesto's strategic framing + design principles + value model + audience universality
- v1's technical precision + product detail + entity structure + tone rules + business model
- NEW: Migration Protocol, build-time estimates, integration speed matrix, white-label tiers

---

## PART 2: THE HYBRID FRAMEWORK — BEST OF BOTH

### New Strategic Hierarchy

```
LAYER 1: STRATEGIC IDENTITY (from Manifesto)
├── Core definition: "Infrastructure for human outcomes"
├── 7 design principles
├── Three-sided value model (Individual / Community / Brand)
├── Category: The Human Layer
└── Web3 as enabling rails, not the story

LAYER 2: ARCHITECTURE (from v1, refined)
├── Entity structure (Bonuz Inc. + Bonuz Technology DMCC)
├── Human Layer stack (chains → middleware → protocols → apps)
├── Protocol suite (bonuz ID + DNFT + AA)
└── Distribution model (consumer app + dashboard + white-labels + SDK)

LAYER 3: PRODUCTS (from v1, enhanced with agent research)
├── Consumer App (bonuz: Lifestyle Wallet)
├── Brand Dashboard (app.bonuz.market)
├── Bonuz ID Web App (bonuz.id)
├── SDK & Integrations (@bonuz/sdk)
├── White-Label Apps (NEW: tiered system)
└── Migration Protocol (NEW)

LAYER 4: BUILDING (NEW — from deep codebase research)
├── What's modular / strippable
├── Integration patterns (minimal → full)
├── Build-time estimates per product type
├── Feature flag system
├── Theme / branding customization
└── Environment / deployment patterns

LAYER 5: FUTURE (from Manifesto + v1)
├── Social Oracle
├── Social Continuation Layer Protocol
├── AI agent integration
├── Commerce-linked engagement
├── Programmable user journeys
├── $BONUZ / $BOINTS / $MENDE tokens
└── Universal human interface layer
```

### New Opening Statement (Hybrid)

> bonuz is a modular infrastructure layer that transforms identity, intention, and engagement into measurable outcomes for people, communities, brands, and ecosystems.
>
> It is not defined by any single product. The consumer wallet, brand dashboard, loyalty tools, identity system, and Web3 capabilities are all expressions of a broader architecture called the **Human Layer** — a system connecting identity, permissions, incentives, participation, rewards, and real-world or digital actions into programmable outcome flows.
>
> The purpose of bonuz is to reduce friction between what people, communities, and brands want to achieve and the systems required to achieve it. Blockchain and Web3 components are enabling rails, not the primary story. The broader vision is infrastructure for human outcomes that operates across consumer apps, brand systems, communities, commerce, AI agents, and future digital-physical ecosystems.

This framing works for:
- **B2C**: "An app that makes your passes, rewards, and identity portable"
- **B2B**: "Deploy measurable engagement systems without technical complexity"
- **B2G (Government)**: "Digital identity and participation infrastructure for citizen services"
- **Enterprise**: "Programmable outcome flows across your entire ecosystem"
- **Web3 native**: "The Human Layer protocol with its own distribution"

---

## PART 3: THE MIGRATION PROTOCOL — CONCEPT MAP

### What Is It?

The bonuz Migration Protocol is a **structured upgrade path** that converts existing projects, brands, or communities into bonuz-powered systems with minimal disruption.

Two distinct modes:

### Mode 1: Project Migration (Web2 → bonuz / Old Web3 → bonuz)

**Use case:** An existing Web2 brand, old Web3 project, or dying NFT community wants to upgrade to modern infrastructure without starting from scratch.

**What they get:**
- Identity layer via bonuz ID (portable, on-chain, permissioned)
- Engagement assets via DNFT protocol (loyalty, memberships, certificates)
- Account abstraction (gasless for users, no wallet complexity)
- Dashboard for no-code management
- Optionally: white-label app or SDK integration into their existing app

**Migration steps:**

| Step | What Happens | Time Estimate |
|------|-------------|---------------|
| 1. Assessment | Map existing user base, engagement model, and desired outcomes | 1-2 days |
| 2. Identity bridge | Create bonuz IDs for existing users (social login, email match, or wallet link) | 1-3 days |
| 3. Asset migration | Convert existing NFTs/rewards/memberships to bonuz DNFTs | 2-5 days |
| 4. Gating setup | Token-gating or identity-gating for existing holders/members | 1-2 days |
| 5. Dashboard onboard | Set up brand dashboard with campaigns, quests, analytics | 1-2 days |
| 6. Integration | SDK embed into existing app/site, or new white-label app | 3-10 days |
| **Total** | | **9-24 days** |

**Gating functions available:**
- **Web3 gating:** Hold specific NFT/token → unlock access (already built: token-gate in dashboard with contract address + chain + minimum amount)
- **Web2 gating:** Have bonuz ID with specific attestation → unlock access
- **Hybrid gating:** Combine both (e.g., hold legacy NFT + have bonuz ID = premium tier)

### Mode 2: Brand Migration (Physical business → bonuz engagement)

**Use case:** A restaurant, coffee shop, hotel, club, gym, retail store, or venue wants to launch digital engagement (loyalty, memberships, events) using bonuz.

**What they get:**
- Dashboard account with brand profile
- DNFT-based engagement (punch cards, loyalty points, memberships, PoP)
- QR/NFC scanning for check-ins and redemptions
- Consumer-facing experience via bonuz app (or dedicated white-label)
- Analytics (mints, redemptions, check-ins)

**Migration steps:**

| Step | What Happens | Time Estimate |
|------|-------------|---------------|
| 1. Brand setup | Create partner account on dashboard | 30 min |
| 2. Campaign design | Create event/app with challenges and rewards | 2-4 hours |
| 3. DNFT design | Design loyalty cards, vouchers, memberships on canvas | 1-2 hours |
| 4. QR deployment | Print/display QR codes at venue | 1 hour |
| 5. Team setup | Add staff as redeemers/collaborators | 30 min |
| 6. Go live | Users scan QR → get bonuz app → participate | Immediate |
| **Total** | | **1 day (no-code)** |

### What Makes Migration Protocol Unique

1. **No lock-in:** bonuz ID is on-chain, user-owned. Users can take their identity elsewhere.
2. **Backward compatible:** Existing NFTs and tokens can be used for gating without migration.
3. **Progressive adoption:** Start with dashboard only → add SDK → add white-label → full protocol integration.
4. **Immersive upgrade:** Each step adds more capability without breaking what's already working.

---

## PART 4: WHITE-LABEL TIERS — FROM BAREBONE TO FULL

Based on deep analysis of bonuz-app-v2 codebase modularity:

### Tier 0: Barebone Scanner (Fastest to Ship)

**What it is:** A minimal app with just login + QR/NFC + one use case. No wallet visible. No crypto UI. Pure engagement tool.

**Features included:**
- Social login (Google/Apple/Email via Web3Auth)
- Bonuz ID creation (handle + profile)
- QR code display (my QR)
- QR scanner (scan others)
- One engagement use case (e.g., loyalty punch card, membership, event check-in)
- DNFT display for the specific use case
- Settings (basic)

**Features stripped:**
- Full wallet (send/receive/swap/buy/sell) — HIDDEN
- DApp browser — REMOVED
- Discover section — REMOVED
- AI features — REMOVED
- Cross-chain anything — REMOVED
- Token list — REMOVED
- NFT gallery — REMOVED (only specific DNFT shown)

**Technical changes needed:**

| Change | File | Effort |
|--------|------|--------|
| Remove `(ai)` route group | `/app/(ai)/` | Delete directory |
| Remove `(discover)` route group | `/app/(discover)/` | Delete directory |
| Remove `(browser)` route group | `/app/(browser)/` | Delete directory |
| Hide wallet tab or reduce to DNFT-only | `/app/(tabs)/(wallet)/` | Modify tab config |
| Remove buy/sell | `/app/(wallet)/(buy)`, `(sell)` | Delete directories |
| Remove swap UI | `/pages/wallet/swap/` | Delete or hide |
| Customize theme | `/store/theme.ts` | Change `baseColors` |
| Update branding | `app.json`, `app.config.js` | Change name, icons, metadata |
| New bundle ID | `app.json` | Change from `market.bonuz.app` |
| Feature flags | `/constants/featureFlags.ts` | Add flags for wallet visibility |

**Build time estimate:** 3-5 days (experienced dev), 5-8 days (new to codebase)
**Ongoing maintenance:** Low — just keep auth + scan + DNFT in sync with upstream

### Tier 1: Branded Engagement App

**What it is:** Scanner + loyalty + events + basic wallet. Branded for one organization.

**Features included:**
- Everything in Tier 0
- Event listing & check-in flows
- Loyalty program display (punch cards, points)
- DNFT collection view
- Basic wallet (receive + view tokens)
- Push notifications
- Analytics tracking

**Features stripped:**
- DApp browser — REMOVED
- AI features — REMOVED
- Cross-chain swaps — REMOVED
- Buy/sell fiat ramps — REMOVED
- Full send/swap flows — REMOVED

**Build time estimate:** 5-10 days
**Best for:** Clubs, gyms, event venues, restaurant chains, sports teams

### Tier 2: Full White-Label Wallet

**What it is:** Complete bonuz experience, fully rebranded. Different name, different colors, same power.

**Features included:**
- Everything from bonuz: Lifestyle Wallet
- Custom theme (colors, fonts, branding)
- Custom app name and bundle ID
- Custom WalletConnect metadata
- Custom chain selection (subset of 44+)
- Custom fiat ramp provider (if needed)

**Features stripped:** Nothing (or selectively)

**Build time estimate:** 10-15 days
**Best for:** Major brands wanting their own crypto lifestyle app

### Tier Comparison Matrix

| Dimension | Tier 0 (Barebone) | Tier 1 (Engagement) | Tier 2 (Full) |
|---|---|---|---|
| Login | Social login | Social login | Social login |
| Bonuz ID | Yes | Yes | Yes |
| QR Scanner | Yes | Yes | Yes |
| Wallet visible | No | Basic (view only) | Full |
| Send/Receive | No | Receive only | Full |
| Swap | No | No | Yes |
| Buy/Sell | No | No | Yes |
| DNFTs | Single use case | Full collection | Full collection |
| Events | No | Yes | Yes |
| Loyalty | Single program | Full | Full |
| DApp Browser | No | No | Yes |
| AI Features | No | No | Optional |
| Discover | No | No | Yes |
| Chains | 1 (Base) | 1-3 | 44+ |
| Build time | 3-5 days | 5-10 days | 10-15 days |
| Maintenance | Low | Medium | High (keep in sync) |
| Target | Coffee shop, gym | Club, venue, chain | Major brand |

---

## PART 5: INTEGRATION SPEED MATRIX — HOW FAST CAN EACH PRODUCT TYPE BE SHIPPED?

### A. New Products from Scratch

| Product | Description | Build Time | Vibe-Codeable? | Dependencies |
|---|---|---|---|---|
| **Barebone white-label** | Login + scan + 1 use case | 3-5 days | Yes (with template) | bonuz-app-v2 fork |
| **Brand dashboard account** | No-code campaign setup | 30 min | N/A (no code needed) | Dashboard access |
| **SDK integration into existing dApp** | Add bonuz login to any React app | 1-2 days | Yes | @bonuz/sdk npm |
| **SDK integration into existing website** | Add bonuz profile widget | 1-2 days | Yes | @bonuz/sdk npm |
| **Event check-in system** | Dashboard + QR codes + scanning | 4-6 hours | N/A (no code) | Dashboard access |
| **Loyalty punch card program** | Dashboard + punch card DNFT | 2-4 hours | N/A (no code) | Dashboard access |
| **Membership program** | Dashboard + membership DNFT | 2-4 hours | N/A (no code) | Dashboard access |
| **Quest/challenge campaign** | Dashboard + challenges + rewards | 4-8 hours | N/A (no code) | Dashboard access |
| **Branded engagement app (Tier 1)** | Fork + customize + deploy | 5-10 days | Partially | bonuz-app-v2 fork |
| **Full white-label wallet** | Complete rebrand | 10-15 days | No | bonuz-app-v2 fork |
| **Migration: Web3 project** | Full protocol migration | 9-24 days | No | Multiple systems |
| **Migration: Physical brand** | Dashboard + QR + training | 1 day | N/A (no code) | Dashboard access |
| **Custom web profile embed** | Fetch user via API, render card | 1 day | Yes | REST API |
| **New marketing satellite page** | New wallet-type landing page | 1-2 days | Yes | bonuz-xyz components |

### B. Enhancement to Existing Products

| Enhancement | Where | Build Time | Complexity |
|---|---|---|---|
| Add new chain support | bonuz-app-v2 `constants/networks.ts` | 2-4 hours | Low |
| Add new social platform to bonuz ID | BonuzSocialId.sol + frontend | 1-2 days | Medium |
| Add new DNFT type | BonuzTokens.sol + dashboard + app | 3-5 days | Medium |
| Add new feature flag | `constants/featureFlags.ts` + conditionals | 1-2 hours | Low |
| Add new theme | `store/theme.ts` | 2-4 hours | Low |
| Add new locale/language | `/locales/{lang}.json` | 1-2 days | Low (translation work) |
| Webhook/API integration | Dashboard backend | 3-5 days | Medium |
| Embeddable profile widget | New package in monorepo | 3-5 days | Medium |
| NavBar / Web SDK | New package in monorepo | 5-10 days | High |

### C. Future Product Build Estimates

| Product | Description | Build Time | Prerequisite |
|---|---|---|---|
| **Social Oracle v1** | Permissioned query layer over identity + engagement data | 4-8 weeks | Sufficient user data |
| **Social Continuation Layer** | Protocol for persistent social state across apps | 8-16 weeks | Social Oracle |
| **AI Agent SDK** | Machine-readable API for autonomous actors | 4-6 weeks | API standardization |
| **Commerce integration** | Payment flow linked to engagement | 4-8 weeks | Payment partner |
| **Programmable journey builder** | Visual flow builder for user journeys | 6-12 weeks | Quest system expansion |
| **Outcome scoring engine** | Measure and optimize for outcomes, not activity | 4-8 weeks | Analytics foundation |
| **$BONUZ token launch** | Token contract + distribution + governance | 8-12 weeks | Legal + economic design |

---

## PART 6: WHAT THE v2 MASTER CONTEXT SHOULD LOOK LIKE

### Recommended Structure

```
Section 0: Strategic Identity (NEW — from Manifesto)
  - Core definition
  - 7 design principles
  - Three-sided value model
  - Audience framing (B2C, B2B, B2G, Enterprise)

Section 1: Architecture (refined from v1 Section 3)
  - Human Layer stack
  - Protocol suite overview
  - Distribution model

Section 2: Entities & Legal (from v1 Section 2)
  - Bonuz Inc. + Bonuz Technology DMCC
  - Domains & roles table

Section 3: Protocols (from v1 Section 4, unchanged — precise)
  - bonuz ID Protocol
  - bonuz Engagement Protocol (DNFT)
  - Account Abstraction & Middleware

Section 4: Products (from v1 Section 5, enhanced)
  - Consumer App
  - Brand Dashboard
  - Bonuz ID Web App
  - SDK & Developer Integration
  - White-Label Tiers (NEW — Tier 0/1/2)
  - Migration Protocol (NEW — Mode 1 + Mode 2)

Section 5: Building Guide (NEW — from codebase research)
  - What's modular / strippable
  - Feature flag system
  - Theme / branding customization
  - Minimum viable fork strategy
  - Build-time estimates
  - Integration speed matrix

Section 6: Infrastructure & Partners (from v1 Section 6)

Section 7: Codebase Architecture (from v1 Section 7)

Section 8: Roadmap (NEW — from Manifesto + v1)
  - Social Oracle
  - Social Continuation Layer Protocol
  - AI Agent Integration
  - Commerce Integration
  - Programmable Journeys
  - Outcome Scoring Engine
  - Token Ecosystem ($BONUZ, $BOINTS, $MENDE)

Section 9: Business Model (from v1 Section 11)

Section 10: Tone & Writing Rules (from v1 Section 12, updated)
  - Add Manifesto language for non-crypto audiences
  - Keep technical precision for builders
  - Add audience-switching rules

Section 11: Reusable Snippets (updated with hybrid language)

Section 12: White-Label Technical Brief (from v1 Section 14, enhanced with tiers)

Section 13: AI Instructions (updated)
```

### Key Differences from v1

1. **Leads with outcomes, not crypto** — Section 0 sets the strategic frame before any technical detail
2. **Includes design principles** — The 7 principles guide decisions consistently
3. **Three-sided value model** — Individual/Community/Brand framing for every audience
4. **White-label tiers** — Concrete: Tier 0 (barebone), Tier 1 (engagement), Tier 2 (full)
5. **Migration Protocol** — Two modes: project migration + brand migration
6. **Building guide** — Practical: what's strippable, what stays, how long things take
7. **Build-time estimates** — For every product type bonuz can ship
8. **Explicit roadmap** — What's future vs what's built, with time estimates
9. **Audience-switching** — The same system described differently for B2C, B2B, B2G, Enterprise

### Key Things Preserved from v1

1. All contract addresses and chain deployments
2. Full tech stack details (versions, packages, patterns)
3. Entity/legal split (Bonuz Inc. vs Bonuz Technology DMCC)
4. SDK component list and usage patterns
5. RBAC role matrix (6 roles)
6. Gas wording rules
7. Naming conventions
8. Business model
9. Codebase architecture maps
10. All infrastructure partners

---

## PART 7: THE BONUZ PRODUCT UNIVERSE — COMPLETE MAP

### What Exists Today (Proven, Shipped)

```
PROTOCOLS (Bonuz Inc.)
├── bonuz ID Protocol (BonuzSocialId.sol) — 4 chains, Hacken 10/10
├── bonuz Engagement Protocol (BonuzTokens.sol + BonuzDynamicNft.sol) — Base + Polygon
└── Account Abstraction (Biconomy v4 + Web3Auth)

PRODUCTS (Bonuz Technology DMCC)
├── bonuz: Lifestyle Wallet — App Store, 44+ chains, 24 languages
├── bonuz.id — Web profiles, social identity, biolinks
├── Brand Dashboard — No-code campaigns, DNFTs, analytics, 6-role RBAC
├── @bonuz/sdk — React components + hooks for Bonuz ID integration
└── bonuz.xyz — Marketing site, 60+ languages, 19 satellite components
```

### What Can Be Built Today (With Existing Code)

```
IMMEDIATE (hours, no code)
├── Event check-in system (dashboard)
├── Loyalty punch card program (dashboard)
├── Membership program (dashboard)
├── Quest/challenge campaign (dashboard)
├── Brand migration for physical business (dashboard + QR)

FAST (days, some code)
├── SDK integration into existing React dApp/website
├── Custom web profile embed (REST API)
├── Barebone white-label (Tier 0): login + scan + 1 use case
├── New marketing satellite page

MEDIUM (1-2 weeks, significant code)
├── Branded engagement app (Tier 1)
├── Project migration (Web2/Web3 → bonuz)
├── Embeddable profile widget
├── New DNFT type

LONGER (2+ weeks)
├── Full white-label wallet (Tier 2)
├── NavBar / Web SDK
├── New protocol features
```

### What's Coming (Future Roadmap)

```
NEAR TERM (next 3-6 months)
├── Migration Protocol (formalized)
├── White-label Tier 0 template (standardized)
├── NavBar / Web SDK
├── Embeddable profile widgets
├── Commerce integration v1

MID TERM (6-12 months)
├── Social Oracle v1
├── AI Agent SDK
├── Outcome scoring engine
├── Programmable journey builder
├── $BONUZ token

LONG TERM (12-24 months)
├── Social Continuation Layer Protocol
├── Universal human interface layer
├── AI agent ecosystem
├── Cross-ecosystem interoperability at scale
```

---

## PART 8: AUDIENCE TRANSLATION GUIDE

The same bonuz system described for different audiences:

### For a Coffee Shop Owner
> "bonuz lets you launch a digital punch card and loyalty program in under an hour. No app to build. Your customers scan a QR code, get a digital card in their wallet, and you track everything from a dashboard. It's like Square Loyalty but on-chain and portable."

### For a Web3 Project Migrating
> "bonuz Migration Protocol upgrades your project to modern infrastructure: on-chain identity via bonuz ID, DNFT-based engagement assets with state machine, account abstraction for gasless UX, and a no-code dashboard for your team. Your existing holders can be gated in without minting new assets."

### For a Government Digital Services Team
> "bonuz provides programmable digital identity and participation infrastructure. Citizens get portable, privacy-respecting identity. Services get attestation-based access control. Everything is auditable, permissioned, and built on audited open infrastructure."

### For an Enterprise Brand
> "bonuz is outcome-oriented engagement infrastructure. Deploy loyalty, memberships, quests, and access systems through a no-code dashboard. Your customers interact through a simple app — no blockchain knowledge needed. Everything is measurable: participation, redemption, retention, progression."

### For a VC / Investor
> "bonuz is the Human Layer: a protocol + distribution system that monetizes through SaaS (brand dashboard), enterprise (white-labels), and protocol fees (DNFT issuance/redemption). Network effects compound as every brand campaign grows the shared identity graph. 44+ chains, Hacken 10/10, live on App Store."

### For a Developer
> "bonuz gives you `@bonuz/sdk` (npm) with React components for Bonuz ID integration. `<SignIn>` for Web3Auth login, `useBonuzSocialId()` for on-chain profile data, BonuzTokens contract for minting DNFTs. Gasless via Biconomy paymasters. Base chain primary, Polygon available."

---

## PART 9: CRITICAL FINDINGS FROM DEEP CODEBASE RESEARCH

### Things That Make Fast Building Possible

1. **Feature flags are compile-time only** — Currently only 2 flags (`TRANSACTION_PREVIEW_ENABLED`, `CROSS_CHAIN_SWAPS_ENABLED`). For white-labels, this system needs expansion. Add flags for: wallet visibility, DApp browser, AI features, discover section, buy/sell, swap.

2. **Theme system is production-ready** — 3 themes exist (light, dark, funky). Adding a brand theme is straightforward: add new `ThemeTokens` object in `store/theme.ts`, modify `baseColors`.

3. **Route groups are cleanly separated** — `(ai)`, `(discover)`, `(browser)` can be deleted entirely with no impact on core flows. `(wallet)` can be reduced to view-only.

4. **Login flow is 953 lines but well-structured** — `useWeb3AuthLogin.ts` handles the full auth pipeline. For white-labels, the same flow works — just change Web3Auth client ID and Biconomy config.

5. **QR parsing is minimal and reusable** — `parseBonuzQrCode()` in `lib/parse-bonuz-qr.ts` handles all QR formats. Just 50 chars regex validation.

6. **Dashboard is genuinely no-code** — Brand can go from zero to first DNFT in ~40 minutes without touching code. 22 real-world + 12 digital categories. Canvas-based DNFT design.

7. **Smart contracts are minimal surface area** — `mint()` takes 9 params. `setUserProfile()` takes 4 params. Clean, audited, simple to integrate.

8. **Backend API is RESTful and documented** — `/api/users/{handle}` for public profiles, `/api/nfts` for minting, `/api/users/auth` for login. Standard JWT auth.

### Things That Need Work for Scale

1. **No runtime feature flag system** — Current flags are compile-time constants. For managing multiple white-labels, need: server-fetched flags, per-build configuration, or a feature flag service.

2. **No white-label build pipeline** — No EAS build profiles for multiple white-labels. Would need: per-brand `app.json`, per-brand env files, CI/CD pipeline for multi-target builds.

3. **SDK is bonuz ID only** — `@bonuz/sdk` v1.1.2 only covers Social ID. Doesn't include: DNFT minting, quest participation, loyalty operations, event check-ins. For full "integrate bonuz into your dApp" story, SDK needs expansion.

4. **No embed/widget system** — No `<iframe>` embeds, no web components, no lightweight profile cards. Building one would take 3-5 days and massively increase integration surface.

5. **No webhook system in dashboard** — Brands can't receive callbacks on events (new check-in, DNFT redeemed, etc.). This limits automation and third-party integration.

6. **Bundle ID is hardcoded** — `market.bonuz.app` in `app.json`. Each white-label needs its own bundle ID, which means either build variants or separate config files.

---

## PART 10: RECOMMENDED IMMEDIATE ACTIONS

### To enable fast white-label shipping:

1. **Expand feature flags** (2 hours) — Add flags for every strippable feature:
   ```typescript
   FEATURE_FLAGS = {
     WALLET_TAB_VISIBLE: true,
     DISCOVER_VISIBLE: true,
     AI_VISIBLE: true,
     BROWSER_VISIBLE: true,
     BUY_SELL_ENABLED: true,
     SWAP_ENABLED: true,
     CROSS_CHAIN_SWAPS_ENABLED: true,
     TRANSACTION_PREVIEW_ENABLED: false,
     FULL_NFT_GALLERY: true,
     SEND_ENABLED: true,
   }
   ```

2. **Create Tier 0 template** (3-5 days) — Fork bonuz-app-v2, strip to barebone, document as starting point for all future minimal white-labels.

3. **Create brand config file** (1 day) — Single `brand.config.ts` that controls: name, colors, features, chains, DNFT types, dashboard partner ID. One file to customize per white-label.

4. **Build embeddable profile widget** (3-5 days) — Lightweight web component that fetches `/api/users/{handle}` and renders a profile card. Massive integration surface expansion.

5. **Expand SDK** (2-4 weeks) — Add DNFT minting, quest hooks, loyalty hooks, event hooks. Make `@bonuz/sdk` the one-stop integration library.

---

## APPENDIX A: CONTRACT ADDRESSES (Quick Reference)

### bonuz ID Protocol (BonuzSocialId.sol)

| Chain | Address |
|-------|---------|
| Base (8453) | `0x9220070245b67130977FdF32acA4acdF6aD163cC` |
| Polygon (137) | `0x178C18Cc348C1b6eBc76d91A61E2D8f840227d28` |
| Arbitrum Nova (42170) | `0x3920F2C479D3C805EB89F2fdC6069dda58f4A734` |
| Core DAO (1116) | `0x9220070245b67130977FdF32acA4acdF6aD163cC` |

### bonuz Engagement Protocol (DNFT)

| Chain | Contract | Address |
|-------|----------|---------|
| Base (8453) | BonuzTokens | `0xf13d5259421D84C56A886a6e4F18814555eEb24c` |
| Base (8453) | BonuzDynamicNft | `0x39056372Eb93F4565d34C693A4A2Ea0D4F7187e5` |
| Polygon (137) | BonuzTokens | `0x2A945B46EE2c6B8BAC319514d5EcdEdf2CBB607b` |

---

## APPENDIX B: MINIMUM INTEGRATION CODE SAMPLES

### Simplest possible bonuz integration (React web app)

```tsx
// 1. Install: npm install @bonuz/sdk wagmi viem
// 2. Wrap your app:
import { SignIn, ConnectButton, useBonuzSocialId } from '@bonuz/sdk'

function App() {
  return (
    <WagmiProvider config={config}>
      <QueryClientProvider client={queryClient}>
        <SignIn />        {/* Login button */}
        <ConnectButton /> {/* Profile dropdown */}
        <MyApp />
      </QueryClientProvider>
    </WagmiProvider>
  )
}

// 3. Read user identity:
function MyApp() {
  const { address } = useAccount()
  const { data } = useBonuzSocialId({ address })
  return <div>Welcome, {data?.handle}</div>
}
```

### Simplest possible DNFT mint (backend/script)

```typescript
import { ethers } from 'ethers'

const contract = new ethers.Contract(
  '0xf13d5259421D84C56A886a6e4F18814555eEb24c', // BonuzTokens on Base
  BONUZ_TOKENS_ABI,
  signer // Must have isIssuer role
)

await contract.mint(
  recipientAddress,  // Who gets it
  'VOUCHER',         // Type: VOUCHER|LOYALTY|MEMBERSHIP|POP|CERTIFICATE
  'Welcome Offer',   // Name
  '10% off first',   // Description
  'ipfs://...',      // Image
  false,             // Soul-bound?
  0,                 // Expiry (0 = never)
  0,                 // Points (for LOYALTY)
  ''                 // Extra metadata JSON
)
```

### Simplest possible public profile fetch (any language)

```bash
curl https://admin.bonuz.xyz/api/users/mende
# Returns: { handle, name, profileImage, bio, points, socialLinks, ... }
```

---

## APPENDIX C: DASHBOARD QUICK-START (Zero Code)

**Time to first DNFT: ~40 minutes**

1. **Sign up** → Connect wallet (5 min)
2. **Create brand** → Name, logo, banner (10 min)
3. **Create event/app** → Title, image, dates, categories (15 min)
4. **Design reward** → Choose type (Voucher/Membership/POP/Loyalty), customize on canvas (5 min)
5. **Get QR code** → Auto-generated, download and print (immediate)
6. **Mint to users** → Click mint, select user, deploy to blockchain (2 min)

**22 real-world categories:** Events, Tech, Food & Drink, AI, Nightlife, Fitness, Crypto, Arts & Culture, Climate, Wellness, Dancing, In-Person Games, Education, Retail, Community, Sports, Music, Networking, Workshops, Travel, Fashion, Outdoors

**12 digital categories:** dApps, DeFi, Games, Buy with Crypto, Quests, Learn, AI Agents, Multiplayer, Launchpad, Donate, Vibe Code, Social, Tools, DAOs, Metaverse, Trading, Staking, Payments, Memes
