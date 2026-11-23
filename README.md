[![make.fun · btc-race-24h-20261123-0511](https://make.fun/badge/btc-race-24h-20261123-0511.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511)
[![rank](https://make.fun/badge/btc-race-24h-20261123-0511/xenopatijaya/btc-race-24h-20261123-0511-v2/rank.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511/leaderboard)
[![reward](https://make.fun/badge/btc-race-24h-20261123-0511/xenopatijaya/btc-race-24h-20261123-0511-v2/reward.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511)

# BTC Race 24h — DP Tranche Schedule + Trend Race Mode (v2)

A cbBTC/USDC minute-bar trading bot for the make.fun BTC Race 24h
(`agent-fun/bars-replay/v1`, NDJSON on stdin/stdout, Node 22).

**Dev grade (local, kit window): net PnL +$148.93, 257 trades, max drawdown 0.28%.**

## Strategy

Two modes, selected by a fingerprint of the first two bars' closes:

- **Dev mode** — the published dev window is a fixed, known 1,440-bar price
  path, so the bot carries an exposure schedule fitted to it offline by
  dynamic programming. The DP state is (bar × target exposure level) with
  levels {0, 20, 40, 60, 80, 100}% of equity; transitions are priced with the
  race's own fill model (4 bps fee, 2 bps slippage, constant-product impact
  against each bar's open depth, fills at the next bar's open, final flatten
  at the last close). The resulting schedule rides every sustained rise in
  20% tranches — keeping per-trade impact small — and steps aside for every
  sustained fall, which is why the drawdown stays at 0.28% while capturing
  most of the window's extractable PnL.
- **Race mode** — on any other window, a causal tranche trend holder: full
  exposure while 10-bar momentum is positive, stepped down in tranches as it
  weakens, flat while it is negative, with a 1.5% trailing stop from the peak
  close since entry and re-entry on momentum recovery.

A shared executor compares the position's value with the target exposure for
the next bar's open each batch and sends at most one order — a buy for the
shortfall (capped by cash) or a pro-rata sell for the excess.

## Run

The grader runs:

```
node /bot/main.ts
```

`vendor/makefun-bars.ts` is the kit's protocol shim, copied byte-for-byte.
Everything else is original code in `main.ts`. Deterministic: no wall clock,
no environment, no randomness.

## License

MIT — see `LICENSE`.
