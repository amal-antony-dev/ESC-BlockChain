# ESC-BlockChain

A small Express service that holds the **treasury wallet** for **[EcoSwapChain](https://github.com/amal-antony-dev/ecoswapchain)** and sends **SwapCoin** (the platform's SPL token) to users on Solana. Keeping the treasury key in one service means the Django backend and the browser never handle it.

> **Status:** part of my final-year college project (2025). Runs against Solana **devnet**.
>
> Case study: [amal-antony-dev.vercel.app](https://amal-antony-dev.vercel.app/#work/ecoswapchain/overview)

## Part of EcoSwapChain

| Repo | Role |
|---|---|
| [ecoswapchain](https://github.com/amal-antony-dev/ecoswapchain) | Django API, Celery workers, websockets. **Start here for full setup.** |
| [ESC-Frontend](https://github.com/amal-antony-dev/ESC-Frontend) | React + TypeScript web app; mints and transfers item NFTs with the user's own wallet |
| **ESC-BlockChain** | This repo: treasury service |
| [Sustainability-Prediction](https://github.com/amal-antony-dev/Sustainability-Prediction) | Sustainability-scoring model |

## API

Every request needs `Authorization: Bearer <JWT>`, signed with HS256 using the `JWT_SECRET` shared with the Django backend.

| Method | Path | What it does |
|---|---|---|
| `GET` | `/ping/` | Checks the token and returns the caller |
| `GET` | `/token/reward/:publicKey/:amount` | Transfers SwapCoin from the treasury to `publicKey`, creating the recipient's token account if needed, and returns the transaction signature. *The current code always sends 100 and ignores `:amount`.* |

`src/Token/SolDrop.js` also has a helper that sends devnet SOL from the treasury so new wallets can pay fees.

## Run it

Requires Node 18+, a Solana devnet keypair for the treasury, and an SPL token mint (see the [ecoswapchain README](https://github.com/amal-antony-dev/ecoswapchain#1-solana-devnet) for creating both).

```bash
cd MiddleLayer
cp .env.example .env     # then fill it in
npm install
npm install @solana/spl-token   # imported by src/Token/tokenCredit.js but missing from package.json
npx nodemon index.js     # http://localhost:3000
```

| Variable | Meaning |
|---|---|
| `token_mint_address` | SwapCoin SPL token mint |
| `devnet_url` | Solana RPC, e.g. `https://api.devnet.solana.com` |
| `treasury_wallet_path` | Path to the treasury keypair JSON |
| `JWT_SECRET` | Shared with the Django backend |
| `helius_url`, `merkle_tree_address` | Only for compressed-NFT experiments; unused by the routes above |

Never commit `.env` or the keypair file.

## Tech

Node.js · Express · `@solana/web3.js` · `@solana/spl-token` · jsonwebtoken · dotenv · morgan
