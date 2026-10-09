# stellar-scan-web

> Next.js explorer frontend for Stellar Scan — browse contracts, view WASM, simulate function calls, and verify source.

[![Stellar Wave](https://img.shields.io/badge/Stellar%20Wave-Wave%205-blue?style=flat-square)](https://www.drips.network/wave/stellar)
[![Next.js](https://img.shields.io/badge/Next.js-15-black?style=flat-square)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue?style=flat-square)](https://www.typescriptlang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)
[![CI](https://img.shields.io/badge/CI-GitHub%20Actions-green?style=flat-square)](.github/workflows/ci.yml)

---

## Overview

> **Repository status:** this checkout currently contains the frontend project
> documentation only. The actively implemented Next.js application is
> maintained in the companion `coredevdave-cmd/stellar-scan-web` repository.
> Keep screenshots, routes, and deployment claims synchronized with that
> implementation before publishing a release from this repository.

`stellar-scan-web` is the user-facing explorer for the Stellar Scan platform. It allows developers, auditors, and users to:

- **Browse** all indexed Soroban contracts by network, deployer, tag, or function signature
- **Inspect** contract WASM bytecode and decoded function specs
- **Simulate** read functions directly in the browser via `simulateTransaction`
- **Verify** source code status — confirmed WASM hash matches
- **Track** contract event history

---

## File Structure

```
stellar-scan-web/
│
├── package.json
├── tsconfig.json
├── next.config.ts
├── tailwind.config.ts
├── README.md                              # This file
├── CONTRIBUTING.md
├── LICENSE
├── CODEOWNERS
├── .gitignore
├── .env.example
├── .eslintrc.json
├── .prettierrc
│
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                         # Lint, typecheck, build on every PR
│   │   └── deploy.yml                    # Deploy to Vercel/GitHub Pages on main merge
│   └── ISSUE_TEMPLATE/
│       ├── bug_report.md
│       └── stellar_wave_task.md
│
├── public/
│   ├── favicon.ico
│   ├── og-image.png
│   └── robots.txt
│
├── src/
│   ├── app/                               # Next.js App Router
│   │   ├── layout.tsx                     # Root layout with nav and providers
│   │   ├── page.tsx                       # Homepage — featured contracts, search
│   │   ├── not-found.tsx
│   │   │
│   │   ├── contracts/
│   │   │   ├── page.tsx                   # Contract list — filterable, paginated
│   │   │   └── [contractId]/
│   │   │       ├── page.tsx               # Contract detail page
│   │   │       ├── functions/
│   │   │       │   └── page.tsx           # Functions tab with call simulator
│   │   │       └── events/
│   │   │           └── page.tsx           # Events history tab
│   │   │
│   │   ├── deployers/
│   │   │   └── [address]/
│   │   │       └── page.tsx               # Deployer profile — all their contracts
│   │   │
│   │   └── search/
│   │       └── page.tsx                   # Search results page
│   │
│   ├── components/
│   │   ├── contract/
│   │   │   ├── ContractCard.tsx           # Summary card used in listings
│   │   │   ├── ContractHeader.tsx         # Contract ID, deployer, verified badge
│   │   │   ├── ContractTabs.tsx           # Functions / Events / WASM tabs
│   │   │   ├── FunctionList.tsx           # List of decoded functions
│   │   │   ├── CallSimulator.tsx          # Interactive function call form
│   │   │   ├── WasmViewer.tsx             # Hex / decoded WASM display
│   │   │   ├── EventFeed.tsx              # Real-time contract event stream
│   │   │   └── VerifiedBadge.tsx          # Visual source verification status
│   │   │
│   │   ├── explorer/
│   │   │   ├── ContractTable.tsx          # Sortable, filterable contract table
│   │   │   ├── SearchBar.tsx              # Global search with autocomplete
│   │   │   ├── NetworkSwitcher.tsx        # Toggle testnet / mainnet
│   │   │   ├── TagFilter.tsx              # Filter by DeFi, token, NFT, etc.
│   │   │   └── Pagination.tsx             # Cursor-based pagination control
│   │   │
│   │   └── ui/
│   │       ├── AddressPill.tsx            # Formatted, copyable Stellar address
│   │       ├── HashDisplay.tsx            # Truncated hash with copy-to-clipboard
│   │       ├── LoadingSkeleton.tsx        # Skeleton screens for loading states
│   │       ├── ErrorBoundary.tsx
│   │       └── Navbar.tsx
│   │
│   ├── hooks/
│   │   ├── useContract.ts                 # SWR hook for single contract data
│   │   ├── useContractList.ts             # SWR hook for paginated contract list
│   │   ├── useContractEvents.ts           # SWR hook with real-time polling
│   │   ├── useSimulate.ts                 # Hook wrapping the simulate API call
│   │   └── useSearch.ts                   # Debounced search hook
│   │
│   ├── lib/
│   │   ├── api.ts                         # Typed API client (wraps stellar-scan-api)
│   │   ├── stellar.ts                     # @stellar/stellar-sdk helpers
│   │   ├── format.ts                      # Address truncation, ledger formatting
│   │   └── constants.ts                   # Network configs, tag definitions
│   │
│   └── types/
│       ├── contract.ts                    # Contract, ContractFunction, ContractEvent
│       ├── api.ts                         # API response shapes
│       └── stellar.ts                     # Stellar SDK type extensions
│
└── tests/
    ├── ContractCard.test.tsx
    ├── CallSimulator.test.tsx
    ├── SearchBar.test.tsx
    └── useSimulate.test.ts
```

---

## Pages

### `/` — Homepage
Featured contracts, network stats (total contracts indexed, verified count), and a prominent search bar.

### `/contracts` — Contract Browser
Filterable table of all indexed contracts. Filters: network, verification status, tag, deployer address. Sortable by deploy date, function count.

### `/contracts/[contractId]` — Contract Detail
Three tabs:
- **Functions** — decoded function list with the call simulator
- **Events** — event history with decoded topics and values
- **WASM** — raw hex and decoded spec viewer

### `/deployers/[address]` — Deployer Profile
All contracts deployed by a Stellar address, with aggregate stats.

### `/search` — Search Results
Full-text search results across contract IDs, function names, tags, and documentation strings.

---

## Environment Variables

```
NEXT_PUBLIC_API_URL=http://localhost:3002
NEXT_PUBLIC_STELLAR_RPC_URL=https://soroban-testnet.stellar.org
NEXT_PUBLIC_NETWORK=testnet
```

---

## Running Locally

```bash
git clone https://github.com/stellar-scan/stellar-scan-web
cd stellar-scan-web
cp .env.example .env.local
npm install
npm run dev
```

App available at `http://localhost:3000`.

---

## Stellar Wave — Open Issues

Browse: [github.com/stellar-scan/stellar-scan-web/issues](https://github.com/stellar-scan/stellar-scan-web/issues?q=label%3A%22Stellar+Wave%22)

**Points:** Trivial = 100 pts | Medium = 150 pts | High = 200 pts

Full rules: [docs.drips.network/wave/terms-and-rules](https://docs.drips.network/wave/terms-and-rules)

---

## License

MIT — see [LICENSE](LICENSE)
