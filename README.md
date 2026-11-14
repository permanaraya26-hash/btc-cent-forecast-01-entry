# btc-cent-forecast-01 — Forecast Bitcoin's price 4 hours ahead

Entry for the make.fun bounty "Forecast Bitcoin's price 4 hours ahead,
12 times over 48 hours" ($250 USDC, top 100 paid).

## What it does

`main.py` (Python 3.13, standard library only, offline, deterministic) reads
every `<id>.in` case in `$MAKEFUN_INPUT_DIR` (else `/input`) — BTC/USDC
minute closes for the 72 hours up to a checkpoint — and writes
`<id>.out` to `$MAKEFUN_OUTPUT_DIR` (else `/output`) with exactly
`{"forecasts": [{"case": "<id>", "price": <guess>}]}`: its guess of the price
240 minutes after the checkpoint.

## Method

Persistence plus a small drift: the guess is the checkpoint close plus
2 basis points of the close, capped at $15.00.

Four months of hourly BTC closes (2,869 four-hour windows) show the median
four-hour change is +$12.30, the mean +$12.70, with no autocorrelation of
four-hour returns and no predictive power in the last hour's move, linear
trends, mean reversion, or adaptive drifts — all were tested on the six
practice cases and on 128 rolling checkpoints cut from the recording, and
all are neutral-to-worse than persistence out of sample (60-minute momentum
looks great on the six practice cases but loses on the rolling window: a
one-day trend, not a signal). So the conditional median of the four-hour
change is about +$12, and a small positive drift is the only adjustment
that survives. A tie with the house persistence baseline pays nothing (a
prize needs a score strictly below it), so the drift is also what makes the
entry able to win at all.

## Practice score (local, on the kit's six cases)

- House persistence baseline (stated in RULES, reproduced exactly by our
  evaluator): **44,521.58 cents** mean absolute error.
- This entry: **45,021.3 cents** — about 500 cents (1.1%) worse on practice.

That is expected, not a bug: the practice window (10–11 Nov 2026) was a
downtrend (median four-hour change −$105), so any positive drift hurts
there. The bet is on the final window (15–17 Nov, all weekend checkpoints,
whose historical median four-hour change is +$26): the drift is the
long-run median, shrunk and capped. Practice is explicitly no guide to
the final, and a pure-persistence entry would tie the baseline and take
no prize.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

Two runs on the same case write byte-identical outputs; one case takes
~0.05 s (limit 8 s).

## License

MIT — see `LICENSE`.
