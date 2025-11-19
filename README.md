<div align="center">

# Blockchain Development Advanced

Hands-on reference repo showcasing two fully working Ethereum development workstreams—one pure Foundry stack focused on NFT raffles and one hybrid Hardhat/Foundry stack for a micro-scholarship dispenser.

</div>

---

## Repository Overview

| Path | Toolkit | Summary |
| --- | --- | --- |
| `Foundry-Toolchain/` | Foundry (Forge, Anvil, Cast) | RaffleHouse + TicketNFT contracts with Solidity-only scripts and tests. |
| `Regular Exam 01.06/` | Hardhat + Foundry | Micro-scholarship dispenser using Merkle proofs, Chainlink price feeds, minimal proxy factory, JS + Solidity tests. |

Both subprojects are independent; you can work inside either directory without touching the other.

---

## Prerequisites

- Node.js ≥ 18 and npm (for the Hardhat workspace)
- Foundry toolchain (`foundryup`, `forge`, `cast`, `anvil`) → [Installation guide](https://book.getfoundry.sh/getting-started/installation)
- Git submodules initialized (`forge install` inside each Foundry project pulls dependencies)

Optional but helpful: VS Code + Solidity extension, `direnv`/`.env` management for RPC URLs and private keys.

---

## Quick Start

```bash
git clone <repo-url>
cd /workspace
# choose the project you want to explore:
cd Foundry-Toolchain          # RaffleHouse example
# or
cd "Regular Exam 01.06"       # Micro-scholarship dispenser
```

---

## Project 1 — `Foundry-Toolchain/`

Raffle platform built around two contracts:

- `RaffleHouse`: creates time-boxed raffles, enforces ticket prices/durations, pseudo-randomly selects winners, and handles prize payouts.
- `TicketNFT`: ERC721 + enumerable NFT contract dedicated to each raffle, giving buyers transferable proof of participation.

### Key Features

- Pure Foundry workflow (no Node.js dependency)
- Scripts in `script/` for deploying `RaffleHouse` (`RaffleHouse.s.sol`) and `TicketNFT` collections
- Tests in `test/` validating raffle lifecycle, winner selection, and security guards

### Commands

```bash
forge install                 # once, to pull OZ + forge-std
forge fmt                     # format contracts and scripts
forge build                   # compile contracts
forge test                    # run Solidity unit tests
forge script script/RaffleHouse.s.sol:RaffleHouseScript \
  --rpc-url $SEPOLIA_RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast                 # deploy via Foundry script
```

> Use `anvil` for local testing or `cast` to poke deployed contracts.

---

## Project 2 — `Regular Exam 01.06/`

Micro-Scholarship Dispenser that sends sub-$5 ETH stipends to verified students. The system combines a minimal proxy factory, the `ScholarshipDispenser` logic contract, Chainlink price feeds for USD→ETH conversion, and Merkle proofs for student allowlists.

### Highlights

- Dual toolchain: Hardhat (JS/TS scripting, mocks) + Foundry (Solidity tests)
- `ScholarshipFactory` deploys cheap clones with immutable settings
- Chainlink `AggregatorV3` integration for live pricing
- Merkle tree verification plus single-claim enforcement
- Comprehensive testing (`test/ScholarshipDispenser.test.js` + `test/ScholarshipDispenser.t.sol`)

### Setup

```bash
cd "Regular Exam 01.06"
npm install                   # install Hardhat toolchain
forge install                 # pull OZ/Chainlink libs for Foundry
cp .env.example .env          # add RPC URLs, PRIVATE_KEY, ETHERSCAN key, etc.
```

### Build & Test

```bash
npx hardhat compile
forge build

npx hardhat test                      # JS unit tests with mocks
forge test --match-contract ScholarshipDispenserTest --gas-report
```

### Deployment & Scripts

- `scripts/deploy.js`: Deploy the factory/dispenser set to Sepolia (`npx hardhat run scripts/deploy.js --network sepolia`)
- `scripts/generateMerkleProofs.js`: Build Merkle trees from student CSV/JSON lists
- Foundry scripts inside `script/` (if added) can be executed with `forge script ... --broadcast`

Remember to fund the deployed dispenser with ETH before students claim and to keep proofs synchronized with on-chain Merkle roots.

---

## Useful Commands Reference

| Purpose | Foundry | Hardhat |
| --- | --- | --- |
| Install deps | `forge install` | `npm install` |
| Compile | `forge build` | `npx hardhat compile` |
| Test | `forge test` | `npx hardhat test` |
| Gas report | `forge test --gas-report` | `npx hardhat test --network hardhat --gas-report` *(if configured)* |
| Local node | `anvil` | `npx hardhat node` |
| Deploy | `forge script <Script> --broadcast` | `npx hardhat run scripts/deploy.js --network <net>` |

---

## Folder Structure

```
.
├── Foundry-Toolchain/          # Foundry-only raffle example
│   ├── src/                    # Solidity contracts (RaffleHouse, TicketNFT)
│   ├── script/                 # Forge deployment scripts
│   └── test/                   # Forge tests
├── Regular Exam 01.06/         # Hardhat + Foundry project
│   ├── contracts/              # Scholarship factory + dispenser logic
│   ├── scripts/                # JS utilities: deploy, Merkle proof generation
│   ├── test/                   # JS + Solidity tests
│   ├── foundry.toml            # Forge config
│   └── hardhat.config.js       # Hardhat config
└── README.md                   # You are here
```

---

## Contributing

1. Fork the repo & create a feature branch.
2. Keep changes scoped to one subproject when possible.
3. Run the relevant test suites (`forge test`, `npx hardhat test`) before opening a PR.

Bug reports and feature suggestions are welcome—open an issue with as much context as possible.

---

## License

This repository is released under the MIT License. See the individual subproject READMEs for any additional licensing notes.

