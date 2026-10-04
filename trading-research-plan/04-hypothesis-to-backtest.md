# 04 — One idea, from hypothesis to "do not trade"

This file follows one trading idea through all three levels. The idea is a simple **mean-reversion** rule. Mean reversion is the belief that a price that moved sharply one way tends to move partly back. We test it on the practice fund ETF-X.

The full-sample result **loses money after realistic costs**. That is deliberate. The skill you are practising is stopping.

All ETF-X numbers below come from `data/etfx-practice-2014-2023.csv` and, in Stage 3 only, `data/etfx-holdout-2024-2025.csv`. ETF-X is synthetic (see `00`). Each number was computed with the spreadsheet formulas in `01-beginner.md` (Module B6) and checked against the Python notebook in `02-intermediate.md`. The two agree to the cent. If your spreadsheet disagrees, your spreadsheet has a bug. Find it before reading on.

Read Stage 1 after Level 1, Stage 2 after Level 2, and Stage 3 after Level 3.

---

## Stage 0 — The observation

You watch a broad stock-index fund for a few months. You notice that after it falls for several days in a row, it often rises the next day.

That is an **observation**. It is not yet testable. "Several days" is vague. "Often" is vague. "Rises" does not say from where, or by how much compared with costs.

---

## Stage 1 — Beginner: write the rule, run it, read it

### 1a. The hypothesis

A **hypothesis** is a claim the data could prove wrong. Write it so a stranger could check it.

> **H-001.** After ETF-X closes lower than the previous close three days in a row, buying at the next open and selling at the open after the first higher close makes more than its costs on average.
>
> **It is false if:** the average net profit per trade, after commission and slippage, is zero or below over 2014-01-02 to 2023-12-29.

"Net" means after costs. "Gross" means before costs.

### 1b. The rule card

A **rule card** is the written rule. It is complete when two people following it would make the same trades.

```
RULE CARD  H-001 v1  "Three down closes"        Written: (date you wrote it)
Instrument   ETF-X practice file. Real-data twin: SPY.
Data         Daily prices, 2014-01-02 to 2023-12-29. Nothing after 2023.
Setup        At today's close: today's close is below yesterday's close,
             and this has happened 3 or more days in a row.
Entry        Buy at the next day's open. Only if not already holding.
Exit         Check at each close while holding. Sell at the next open if ANY is true:
               1. today's close is above yesterday's close;
               2. today's close is at or below entry fill x 0.95  (the stop);
               3. today is the 5th day held.
Size         Shares = round down ($10,000 / entry day's open).
Costs        Commission $0.005 per share, minimum $1.00 per order.
             Slippage 0.05% of price on each side.
Not traded   Any other ticker under this card. Adding to a position.
             Days with a missing or zero price. Short selling. Leverage.
             Overrides "because it looks different this time".
Research     If the net result is zero or below, stop. Do not tune.
stop rule
```

Notice the last line. You decide **before** running the test what result makes you stop.

### 1c. One trade by hand

The first trade in the file shows what the spreadsheet will do on every row.

| Date | Open | Close | What happens |
|---|---|---|---|
| 2014-01-06 | 101.77 | 102.03 | Close up. Streak 0. |
| 2014-01-07 | 101.75 | 101.64 | Down close 1 |
| 2014-01-08 | 101.68 | 101.46 | Down close 2 |
| 2014-01-09 | 101.48 | 101.28 | Down close 3. **Setup.** |
| 2014-01-10 | 100.30 | 98.91 | **Buy at open.** Close is down again, so stay in. |
| 2014-01-13 | 99.11 | 99.40 | Close is up (99.40 > 98.91). **Exit signal.** |
| 2014-01-14 | 99.27 | 100.91 | **Sell at open.** |

The arithmetic:

- Shares = round down(10,000 / 100.30) = round down(99.70) = **99**.
- Entry fill = 100.30 × (1 + 0.0005) = **100.35015**. Slippage makes you pay a little more than the open.
- Exit fill = 99.27 × (1 − 0.0005) = **99.220365**. Slippage makes you receive a little less.
- Commission each side = the larger of $1.00 and 99 × $0.005 = $0.495. So **$1.00** each side, **$2.00** total.
- Net profit = (99.220365 − 100.35015) × 99 − 2.00 = −111.85 − 2.00 = **−$113.85**.
- Gross profit (no costs, open to open) = (99.27 − 100.30) × 99 = **−$101.97**.

The planned risk on entry:

- Stop price = 100.35015 × 0.95 = **95.33**.
- Risk per share = 100.35015 − 95.33 = **$5.02**.
- Planned risk = 5.02 × 99 = **$496.73**, plus $2.00 commission ≈ **$499**.

The stop is checked at the close and executed at the next open. If the fund gaps down overnight, the real loss can be larger than $499. "Gaps down" means it opens well below the previous close. Planned risk is a plan, not a guarantee.

### 1d. The full run

Built in a spreadsheet as in Module B6. Same 160 trades in both columns. Only the costs differ.

| 2014-01-02 to 2023-12-29 | Gross (no costs) | Net (with costs) |
|---|---|---|
| Trades | 160 | 160 |
| Net profit | **+$1,451.21** | **−$456.25** |
| Win rate | 60.0% | 52.5% |
| Average win | $72.67 | $70.34 |
| Average loss | −$86.34 | −$83.75 |
| Expectancy (profit per trade) | +$9.07 | **−$2.85** |
| Profit factor | 1.26 | 0.93 |
| Max drawdown | −11.06% | −20.11% (−$2,350.61) |
| Exposure (share of days holding) | 11.1% | 11.1% |

Total costs were $1,907.46, about $11.92 per trade. That is $2.00 commission plus about $10 of slippage on a $10,000 position. Costs turned 12 winning trades into losing trades.

Expectancy check in plain arithmetic, net:

0.525 × $70.34 − 0.475 × $83.75 = $36.93 − $39.78 = **−$2.85 per trade**.

### 1e. Read it and stop

The hypothesis said "makes more than its costs on average". It did not: −$2.85 per trade. **H-001 is falsified on this data. Stop.**

What this result does **not** prove:

- It does not prove that bounces after down days never happen. The gross column shows a small positive average.
- It does not say anything about SPY or any real fund. ETF-X is synthetic.
- It does not prove a different exit or a cheaper broker plan would fail. Those are **new hypotheses**. They need new rule cards and, in Level 3, data you have not seen.

What you must not do now: change "3 days" to "2 days", or add a filter, and run it again on the same data until it turns positive. Stage 2 shows why.

### 1f. Your turn on real data

1. Pull SPY daily prices for 2014-01-01 to 2023-12-31 into Google Sheets (Module B5).
2. Do not load anything after 2023. Keep 2024 onward sealed for Level 3.
3. Copy your ETF-X spreadsheet. Paste SPY dates and prices over columns A to E from row 2. Delete any rows below the last SPY date. If SPY has more rows than ETF-X, copy the row-3 formulas down to the last row. Then read the summary.
4. Write the trade count, gross and net profit, and expectancy on a new line in your hypothesis log.

Your numbers will not match ETF-X. Whatever they are, write down what they do not prove.

---

## Stage 2 — Intermediate: what happens if you don't stop

In real life, many people do not stop at 1e. They tune. This stage shows, with numbers, how tuning fools you.

**A confession first.** You already saw the full 2014–2023 result in Stage 1. So you cannot honestly "hold out" any of that period now. You know how it ends. The split below is for practice only. That is why Level 3 uses a file you have never opened.

### 2a. Split the data

**In-sample** is the data you are allowed to look at and tune on. **Out-of-sample** is data you keep aside to check the tuned rule once.

- In-sample: 2014-01-02 to 2020-12-31 (1,826 days).
- Out-of-sample: 2021-01-01 to 2023-12-29 (781 days).

Each part is run separately, starting flat (no position).

| Rule (n_down = 3, max_hold = 5), net of costs | In-sample 2014–2020 | Out-of-sample 2021–2023 |
|---|---|---|
| Trades | 110 | 50 |
| Net profit | **+$788.40** | **−$1,244.65** |
| Expectancy | +$7.17 | −$24.89 |
| Noise band on expectancy (± 2 standard errors) | ± $19.35 | ± $29.30 |
| Profit factor | 1.21 | 0.52 |
| Win rate | 55.5% | 46.0% |
| Max drawdown | −10.07% | −17.11% |

In-sample looks fine. But look at the noise band. The **noise band** is a rough range that the true average per trade could plausibly sit in, given how much trade results vary. Here it is ±2 × (standard deviation of trade profits) ÷ √(trades); Module I6 explains it. In-sample, +$7.17 ± $19.35 runs from −$12.18 to +$26.52. That range includes zero. Even the good half does not show an edge clearly.

### 2b. Tune on in-sample only

Try 16 versions on in-sample data: n_down from 2 to 5, max_hold in {3, 5, 7, 10}. Net profit, in dollars:

| n_down ↓ / max_hold → | 3 | 5 | 7 | 10 |
|---|---|---|---|---|
| 2 | 642 | **1,050** | 1,015 | 1,015 |
| 3 | 736 | 788 | 755 | 755 |
| 4 | 626 | 418 | 418 | 418 |
| 5 | −260 | −220 | −300 | −300 |

The best cell is n_down = 2, max_hold = 5: +$1,049.82 on 234 trades. Its neighbours are also positive. This looks like a **plateau**: a region where nearby settings give similar results. A plateau is better than a lone spike. Keep that in mind for Stage 3.

### 2c. Test the winner once on out-of-sample

| n_down = 2, max_hold = 5 | In-sample | Out-of-sample |
|---|---|---|
| Trades | 234 | 87 |
| Net profit | +$1,049.82 | **−$964.01** |
| Expectancy | +$4.49 | −$11.08 |

The tuned rule lost out-of-sample. Tuning picked the setting that fitted the past best. That is not the same as the setting that will work next.

### 2d. The tempting filter

Now try a filter: only take entries on one weekday. Five versions, in-sample, rule otherwise unchanged:

| Entry day | IS trades | IS net | IS profit factor | IS expectancy | OOS trades | OOS net | OOS expectancy |
|---|---|---|---|---|---|---|---|
| Monday | 38 | **+$706.47** | **1.94** | +$18.59 | 20 | +$106.74 | +$5.34 |
| Tuesday | 32 | −$97.55 | 0.91 | −$3.05 | 17 | −$96.93 | −$5.70 |
| Wednesday | 40 | −$148.44 | 0.90 | −$3.71 | 19 | −$751.85 | −$39.57 |
| Thursday | 30 | +$89.39 | 1.07 | +$2.98 | 19 | −$640.36 | −$33.70 |
| Friday | 45 | +$153.44 | 1.10 | +$3.41 | 22 | +$48.57 | +$2.21 |

"Monday only" looks excellent in-sample: profit factor 1.94, win rate 63.2%, max drawdown only −4.51%. Out-of-sample it stayed slightly positive. Is it real?

Reject it. Four reasons, each checkable:

1. **No mechanism.** Nothing in the hypothesis says Mondays matter. You picked Monday because it scored best.
2. **Five tries, one winner.** With five versions, one will look best by luck alone.
3. **Too few trades.** 20 out-of-sample trades. Expectancy +$5.34, noise band ±$39.02 (2 × $19.51). That is −$33.68 to +$44.36. Zero sits comfortably inside.
4. **The decay.** Expectancy fell from +$18.59 to +$5.34. That fall is what a fitted rule looks like.

This is a **curve-fit**: a rule shaped to past noise. "It didn't lose out-of-sample" is not the same as "it works".

### 2e. Walk-forward: tuning done honestly, once

**Walk-forward** repeats the tune-then-test step through time. Tune on three years, test on the next one year, slide forward one year, repeat. Only the test years count. Module I7 explains it.

Each row tunes over the same 16 versions as 2b. It picks the one with the highest training-period net profit, then runs that one on the test year:

| Test year | Trained on | Picked n_down / max_hold | Training net | Test trades | Test net |
|---|---|---|---|---|---|
| 2017 | 2014–2016 | 2 / 5 | +$1,673 | 34 | −$749 |
| 2018 | 2015–2017 | 2 / 5 | +$1,046 | 34 | −$950 |
| 2019 | 2016–2018 | 4 / 3 | +$41 | 5 | +$450 |
| 2020 | 2017–2019 | 4 / 3 | +$428 | 10 | −$5 |
| 2021 | 2018–2020 | 4 / 3 | +$341 | 10 | +$270 |
| 2022 | 2019–2021 | 2 / 7 | +$984 | 28 | −$770 |
| 2023 | 2020–2022 | 5 / 3 | −$10 | 3 | −$119 |
| **Total** | | | | **124** | **−$1,873** |

Two warnings:

- The picked settings jump around: 2/5, 4/3, 2/7, 5/3. A real, stable effect would usually pick similar settings each time.
- The best training result (+$1,673) came right before one of the worst test years (−$749).

### 2f. Costs: how much would they need to fall?

Same rule as Stage 1 (3 / 5), full 2014–2023, with slippage multiplied:

| Costs | Slippage per side | Net profit | Profit factor |
|---|---|---|---|
| None (gross) | 0% | +$1,451.21 | 1.26 |
| Commission only | 0% | +$1,131.21 | 1.20 |
| Half | 0.025% | +$337.48 | 1.06 |
| **Base case** | **0.05%** | **−$456.25** | **0.93** |
| Double | 0.10% | −$2,043.72 | 0.71 |
| Triple | 0.15% | −$3,631.18 | 0.55 |

With $1 commissions included, the result breaks even at roughly **0.035% slippage per side**: between 0.034% and 0.036%, found by trying values. The rule only works if every fill is better than that. Module I4 shows how to check your real fills on IBKR. Do not assume your fills will be that good.

### 2g. One lucky window

Net profit by the year each trade closed (3 / 5 rule, base costs):

| Year | Trades | Net |
|---|---|---|
| 2014 | 14 | +$486.29 |
| 2015 | 15 | +$468.27 |
| 2016 | 18 | +$460.49 |
| 2017 | 14 | −$334.60 |
| 2018 | 21 | −$265.07 |
| 2019 | 9 | +$310.35 |
| 2020 | 19 | −$337.34 |
| 2021 | 19 | +$139.46 |
| 2022 | 19 | −$1,068.61 |
| 2023 | 12 | −$315.50 |

2014–2016 made +$1,415.05. The other seven years lost −$1,871.30 together. Someone who tested only 2014–2016 would have seen a clean upward line.

### 2h. Stage 2 verdict

The in-sample split, the tuning, the filter, walk-forward, costs and the year table all point the same way. **Reject the rule and every tuned variant.** Stage 1 already told you to stop. Stage 2 shows what tuning would have cost you.

---

## Stage 3 — Expert: pre-register, open the sealed data, write the memo

Level 3 asks a different question. If the idea had **not** been killed in Stage 1, how would you run one clean, final test? Here is the procedure, done for real on the sealed file.

### 3a. Hypothesis log entry

```
ID        H-001
Date      (today)
Idea      Short-term bounce after 3+ down closes in a broad index fund.
Mechanism (why it might exist) Short-term selling pressure that reverses.
          Weak: no evidence it is bigger than costs.
Status    v1 failed on practice 2014-2023 (net -$456.25, 160 trades).
          Tuned variants failed out-of-sample and in walk-forward.
Next      One pre-registered test of v1 on sealed 2024-2025 data, then close.
```

### 3b. Pre-registration

To **pre-register** is to write down the exact rule, data and pass/fail thresholds before you run the test, and date it. Then nothing can be adjusted after you see the answer.

```
PRE-REGISTRATION  H-001 v1                       Date: (before opening the file)
Rule        Rule card H-001 v1, unchanged (n_down 3, max_hold 5, stop 5%).
Data        etfx-holdout-2024-2025.csv, opened once, today, after this is saved.
Costs       Base: $0.005/share, $1 minimum; slippage 0.05% per side.
Pass only if ALL hold:
  1. Net profit > $0 at base costs.
  2. Net profit > $0 at double slippage (0.10% per side).
  3. Profit factor >= 1.10 at base costs.
  4. At least 20 trades.
  5. Max drawdown no worse than -15%.
Otherwise   Fail. No variants run on this file. H-001 closed.
If pass     Paper-trade on IBKR under the paper plan (Module E7). Still no real money.
```

Why these thresholds? They are a judgement made in advance, not a law. Twenty trades is too few to prove anything. It is a floor below which you will not even read the result. Module E2 discusses choosing thresholds.

### 3c. Open the sealed file once

| H-001 v1 on 2024-01-01 to 2025-12-31 | Result | Pass? |
|---|---|---|
| Trades | 29 | Criterion 4: yes |
| Net profit, base costs | **−$830.08** | Criterion 1: **no** |
| Net profit, double slippage | −$1,117.41 | Criterion 2: no |
| Profit factor | 0.51 | Criterion 3: no |
| Max drawdown | −12.53% | Criterion 5: yes |
| Gross profit (no costs) | −$484.75 | — |
| Win rate | 41.4% | — |

**Fail.** Note that even the gross result is negative. In this period the bounce did not just shrink below costs. It went the wrong way.

### 3d. Cost stress and neighbour check

**Cost stress** means re-running at higher costs to see how fast the result breaks. On the sealed file: gross −$484.75, base −$830.08, double −$1,117.41, triple −$1,404.74. There is no cost level at which this passes.

A **parameter neighbour check** means running the settings next to your chosen ones to see whether the result is a plateau or a lone spike. On in-sample data (Stage 2b), n_down 2, 3 and 4 were all positive. **The neighbour check passed. The rule still failed.**

That is the lesson of this step. A plateau is necessary, not sufficient. Neighbours share the same data, so they share the same luck. You may not run neighbours on the sealed file to "find one that works". The pre-registration forbids it.

### 3e. Decision memo

```
DECISION MEMO  H-001 "Three down closes" on ETF-X            Date: (today)

Decision      DO NOT TRADE. DO NOT PAPER-TRADE. Close H-001.

Question      Does buying after 3+ down closes and selling after the first
              up close earn more than realistic costs?

Evidence      1. Practice 2014-2023, base costs: -$456.25 net, 160 trades,
                 expectancy -$2.85 (noise band +/- $16.27).
              2. Gross +$1,451.21: any edge is smaller than costs.
                 Breakeven needs slippage under ~0.035% per side.
              3. Out-of-sample 2021-2023: -$1,244.65 on 50 trades.
              4. Walk-forward 2017-2023: -$1,873 on 124 trades.
                 Picked settings unstable.
              5. Pre-registered sealed test 2024-2025: -$830.08 on 29 trades.
                 Failed 3 of 5 criteria, including gross.

What this does NOT show
              - That no mean-reversion rule works on any fund.
              - Anything about SPY or other real funds (ETF-X is synthetic).
              - That a cheaper execution method would fail. That would be a
                new hypothesis on new data.

What would change the decision
              A new, written hypothesis with a stated mechanism, a rule card
              written before seeing data, and a pass on data not yet opened.
              Not a variant of H-001 fitted to the files already used.

Cost of being wrong
              If H-001 did have a small real edge, we lose a possible profit
              smaller than costs at our likely fills. Low.

Signed        (you)
```

### 3f. Defending "do not trade"

Someone will say: "But Monday-only made money out-of-sample." Your answer is in 2d: no mechanism, chosen from five, 20 trades, noise band includes zero.

Someone will say: "Your costs are too high." Your answer is in 2f. It needs slippage under 0.035% per side on every fill. You have no paper-trading evidence that you get fills that good. Even at zero cost, the sealed data lost.

Someone will say: "2022 was unusual. Remove it." Removing a bad period after seeing it is the same mistake as picking Monday. The rule card has no "unusual year" clause.

---

## The reveal: how ETF-X was built

Read this only after Stage 3.

ETF-X was generated by a computer with a small built-in effect. Until 2021-03-16, days after three or more down closes had a slightly higher average return than other days. From that date on, they had a slightly **lower** average return. The rest was random noise.

So there **was** a real gross edge early on. It was mostly smaller than costs, and then it reversed. That change of behaviour is a **regime change**. The tests caught it:

- The full-sample net result was negative.
- Out-of-sample and walk-forward were negative.
- The sealed file was negative even gross.

Real markets never give you this reveal. You will never know for sure whether an effect was real, faded, or never existed. The method above is what you have instead.

## Your turn on real data

Repeat the whole file on SPY, or another liquid fund you can trade on IBKR:

1. Stage 1 with 2014–2023 adjusted prices from Module I2.
2. Stage 2 splits and walk-forward inside 2014–2023 only.
3. Stage 3: write the pre-registration, then load 2024-01-01 to the end of the last full month, once.

Your numbers will differ. A short-term bounce effect may look stronger on a real fund. If your SPY result passes every pre-registered criterion, the next step is Module E7: a paper-trading plan with a written stop rule. It is still not live trading.
