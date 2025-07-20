# KPI Bond Escrow

> **Performance‑bond smart‑contracts + Node.js orchestrator**  
> Stake FUZE, lock against KPIs, auto‑release on success or **slash & reward stakers** on failure.

[![Solidity 0.8.x](https://img.shields.io/badge/solidity-0.8.x-blue)](https://docs.soliditylang.org/) 
[![Node.js 20+](https://img.shields.io/badge/node-20%2B-brightgreen)](https://nodejs.org/) 
[![Tests](https://github.com/fuze-ac/fuze-kpi-bond-escrow/actions/workflows/ci.yml/badge.svg)](./actions)

---

## 🧩  Repo Contents

| Path / Package              | Description                                                                  |
|-----------------------------|------------------------------------------------------------------------------|
| `contracts/`                | **`KPIBond.sol`**, `BondFactory.sol`, `SlashVault.sol` – fully audited        |
| `orchestrator/`             | TypeScript service: KPI fetch → on‑chain trigger (`release()`/`slash()`)      |
| `subgraph/`                 | TheGraph schema to index bond events                                         |
| `scripts/`                  | Hardhat deployment + E2E demo scripts                                        |
| `docs/`                     | UML diagrams, API spec, PeckShield audit PDF                                 |

---

## ✨  Key Features

* **Time‑locked FUZE stake** – projects bond tokens as collateral.
* **Milestone or metric KPIs** – JSON spec (volume, TVL, GitHub tag, API feed).
* **Auto‑slash** – failed KPI triggers `slash()`; sends 80 % to YieldVault, 20 % to treasury.
* **Multi‑sig override** – guardian can pause in edge‑cases.
* **Orchestrator** – Node.js job polls KPIs, posts proofs, and calls release/slash.

---

## 🚀  Quick Start (Testnet)

### 1 Clone & Install

```bash
git clone https://github.com/fuze-ac/fuze-kpi-bond-escrow.git
cd fuze-kpi-bond-escrow
pnpm i
````

### 2 Deploy Contracts

```bash
npx hardhat deploy --network sepolia
```

Outputs addresses to `deployments/sepolia/*.json`.

### 3 Seed a Bond

```bash
npx hardhat run scripts/createBond.ts --network sepolia \
  --projectAddr 0xProject \
  --kpiURI ipfs://QmKPI.json \
  --amount 500000e18 \
  --duration 180
```

### 4 Run Orchestrator

```bash
cd orchestrator
cp .env.sample .env            # set RPC, PRIVATE_KEY
pnpm ts-node src/index.ts
```

Service checks KPI oracle every 10 min → calls `release()` or `slash()` automatically.

---

## 🏗️  KPI JSON Schema

```json
{
  "name": "Mainnet Launch KPI",
  "deadline": "2025-12-31T23:59:59Z",
  "conditions": [
    { "type": "cex_volume",  "exchange": "binance", "pair": "TOKEN/USDT", "min": 500000 },
    { "type": "github_tag",  "repo": "org/dapp",    "tag": "v1.0.0" }
  ],
  "oracle": "https://oracle.fuze.ac/api/v1/check"
}
```

---

## ⚙️  Orchestrator Config

`.env`

```
RPC_URL=https://base-mainnet.g.alchemy.com/v2/KEY
PRIVATE_KEY=0x...
BOND_FACTORY=0xFactoryAddress
CHECK_INTERVAL=600        # seconds
```

---

## 🔒  Security

* **PeckShield audit** – see `docs/audit-peckshield-2025-04.pdf`.
* Re‑entrancy guards, overflow‑safe math, error codes.
* Guardian multi‑sig can pause bonds in case of oracle failure.

---

## 🧪 Testing

```bash
pnpm test          # Hardhat + chai
pnpm run coverage  # solidity‑coverage
```

Simulated failure → `slash()` distribution verified.

---

## 🤝  Contributing

1. Fork → `feature/*` branch
2. `pnpm lint` (eslint + solhint)
3. Ensure `pnpm test` passes
4. Open PR against `develop`

All contributors sign the CLA.

---

## 📝 License

MIT © 2025 FUZE Foundation

```
::contentReference[oaicite:0]{index=0}
```
