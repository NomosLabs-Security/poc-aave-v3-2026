# Aave V3 — CAPO Oracle Misconfiguration — PoC

> **Educational Purpose Only** — This PoC is created for security research and education
> purposes only. It uses local mocks, not mainnet.

## Quick Start

```bash
git clone https://github.com/NomosLabs-Security/poc-aave-v3-2026
cd poc-aave-v3-2026
forge install foundry-rs/forge-std --no-git
forge test -vvvv
```

## Details
- **Exploit Date:** 2026-03-10
- **Chain:** Ethereum
- **Loss:** ~$27.78M (10,938 wstETH liquidated across 34 accounts)
- **Technique:** CAPO Oracle Misconfiguration (stale snapshotRatio + fresh snapshotTimestamp)
- **Expected Output:** Tests demonstrating how desynchronized CAPO parameters undervalue wstETH by ~2.85%, triggering unfair liquidations in E-Mode positions
- **Full Analysis:** [NomosLabs Security Intelligence Archive](https://nomoslabs.io/archive/aave-v3-2026)

## Vulnerability

Aave V3 uses a Correlated Asset Price Oracle (CAPO) to cap the exchange rate of correlated assets like wstETH/ETH. The CAPO system relies on three synchronized parameters:

1. **snapshotRatio** — The anchor exchange rate
2. **snapshotTimestamp** — When the ratio was captured
3. **maxYearlyRatioGrowthPercent** — Growth rate limiter

The bug: `snapshotRatio` was rate-limited to a 3% max increase per update, but `snapshotTimestamp` had no such constraint. When Chaos Labs submitted a ratio update after >1 year of staleness, the ratio capped at ~1.1919 while the timestamp jumped forward by 7 days. The CAPO formula then calculated a maximum allowed rate *below* the live market rate (~1.2282), creating an artificial 2.85% price drop that triggered E-Mode liquidations on healthy positions.

## License

MIT — For educational use only.
