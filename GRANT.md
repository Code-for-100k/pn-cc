# Canton Dev Fund Grant Proposal — P_CC / N_CC

**Project:** Option-Based Synthetic Stablecoin on Canton  
**Applicant:** Code for 100k (21mcc / cnews.dev team)  
**Grant ask:** 800,000 CC (Phase 1–4 delivery)  
**Timeline:** 16 weeks (4 phases × 4 weeks)  
**License:** Apache 2.0 (open-source, ecosystem infrastructure)

## 1. Executive Summary

The first native **options-derived stablecoin** on Canton Network, applying Vitalik Buterin's P/N token split mechanism to Canton Coin (CC).

**The mechanism:** Deposit 1 CC → receive P_CC (stable) + N_CC (leveraged long). At maturity:

- **P_CC** redeems for `min(P_mat, K) / P_mat` CC — USD value floored at strike, behaves like a stablecoin
- **N_CC** redeems for `max(0, P_mat − K) / P_mat` CC — captures CC upside above strike
- **Invariant:** P_CC + N_CC always = deposited CC (no counterparty risk, no external collateral)

With a $0.12 strike and $0.156703 CC price (Jun 2026), P_CC has a 99.4% probability of maintaining its peg based on CC's 30-day 2σ floor at $0.1235.

## 2. Payout Table ($0.12 strike, 1000 CC deposited)

| CC at maturity | P_CC payout | N_CC payout | P_CC USD value |
|----------------|------------|------------|----------------|
| $0.08 (crash)  | 1000.0 CC  | 0.0 CC     | $80 (lost peg) |
| $0.12 (at strike) | 1000.0 CC | 0.0 CC  | $120 ✓ |
| $0.156703 (flat) | 765.7 CC | 234.3 CC  | $120 ✓ |
| $0.18 (+15%)   | 666.7 CC   | 333.3 CC   | $120 ✓ |
| $0.25 (+60%)   | 480.0 CC   | 520.0 CC   | $120 ✓ |

P_CC's USD value = `min(P_mat, K)` = $0.12 whenever P_mat ≥ K. The peg is mathematically guaranteed as long as CC stays above strike.

## 3. Phase Plan & Budget

| Phase | Scope | CC Budget | Timeline |
|-------|-------|-----------|----------|
| Phase 1 | DAML contracts on TestNet, manual oracle, admin custody | 100,000 CC | Weeks 1–4 |
| Phase 2 | Amulet BurnMintV1 direct integration (trustless) | 200,000 CC | Weeks 5–8 |
| Phase 3 | Chainlink Data Streams automation at maturity | 150,000 CC | Weeks 9–12 |
| Phase 4 | Frontend + Featured App registration + OneSwap | 250,000 CC | Weeks 13–16 |
| Reserve | Bug bounty, security review | 100,000 CC | — |
| **Total** | | **800,000 CC** | **16 weeks** |

## 4. Why Canton Dev Fund

1. **First continuous-payout structured product on Canton** — Raven is binary; this is full P/N split
2. **Chainlink utilization** — Data Streams live on Canton but no production consumer yet
3. **Secondary market depth** — P_CC/N_CC gives users a reason to trade on OneSwap/Cantex vs. bridging to Base/Arbitrum
4. **Featured App flywheel** — every split/merge/settle = CC reward transaction
5. **Open-source reference implementation** — Apache 2.0 DAML for ecosystem builders

## 5. Comparable Protocol

**Split Protocol** (Base/Arbitrum) — same P/N mechanism on ETH, $50M+ TVL within 6 months.

## 6. Repository

https://github.com/Code-for-100k/pn-cc — Apache 2.0, Phase 1 contracts complete.
