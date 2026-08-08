# TrustLance

> Reputation-weighted, AI-assisted, blockchain-based freelance escrow platform.

## Tech Stack
- **Frontend**: React 18 + Vite, TailwindCSS, Framer Motion, RainbowKit/wagmi
- **Backend**: Node.js + Express, MongoDB/Mongoose, BullMQ
- **Contracts**: Solidity ^0.8.24, Hardhat, Ethers.js v6, OpenZeppelin 5
- **AI**: Anthropic Claude (mock fallback if no key)

## Quick Start (Local Dev)

### Prerequisites
- Node.js 20+
- MongoDB running locally (`mongod`)
- Git

### 1. Install Dependencies
```bash
npm install
npm install --workspace=client
npm install --workspace=server
npm install --workspace=contracts
```

### 2. Configure Environment
```bash
cp .env.example server/.env
cp .env.example contracts/.env
cp .env.example client/.env.local
# Edit each file with your values (all external APIs are optional — mocks built in)
```

### 3. Start Local Blockchain
```bash
# Terminal 1
npm run dev:chain
```

### 4. Deploy Contracts
```bash
# Terminal 2
npm run deploy:local
```

### 5. Start Backend + Frontend
```bash
# Terminal 3
npm run dev
```

### 6. Run Demo Seed Script
```bash
# Terminal 4
npm run seed
```

The seed script runs the full demo flow:
1. Creates two test users (client + freelancer)
2. Client creates a project + milestone
3. AI optimizes scope → both parties sign
4. Client funds escrow (Invoice #1 generated)
5. Freelancer submits partial work (pinned to IPFS mock)
6. Dispute triggered
7. AI verdict with evidence citations
8. Split payout (Invoice #2 generated)
9. Reputation SBT updated
10. Invoice QR verification URL printed

## Project Structure
```
/client       → React frontend (Vite)
/server       → Express backend
/contracts    → Solidity smart contracts (Hardhat)
```

## Contract Architecture
```
EscrowFactory → deploys Escrow per project
Escrow        → milestone state machine + fund custody
ReputationSBT → soulbound reputation token (write-gated to Escrow)
StakingBond   → anti-ghosting deposit (slash on bad faith)
BanRegistry   → on-chain fraud blacklist
```

## Milestone States
```
Created → Signed → Funded → InProgress → Submitted →
  ├─ Approved → Released
  ├─ AutoReleased (72hr timer)
  ├─ Disputed → AI_Review → Resolved (proportional split)
  └─ Cancelled → Refunded
```

## Security Notes
- No admin backdoor can move funds directly
- All reputation writes gated to Escrow contract
- AI verdicts always show reasoning in UI (never just a number)
- Invoices only generated for settled on-chain transactions
- QR/barcode payloads: IDs, hashes, URLs only — never private data
- All PDF/invoice generation server-side only

## Environment Variables
See `.env.example` for all variables. External APIs (AI, IPFS, email) have mock fallbacks for local development.
