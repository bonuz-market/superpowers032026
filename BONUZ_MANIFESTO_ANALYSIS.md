# BONUZ MANIFESTO — Vision Analysis & Reality Map

> Source: Founder's strategic manifesto (March 2026)
> Analysis: Cross-referenced against all 6 codebases
> Purpose: Ground the vision in what's built, identify gaps, inform v2 Master Context

---

## The Manifesto (Stored Verbatim)

### Core Definition
> bonuz is a modular human-centered infrastructure layer that transforms identity, intention, and engagement into measurable outcomes.

### Category Claim
> Not a wallet, loyalty platform, or Web3 app. Those are product expressions of a broader architecture. The core bonuz category is the **Human Layer**.

### Core Thesis
> Most digital systems optimize for activity, visibility, or feature usage. bonuz is designed to optimize for **outcomes**.

### 7 Design Principles
1. Outcomes over activity
2. Simplicity over complexity
3. Modularity over rigidity
4. Identity as the anchor
5. Interoperable value flows
6. Configurable systems with low friction
7. Human agency strengthened, not replaced

### Short Definition
> bonuz = the Human Layer. A modular infrastructure that turns identity, intention, and interaction into measurable outcomes across people, communities, brands, and future intelligent systems.

---

## Reality Map: What Exists vs. What's Vision

### BUILT & WORKING (Proven in Code)

| Manifesto Concept | What's Actually Built | Where |
|---|---|---|
| **Identity as the anchor** | BonuzSocialId.sol on 4 chains, @bonuz/sdk with `<BonuzSocialId>`, bonuz.id web app, Apple Wallet pass | bonuz-monorepoo, bonuz-app-v2 |
| **Simplicity over complexity** | Email/social login → self-custodial wallet created behind the scenes (Web3Auth MPC + Biconomy AA) | bonuz-app-v2 |
| **Programmable engagement** | DNFT protocol: passes, vouchers, loyalty, memberships, tickets, PoV/PoP with state machine | bonuz-monorepoo (BonuzTokens.sol, BonuzDynamicNft.sol) |
| **Configurable without technical burden** | Brand dashboard with no-code campaign builder, event wizard, quest builder, NFT ticket canvas designer | BonuzDashboard |
| **Real-world activations** | QR/NFC scanning, check-ins, redemption flow, geolocation events | bonuz-app-v2 (scan tab), BonuzDashboard |
| **Loyalty & rewards** | Points system + punch cards with auto-mint voucher, leaderboards, challenge scoring | bonuz-monorepoo, BonuzDashboard |
| **Modularity** | Route groups in mobile app, feature flags, 44+ chain configs, modular Zustand stores | bonuz-app-v2 |
| **Interoperable value flows** | Cross-chain swaps (LI.FI, 1inch), fiat on/off-ramp (Mercuryo), multi-chain wallet | bonuz-app-v2 |
| **Verified participation** | DNFT state machine (issued→active→redeemed/expired→archived), attestations via bonuz ID Protocol | bonuz-monorepoo |
| **Brand-side outcome systems** | 6-role RBAC, analytics (mints, redemptions, check-ins, leaderboards), templates (Events, F&B, Retail, Entertainment) | BonuzDashboard |
| **Portable identity** | Bonuz ID works across consumer app, bonuz.id web app, dashboard, white-labels | All repos |
| **Consumer wallet** | Full lifestyle wallet: 44+ chains, dApp browser, discover, send/receive/swap/buy/sell | bonuz-app-v2 |

### PARTIALLY BUILT / IN PROGRESS

| Manifesto Concept | Current State | Gap |
|---|---|---|
| **White-label apps** | Architecture supports it (feature flags, theming, route groups) but no shipped white-label yet confirmed in code | Need first white-label build to validate the extraction pattern |
| **SDK for third-party integration** | @bonuz/sdk v1.1.2 exists with components, but only for Bonuz ID integration. Not yet a full "Human Layer SDK" | SDK needs expansion: DNFT integration, quest API, engagement hooks |
| **NavBar / Web SDK** | Mentioned as "coming next" — no code found in repos | Needs design + implementation |
| **Habit formation / habit loops** | Points mining with time-based efficiency + levels exists in (ai) route | Basic mechanics exist, but not a configurable habit-loop framework |
| **Reputation system** | Attestations exist in bonuz ID Protocol (visits, achievements) | Not yet a composable reputation score or portable reputation layer |
| **Memberships as a system** | MEMBERSHIP DNFT type exists, soul-bound support works | No subscription billing integration, no recurring membership management in dashboard |
| **AI-linked actors** | (ai) route group with leaderboard in mobile app | Very early — no AI agent integration or machine-to-human coordination |

### FUTURE VISION (Not Yet Built)

| Manifesto Concept | Status | Notes |
|---|---|---|
| **Social Oracle** | Described in v1 Master Context as future | Permissioned discovery/matching layer. No code exists yet. Depends on sufficient identity + engagement data |
| **Social Continuation Layer Protocol** | Founder mentioned as upcoming | No specs or code found |
| **Outcome optimization engine** | Core thesis of manifesto | The infrastructure exists (identity + engagement + attestations) but no "outcome scoring" or "outcome tracking" system yet |
| **AI agent integration** | Manifesto mentions "future AI-linked or machine-assisted actors" | No agent SDK, no machine-readable API for autonomous actors |
| **Commerce-linked engagement** | Mentioned in manifesto | No payment/commerce integration beyond fiat on-ramp (Mercuryo) and gift cards (Bitrefill) |
| **$BONUZ token** | Future, administered by Bonuz Inc. | No token contract deployed |
| **Universal human interface layer** | Long-term vision statement | Aspirational framing of where the Human Layer goes |
| **Programmable user journeys** | Quests exist as linear sequences | No visual journey builder, no branching logic, no conditional flows |

---

## Key Insight: The Manifesto Reframes What Already Exists

The manifesto doesn't describe a new product to build. It describes a **better way to understand what bonuz already is**.

What's powerful: the codebase already has the core primitives:
- **Identity** → BonuzSocialId (on-chain, permissioned, portable)
- **Engagement assets** → DNFTs (stateful, programmable, anti-fraud)
- **Execution** → Account abstraction (gasless for core actions)
- **Distribution** → Consumer app + dashboard + white-label architecture
- **Modularity** → Feature flags, route groups, configurable chains

The manifesto elevates these from "crypto features" to **"outcome infrastructure"** — which is the right framing for brands, investors, and non-crypto audiences.

---

## What Changes for the v2 Master Context

Based on this manifesto, the v2 should:

1. **Lead with outcomes, not features** — Reframe Section 1 from "Human Layer between blockchains and people" to "infrastructure for human outcomes" (more powerful, less crypto-native)

2. **Add the 7 design principles** — These are foundational and should be in the prompt

3. **Separate "what's built" from "what's coming"** — More honest, more useful for AI agents building things. The manifesto blurs this line; the prompt should be precise.

4. **Add the three-sided value model** — Individual / Community / Brand value framing is cleaner than current product-only structure

5. **Position Web3 as enabling rails, not the story** — "Blockchain and Web3 components are enabling rails, not the primary story" is a better framing than current v1

6. **Add future roadmap section** — Social Oracle, Social Continuation Layer, outcome optimization, AI agent integration, commerce-linked engagement, programmable journeys

7. **Keep all technical precision** — Contract addresses, tech stacks, SDK components, chain support — this is what makes the prompt actionable for building

8. **Don't overcomplicate** — Per founder's instruction. The manifesto is vision. The prompt is building instructions. Keep it grounded.

---

## Recommended v2 Structure

```
1. Strategic Definition (manifesto-informed, concise)
2. Entities & Domains (unchanged)
3. Design Principles (NEW — the 7 principles)
4. Human Layer Architecture (refined framing)
5. Protocols — What's Built (technical, precise)
6. Products — What's Shipped (technical, precise)
7. Infrastructure & Partners (unchanged)
8. Value Model (NEW — Individual / Community / Brand)
9. Codebase Architecture (unchanged)
10. Roadmap — What's Coming (NEW — Social Oracle, SCL, AI, commerce, journeys)
11. Business Model (unchanged)
12. Tone & Writing Rules (updated with manifesto language)
13. Reusable Snippets (updated)
14. White-Label Technical Brief (unchanged)
15. AI Instructions (updated)
```

---

## Founder's Own Words (Best Quotes to Preserve)

- "bonuz is designed to optimize for outcomes."
- "The purpose is to create systems where desired results become easier to initiate, track, verify, reward, and scale."
- "Blockchain and Web3 components are enabling rails, not the primary story."
- "The long-term vision is a universal human interface layer where advanced infrastructure becomes invisible."
- "bonuz should strengthen human intention and decision-making, not replace it."
- "Live infrastructure, not theory."
- "bonuz = the Human Layer. A modular infrastructure that turns identity, intention, and interaction into measurable outcomes."
