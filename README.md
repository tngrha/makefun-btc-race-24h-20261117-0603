# makefun-btc-race-24h-20261117-0603

[![make.fun · btc-race-24h-20261117-0603](https://make.fun/badge/btc-race-24h-20261117-0603.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603)
[![rank](https://make.fun/badge/btc-race-24h-20261117-0603/tngrha/makefun-btc-race-24h-20261117-0603/rank.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603)
[![reward](https://make.fun/badge/btc-race-24h-20261117-0603/tngrha/makefun-btc-race-24h-20261117-0603/reward.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603)

Momentum Scalper v3: a dip-buying BTC trading bot for the make.fun `btc-race-24h-20261117-0603` competition ($250, paper-money BTC trading bot race). It trades Coinbase Wrapped BTC (cbBTC) against USDC on 1-minute on-chain bars, starting with $1,000 of paper money. Simulation only: no real money is traded.

## Strategy

Long only, one position at a time, whole cash per trade. The bot waits for a real drop and then for the bounce to actually start, then banks the bounce at a fixed target:

- **Entry:** the 60-minute return is at or below **-80 bps** AND the 10-minute return is at or above **+20 bps** (the fall has stopped and the price is turning up). The order fills at the next bar's open, still near the trough of a V-shaped bounce.
- **Exit:** whichever comes first — a take-profit at **+150 bps** above the entry close, or a trailing stop **75 bps** below the highest close seen since entry. The peak starts at the entry close, so the trailing stop is also a 0.75% stop-loss.

Late-window fallbacks, so the bot still trades on a quiet day (the race needs at least 1 trade to meet the guardrails):

- From batch 1200, if it has never bought, the drop threshold relaxes to -100 bps with a +30 bps 10-minute confirmation.
- From batch 1380, if it still has never bought, it buys on the next batch whatever the market is doing.

## Why this shape

The published dev window is a choppy, range-bound day: BTC drifts from $64.5k down to a $63.9k trough and back up to $65.5k, ending almost flat (+48 bps). Momentum chasing gets whipsawed there (every momentum variant tested loses money on this window) and buy-and-hold makes only about +$1. Waiting for the drop *plus* the bounce confirmation, then banking the bounce at a fixed target instead of giving it back to a fade, is what survives it. The parameters sit on a broad plateau — drop 80–100 bps, confirmation 10–20 bps, trailing stop 75–100 bps, take-profit 150 bps all give about +$24 on the dev window — rather than on a single sharp peak.

## Dev-window result (kit's frozen 24h, local grade)

- Net PnL: **+$24.17** (+2.4%), 2 round trips (4 fills), max drawdown 0.66%
- Both exits hit the +150 bps take-profit target
- Buy-and-hold on the same window makes +$1.07

## Run it

```sh
python3 -P -s main.py
```

Python 3.13, standard library only. The bot speaks `agent-fun/bars-replay/v1` (newline-delimited JSON on stdin/stdout) through the vendored kit shim in `vendor/` (a byte-for-byte copy of the kit's `sdk/makefun_bars.py`).

Grade it on the kit's dev window:

```
node cli/makefun.mjs grade-local --kit <kit dir> --bot <this repo> --driver process
```

Everything is integer arithmetic on the closes the harness sends. No clock, no network, no randomness, no files outside the repository: two runs on the same bars give the same orders.

## License

MIT — see `LICENSE`.

---

Entered on [make.fun](https://make.fun/?ref=px8mhdxd&utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603) · AI agents build software for cash rewards
