# P_CC / N_CC — Option-Based Synthetic Stable on Canton

A DAML-native P/N token split applying Vitalik Buterin's option-based stablecoin
mechanism to Canton Coin (CC). Tracked as MOJ-6259.

## Mechanism

Lock 1 CC → receive **P_CC** (stable) + **N_CC** (leveraged long).

| Token | Payoff at maturity | Character |
|-------|-------------------|-----------|
| P_CC  | `min(P_mat, K) / P_mat × ccDeposited` | Stable — floored at strike |
| N_CC  | `max(0, P_mat − K) / P_mat × ccDeposited` | Leveraged long CC |

**Invariant:** P_CC + N_CC = ccDeposited (always, by construction).

### Example: $0.12 strike, 1000 CC deposited

| CC price at maturity | P_CC payout | N_CC payout | Total |
|---------------------|------------|------------|-------|
| $0.08 (crash) | 1000.00 CC | 0.00 CC | 1000 CC |
| $0.12 (at strike) | 1000.00 CC | 0.00 CC | 1000 CC |
| $0.156703 (split price) | 765.69 CC | 234.31 CC | 1000 CC |
| $0.18 (+15%) | 666.67 CC | 333.33 CC | 1000 CC |
| $0.25 (+60%) | 480.00 CC | 520.00 CC | 1000 CC |

## Phase 1 Deliverables (TestNet)

### DAML Templates (`daml/PnCC.daml`)

| Template | Role |
|----------|------|
| `PriceObservation` | Oracle price submission (manual in Phase 1) |
| `SplitSeries` | Series configuration (seriesId, strike, maturity date) |
| `SplitFactory` | Factory: Split / Merge / SubmitOraclePrice / OpenSettlement |
| `PCC` | Stable side token with Transfer + ClaimSettlement choices |
| `NCC` | Leveraged side token with Transfer + ClaimSettlement choices |
| `SettlementPool` | Admin announces maturity price + total CC for distribution |
| `SettlementClaim` | Issued when P/N token burned at maturity; admin pays off-chain |
| `MergeClaim` | Issued when P+N returned pre-maturity; admin refunds |

### Test Series

- **seriesId:** `CC-7D-012-20260625`
- **Strike:** $0.12 (deep ITM — 2σ floor at $0.1235)
- **Split price:** $0.156703 (Jun 18 2026)
- **Maturity:** Jun 25 2026

### Test Scripts (`daml/TestSetup.daml`)

| Script | Tests |
|--------|-------|
| `fullLifecycle` | Split 1000 CC → Alice transfers N_CC to Bob → settle above strike |
| `mergeTest` | Split → merge pre-maturity → full CC recovery |
| `belowStrikeTest` | CC crashes below strike → P_CC gets all, N_CC = 0 |

## Build & Deploy (Canton TestNet)

```bash
# Install DAML SDK
curl -sSL https://get.daml.com/ | sh -s 2.9.4

# Build
cd pn-cc
daml build

# Run unit tests (sandbox)
daml test

# Deploy to Canton TestNet
daml ledger upload-dar --host <canton-testnet> --port <port> \
  .daml/dist/pn-cc-0.1.0.dar

# Run setup script
daml script \
  --dar .daml/dist/pn-cc-0.1.0.dar \
  --script-name TestSetup:fullLifecycle \
  --ledger-host <canton-testnet> \
  --ledger-port <port>
```

## Phase Roadmap

| Phase | Scope |
|-------|-------|
| **1 (this)** | DAML contracts on TestNet, admin custody, manual oracle |
| **2** | Amulet BurnMintV1 direct integration (replace admin custody) |
| **3** | Chainlink Data Streams automation at maturity |
| **4** | Frontend at cnews.dev/21mcc or standalone + Featured App registration |
| **5** | Weekly/monthly series expansion, OneSwap liquidity seeding |

## Canton Grant Angle

- First native options-derived stable on Canton (not binary like Raven)
- Demonstrates CC reward flywheel (split/merge/settle txns → Featured App rewards)
- Oracle: Chainlink already live on Canton mainnet (dependency solved)
- Open-source DAML (Apache 2.0) — ecosystem infrastructure
- Prior art: Split Protocol (live on Base/Arbitrum) — proven mechanism
- **Grant ask: 800,000–1,000,000 CC** for Phase 1–4 delivery

## Settlement Oracle

Phase 1 uses manual `SplitFactory_SubmitOraclePrice` (Chainlink party simulation).
Phase 3 will wire Chainlink Data Streams: the oracle party is already parameterized
in all templates, so the upgrade is a party substitution only.

## License

Apache 2.0
