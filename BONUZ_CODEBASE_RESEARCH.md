# BONUZ CODEBASE RESEARCH — Deep Technical Findings

> This document captures detailed technical findings from analyzing all 6 bonuz repositories.
> Used as source material for evolving the Master Context Prompt.
> Last updated: 2026-03-11

---

## 1. bonuz-app-v2 (Consumer Mobile App)

### Identity
- **Name:** bonuz-wallet-expo (package.json)
- **Version:** 3.12.77
- **Framework:** Expo SDK 55 + React Native 0.83.2 (New Architecture)
- **TypeScript:** 5.8.3, strict mode
- **Routing:** Expo Router (file-based, route groups)

### State Management
- **Zustand 5.0.10** — selector hooks pattern (CRITICAL: never destructure from `useXStore()`)
- **TanStack React Query 5.90.16** — server state
- **Apollo Client 4.1.3** — GraphQL for Social ID
- **MMKV** — fast local persistence (non-sensitive)
- **expo-secure-store** — encrypted secrets (iOS Keychain / Android Keystore)

### Blockchain Stack
- **Viem 2.38.3 + Wagmi 2.19.5** — EVM interaction
- **Biconomy Account SDK 4.5.7** — ERC-4337 smart accounts
- **Web3Auth React Native SDK 8.1.0** — MPC key management + social login
- **WalletConnect 2.23 + ReOwn AppKit 2.0.1** — dApp connections
- **@solana/web3.js 1.89.1** — Solana
- **bitcoinjs-lib 7.0.1** — Bitcoin (BIP32/BIP39/BIP44)

### Supported Chains (44+ from constants/networks.ts)
```
Ethereum (1), BSC (56), Polygon (137), Aurora (1313161554), Arbitrum (42161),
Arbitrum Nova (42170), Avalanche (43114), Optimism (10), Base (8453),
Bitcoin (-1), Solana (101), Linea (59144), HyperEVM (999), Abstract (2741),
ApeChain (33139), Berachain (80094), Blast (81457), Celo (42220),
Degen (666666666), Fantom (250), Gnosis (100), Gravity (1625), Ink (57073),
Katana (747474), Lens (232), Mantle (5000), Monad (143), Plasma (9745),
Polygon zkEVM (1101), Scroll (534352), Somnia (5031), Sonic (146),
Soneium (1868), Unichain (130), World Chain (480), XDC (50),
Zero Network (543210), zkSync (324), Zora (7777777), MegaETH (4326)
```

### Route Groups
```
(tabs)     → Home, Profile, Scan, Wallet
(wallet)   → buy, sell, connect, token details, security audit log, network manager
(discover) → events, apps, partners, digitalWorld, realWorld
(browser)  → Built-in dApp browser
(ai)       → AI features + leaderboard
(settings) → account, app-settings, security, help-legal
(common)   → notifications
```

### Key Services Architecture
```
services/
├── auth/AuthManager.ts         — Token lifecycle, refresh, device tracking
├── auth/TokenSyncService.ts    — Atomic token updates
├── auth/Web3AuthService.ts     — Web3Auth instance management
├── auth/preCaptureOldToken.ts  — v1→v2 migration (MUST be first import)
├── backend/auth.service.ts     — Login endpoints
├── backend/user.service.ts     — Profile, connections
├── backend/wallets.service.ts  — Balances, NFTs, transactions
├── backend/loyalty.service.ts  — Punch cards, points
├── backend/events.service.ts   — Event management
├── backend/swap.service.ts     — Swap aggregation
├── blockchain/bonuz/           — Social ID contracts + subgraphs
├── blockchain/bitcoin/         — BitcoinService
├── wallet/WalletOrchestrationService.ts
├── wallet/WalletRecreationService.ts
├── wallet/creators/Web3AuthWalletCreator.ts
├── wallet/creators/SeedPhraseWalletCreator.ts
├── passkit/passkit.service.ts  — Apple Wallet
├── security/                   — Secure key management
└── core/ServiceContainer.ts    — DI container
```

### Wallet Types
1. **Main EVM** — derived from Web3Auth private key
2. **Smart Wallet** — Biconomy AA with gasless
3. **Seed Phrase** — HD derivation per chain (BIP44)
4. **Connected** — WalletConnect external wallets
5. **Watch-only** — Monitor addresses

### RPC Resilience
- `lib/rpc/RpcManager.ts` — Multi-RPC fallback orchestration
- `lib/rpc/CircuitBreaker.ts` — Rate limiting and failure handling
- Free vs paid tier endpoints configured per chain
- ALWAYS use `executeWithFallback()` for reads
- NEVER use fallback for tx broadcasts (risk of duplicates)

### Bonuz Protocol Config (in-app)
```typescript
SOCIAL_ID_ADDRESS = '0x9220070245b67130977FdF32acA4acdF6aD163cC'  // Base
SOCIAL_ID_CHAIN_ID = 8453  // Base mainnet
SOCIAL_TOKENS_ADDRESSES = [
  '0xe6461B0C86bF85023a5f359BAdF7012d2ef5ad7e',  // BonuzTokens on Base
  '0x39056372Eb93F4565d34C693A4A2Ea0D4F7187e5',  // BonuzDynamicNft on Base
]
SUBGRAPH_URL = 'https://api.goldsky.com/.../bonuz-social-id-base-mainnet/v0.0.2-new-version/gn'
```

### Analytics Stack
- **Sentry 7.13** — error tracking (PII filtered)
- **PostHog 4.24** — product analytics
- **AppsFlyer 6.17** — attribution
- **Firebase** — push notifications + analytics

### Testing
- **Vitest 4.0.17** — unit/integration
- **Maestro** — E2E (critical journeys, smoke tests)
- E2E triggers: 2+ of auth boundary, native modules, 3+ screens, money/trust, broke in prod

### Environment Profiles
- development → Local .env
- staging → bonuz-admin-staging-*.run.app (TestFlight)
- production → admin.bonuz.xyz (App Store)

---

## 2. bonuz-monorepoo (Core Platform)

### Structure
```
bonuz-monorepoo/
├── apps/bonuz-id/         — Consumer web app (bonuz.id) — Next.js 16 + React 19
├── apps/app-bonuz-xyz/    — Business dashboard
├── packages/sdk/          — @bonuz/sdk v1.1.2
├── packages/protocol/     — Smart contracts (Solidity 0.8.17 + Hardhat)
├── turbo.json + pnpm-workspace.yaml
```

### Smart Contracts

**BonuzSocialId.sol (0.8.17):**
- Profile management: name, handle, profileImage
- Social link management with platform validation
- Role-based access: Owner → Admins → Issuers
- Key functions: setUserProfile, setSocialLink, setSocialLinks, getUserProfileAndSocialLinks
- Username uniqueness, IPFS integration, Pausable

**BonuzTokens.sol (0.8.17):**
- ERC-721 with dynamic attributes
- Soul-bound support (ERC-5192)
- Token types: VOUCHER, LOYALTY, CERTIFICATE, MEMBERSHIP, POP
- Metadata struct: issuer, tokenType, name, desc, imageURL, isSoulBound, redeemDate, expiryDate, points, metadataJson
- Functions: mint, addLoyaltyPoints, removeLoyaltyPoints, redeemVoucher, locked
- On-chain metadata generation with Base64 encoding

### Contract Deployments
```
Base (8453):
  BonuzSocialId:   0x9220070245b67130977FdF32acA4acdF6aD163cC
  BonuzTokens:     0xe6461B0C86bF85023a5f359BAdF7012d2ef5ad7e
  BonuzDynamicNft: 0x39056372Eb93F4565d34C693A4A2Ea0D4F7187e5

Polygon (137):
  BonuzSocialId:   0x178C18Cc348C1b6eBc76d91A61E2D8f840227d28
  BonuzTokens:     0x2A945B46EE2c6B8BAC319514d5EcdEdf2CBB607b

Arbitrum Nova (42170):
  BonuzSocialId:   0x3920F2C479D3C805EB89F2fdC6069dda58f4A734

Core (1116):
  BonuzSocialId:   0x9220070245b67130977FdF32acA4acdF6aD163cC
```

### @bonuz/sdk v1.1.2
- Components: BonuzSocialId, SignIn, ConnectButton, UserDetails, CreateSocialId
- Hooks: useSocialId()
- Dependencies: Web3Auth (no-modal), Biconomy v4, Wagmi 2, Viem 2, TanStack React Query

### Loyalty System (Implemented)
- **Points:** Start at 0, add/remove 1–10,000 per tx
- **Punch cards:** Fixed max_punches, tracks used, auto-mint voucher on completion
- Redeemer authorization: user-based (not partner-based)
- Validation: immutable max_punches/entity_name/entity_id post-mint

### API Endpoints (bonuz-id app)
```
POST /auth/signin, /auth/signup, /auth/refresh
GET  /api/users/me, /api/users/{handle}
PATCH /api/users/me, /api/users/me/link
GET  /api/users/me/roles-permissions
POST /api/loyaltyRequests/redeemer-action
POST /:partnerId/events/:eventId/mint-nft
POST /:partnerId/events/:eventId/mint-nft-for-multiple-users
POST /:partnerId/apps/:appId/mint-nft
```

---

## 3. BonuzDashboard (Brand Platform)

### Stack
- Next.js 16.1.6 + React 19.2.4 + TypeScript 5
- Zustand 5 (sessionStore, userStore, appStore)
- TanStack React Query 5.90 + Apollo Client 3.8
- CASL 6.7 for RBAC
- Tailwind CSS 4 + DaisyUI 5.5
- Konva + react-konva for canvas NFT ticket design
- React Hook Form 7 + Yup + Zod
- ethers.js 5.7 + NFT.storage
- Vitest 4.0 for testing

### RBAC (6 Roles)
| Role | Capabilities |
|------|-------------|
| Creator | Full control + role management |
| Admin | Full access minus role management |
| Collaborator | Edit, manage rewards, check-in, mint |
| Viewer | Read-only + analytics |
| Validator | Verify actions, approve submissions |
| Redeemer | View + redeem rewards |

### Brand-Level Permissions (CASL)
- Actions: manage, create, read, update, delete, mint, assignRole, removeRole
- Subjects: Partner, Event, App
- Combos: Brand Admin, Brand Collaborator, Brand Redeemer, Brand Viewer

### Dashboard Features
- Event creation wizard (multi-step, capacity, waitlist, registration modes)
- Challenge/quest builder with scoring + leaderboards
- Loyalty programs (punch cards + points cards)
- Canvas-based NFT ticket rendering (Voucher, POP, Membership, Loyalty, Badge)
- Single + batch NFT minting
- Token gating for apps (contract address + amount)
- Analytics: mints by entity/month/type, check-in tracking, registration stats
- QR code generation + public scanning endpoints

### API Surface
- Dashboard endpoints: `/api/Events/dashboard`, `/api/apps/dashboard`, `/api/partners/dashboard/:id/mint-statistics`
- Public endpoints: `/api/Events` (QR scanning), `/api/apps` (QR scanning)
- Role management: POST/DELETE per event/app + user + role
- GraphQL: Goldsky subgraph for Social ID

---

## 4. bonuz-xyz (Marketing Site)

### Stack
- Next.js 16.1.1 + React 19.2.3 + TypeScript 5.9.3
- Tailwind CSS 4 + shadcn/ui + Radix UI
- next-intl 4.7 (60+ languages, 58 translation files)
- Motion 12 (Framer Motion) + AOS + Lottie + Spline 3D + canvas-confetti
- Sharp 0.34.5 for image optimization

### Satellite Pages (10)
bitcoin-wallet, ethereum-wallet, solana-wallet, bnb-chain-wallet, base-wallet,
meme-coin-wallet, self-custodial-wallet, defi-wallet, stablecoin-wallet, crypto-for-beginners

### 19 Reusable Satellite Components
SatelliteHero, SatelliteSecurity, SatelliteFeatureOverview, SatelliteComparisonTable,
SatelliteFAQ, SatelliteDownloadCta, SatelliteEmailSignup, SatelliteLanguageShowcase,
SatelliteHowItWorks, SatelliteKeyBenefits, SatelliteTrustStrip, SatelliteSocialProof,
SatelliteLegalDisclaimer, SatelliteFooter, SatelliteStickyMobileCTA,
SatelliteLanguageSwitcher, SatelliteStoreButtons, SatelliteInternalLinks

### SEO
- JSON-LD: Organization, SoftwareApplication, FAQPage, BreadcrumbList, SiteNavigationElement
- Sitemaps per locale, hreflang alternates, GA4, Microsoft Clarity
- Trust signals: Hacken 10/10, Halborn, NCC Group, SOC 2 Type II

### API Routes
- POST /api/subscribe — Mailjet email subscription
- GET /api/sitemap-index — Dynamic sitemap per locale

---

## 5. superpowers032026 (Dev Workflow)

- **Version:** 5.0.0 (2026-03-09)
- **Purpose:** Composable workflow skills for AI coding agents
- **14 core skills:** using-superpowers, brainstorming, writing-plans, subagent-driven-development, executing-plans, test-driven-development, systematic-debugging, verification-before-completion, requesting-code-review, receiving-code-review, using-git-worktrees, finishing-a-development-branch, dispatching-parallel-agents, writing-skills
- **Workflow:** Brainstorm → Plan → Implement (subagent per task) → Review → Merge
- **Key rules:** TDD mandatory, no code before design approval, evidence before claims

---

## 6. marketingskills032026 (Marketing AI Skills)

- **Version:** 1.1.0 (2026-02-27)
- **32 marketing skills** across 7 categories (SEO, CRO, Content, Paid, Growth, Strategy, Sales)
- **51 CLI tools** for marketing automation
- **50+ integrations** (GA4, Stripe, HubSpot, Mailchimp, etc.)
- **Foundation:** product-marketing-context skill (all skills reference)
- Fork of Corey Haines' Marketing Skills, customized for bonuz operations

---

## Cross-Repo Patterns

### Shared Tech Choices
- **Zustand** for state management (all apps)
- **TanStack React Query** for server state (all apps)
- **Web3Auth** for authentication (all apps)
- **Biconomy** for account abstraction (mobile + dashboard + bonuz-id)
- **TypeScript strict mode** everywhere
- **Tailwind CSS** for styling (all apps, NativeWind for mobile)

### Shared Blockchain Config
- **Primary chain:** Base (8453)
- **Social ID chain:** Base mainnet
- **Subgraph:** Goldsky on Base mainnet
- **Contract addresses** consistent across repos

### Shared Patterns
- Selector hooks for Zustand (prevents re-renders)
- React Hook Form for forms
- CASL for authorization
- Vitest for testing
- Circuit breaker for RPC resilience (mobile)
