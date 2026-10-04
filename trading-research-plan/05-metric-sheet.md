# 05 — Metric sheet

One entry per number. Each gives: what it means in plain words, the formula, the spreadsheet cell from Module B7 (or the notebook line), an ETF-X example, when the sample is too small, and the **bad reading**: the wrong conclusion people usually draw from it.

ETF-X examples are H-001 v1, 2014-01-02 to 2023-12-29, net of base costs, unless stated. They are illustrations computed from synthetic data (see `00`).

**Read them together, in this order** (Module I6): trade count and dates → net profit and expectancy with noise band → max drawdown against net profit → profit factor with win rate → exposure. Sharpe ratio comes after Level 2.

Some sample-size guides below are rules of thumb, not statistical laws. They are marked that way.

---

## Trade count

**Plain words.** How many completed round trips (a buy and the matching sell) the test contains.

**Formula.** Count of sell fills.

**Cell.** `=SUM(Data!J:J)`. Notebook: `len(trades)`.

**ETF-X.** 160 trades in about 10 years, roughly 16 a year.

**Too small when.** Rules of thumb: under 30, describe the trades but draw no conclusions; 30 to 100, only large effects show; more than 100 is better but still not proof. Trades bunched in one period count as fewer than they look.

**Bad reading.** "160 trades is plenty." Not if 50 of them came in one year, or if you tried 30 variants to get there.

---

## Net profit

**Plain words.** Total dollars made or lost on all closed trades, after costs.

**Formula.** Sum of (exit fill − entry fill) × shares − commissions, over all trades.

**Cell.** `=SUM(Data!Q:Q)`. Notebook: `trades.pnl.sum()`.

**ETF-X.** −$456.25. Gross (no costs): +$1,451.21.

**Too small when.** Always state it with trade count and dates. On its own it cannot tell skill from luck. Use expectancy with the noise band.

**Bad reading.** "It made $1,451." That is the gross figure. Or: "−$456 is small, so it's nearly breakeven." Expectancy is −$2.85 per trade, and the edge would need to be about $12 per trade larger to cover costs comfortably.

---

## Expectancy

**Plain words.** Average net profit per trade.

**Formula.** Net profit ÷ trade count. Equivalently: win rate × average win − loss rate × average loss, with average loss written as a positive number.

**Cell.** `=E2/E1`. Notebook: `trades.pnl.mean()`.

**ETF-X.** −$456.25 ÷ 160 = **−$2.85**. Check: 0.525 × 70.34 − 0.475 × 83.75 = 36.93 − 39.78 = −2.85.

**Too small when.** Compute the **noise band**: ± 2 × (standard deviation of trade profits) ÷ √(trade count). If the band includes zero, the sample has not shown an edge.

ETF-X: standard deviation $102.89, so 2 × 102.89 ÷ √160 = 2 × 102.89 ÷ 12.65 = **± $16.27**. Range −$19.12 to +$13.42. Includes zero.

The band is approximate. It assumes trades are independent and not extremely lopsided. Read it as the minimum width of your uncertainty.

**Bad reading.** "Expectancy is positive, so the rule has an edge." Not if the band includes zero.

---

## Win rate

**Plain words.** Share of trades that made money after costs.

**Formula.** Winning trades ÷ all trades.

**Cell.** `=E3/E1`. Notebook: `len(wins) / n`.

**ETF-X.** 84 ÷ 160 = **52.5%**. Gross: 60.0%. Costs turned 12 small winners into losers.

**Too small when.** A rough band is ± 2 × √(p × (1 − p) ÷ n), where p is the win rate as a decimal and n is the trade count. This is an approximation.

- ETF-X: 2 × √(0.525 × 0.475 ÷ 160) = 2 × 0.0395 = ± 7.9 points, so 44.6% to 60.4%.
- 20 trades at 60%: 2 × √(0.6 × 0.4 ÷ 20) = 2 × 0.110 = ± 21.9 points, so 38% to 82%.

**Bad reading.** "70% winners, so it works." Win rate says nothing without average win and average loss. A rule can win 90% of the time and lose money.

---

## Average win and average loss

**Plain words.** The average net profit of winning trades, and the average net loss of losing trades.

**Formula.** Sum of wins ÷ number of wins; sum of losses ÷ number of losses.

**Cell.** `=E5/E3` and `=E6/(E1-E3)`.

**ETF-X.** Average win **$70.34**. Average loss **−$83.75**. Losses are bigger than wins, and only a 52.5% win rate keeps it near breakeven.

**Bad reading.** Looking at one without the other.

---

## Profit factor

**Plain words.** Dollars won for every dollar lost.

**Formula.** Sum of all winning trades ÷ |sum of all losing trades|. The bars mean "ignore the minus sign". Above 1 means net profit. Below 1 means net loss.

**Cell.** `=E5/ABS(E6)`. Notebook: `wins.pnl.sum() / abs(losses.pnl.sum())`.

**ETF-X.** 5,908.43 ÷ 6,364.68 = **0.93**. Gross 1.26. In-sample 1.21. Out-of-sample 0.52. Holdout 0.51.

**Too small when.** It is unstable when there are few losing trades: one large loss can halve it. "Monday only" out-of-sample had a profit factor of 1.18 from just 8 losing trades. Treat any profit factor from fewer than about 30 trades as a description. That cut-off is a rule of thumb.

**Bad reading.** "Profit factor 1.94, excellent." From 38 trades, chosen out of five weekday variants? It is noise until shown otherwise (`04` Stage 2d).

---

## Max drawdown

**Plain words.** The worst fall in account value from a previous high, before a new high was reached.

**Formula.** For each day: equity ÷ highest equity so far − 1. Max drawdown is the most negative of these. In dollars: equity − highest equity so far, most negative.

**Cell.** `=MIN(Data!U:U)`. Notebook: `(curve.equity / curve.equity.cummax() - 1).min()`.

**ETF-X.** **−20.11%**, which is −$2,350.61, from $11,686.15 on 2018-08-22 down to $9,335.54 on 2023-10-30. Equity here is marked at each close, including open positions.

**Too small when.** Max drawdown tends to get **worse** with longer samples, because there is more time for a bad run. A short test almost always understates it. Assume the future drawdown can be deeper than the backtest's.

**Bad reading.** "Max drawdown was only −4.5%." That figure is "Monday only", in-sample. It was in the market 3.1% of days. Low exposure means few chances to lose, not safety. Also bad: comparing drawdowns measured on closed trades only with drawdowns marked daily. Closed-trade drawdowns look smaller.

---

## Time under water (Level 3)

**Plain words.** How long equity stayed below a previous high.

**Formula.** Calendar days from a high to the next day that equity regains it. If it never regains it, count to the end of the data and say "not recovered".

**ETF-X.** 1,955 days from 2018-08-22 to 2023-12-29, **not recovered**.

**Bad reading.** Looking only at drawdown depth. A −10% fall lasting four years is harder to sit through than a −20% fall that recovers in two months.

---

## CAGR (compound annual growth rate)

**Plain words.** The steady yearly growth rate that would turn the starting equity into the ending equity over the test period.

**Formula.** (End equity ÷ start equity) ^ (1 ÷ years) − 1, where years = calendar days ÷ 365.25.

**Cell.** `=(E13/B8)^(1/E14)-1`.

**ETF-X.** ($9,543.75 ÷ $10,000) ^ (1 ÷ 9.988) − 1 = **−0.47% a year**. Gross: +1.37%. Buy and hold over the same dates: about +7.1%.

**Too small when.** Under about three years, a few trades dominate it, and annualising a short period exaggerates good and bad runs. That cut-off is a rule of thumb. Holdout example: −4.24% a year from 2 years and 29 trades.

**Bad reading.** Comparing the CAGR of a rule invested 11% of the time with buy-and-hold invested 100% of the time, without saying so. Also, this plan uses **fixed-dollar sizing**: the same $10,000 or the same $100 risk each trade, not growing with the account. So CAGR here describes the account, not compounding of the rule. If you change to sizing that grows with equity, CAGR changes.

---

## Exposure

**Plain words.** Share of trading days on which you held a position at the close.

**Formula.** Days holding ÷ total days.

**Cell.** `=AVERAGE(Data!K3:K)`. Notebook: `curve.in_mkt.iloc[1:].mean()`.

**ETF-X.** **11.1%**, so about 1 day in 9. Monday-only in-sample: 3.1%.

**Too small when.** Exposure is not about sample size. But low exposure means few trades, and every metric above becomes noisier.

**Bad reading.** "Lower exposure means less risk." It means less time at risk. Each position can still be large; H-001 v1 put the whole $10,000 in each trade. Also bad: assuming idle cash earns nothing. In real accounts it may earn interest, which a fair comparison would include.

---

## Noise band (Level 2 onward)

**Plain words.** A rough range within which the true average profit per trade could plausibly sit.

**Formula.** Expectancy ± 2 × standard deviation of trade profits ÷ √(trade count). The part after ± is "two standard errors". The **standard error** is the standard deviation divided by the square root of the count.

**Cell.** `=2*STDEV(FILTER(Data!Q3:Q,Data!J3:J=1))/SQRT(E1)`. Notebook: in `report()`.

**ETF-X.** −$2.85 ± $16.27. In-sample +$7.17 ± $19.35. Holdout −$28.62 ± $42.17.

**Bad reading.** Treating the band as exact. It is a minimum width. Real trade results are often lumpier than the formula assumes.

---

## Sharpe ratio (Level 3 only)

**Plain words.** Return per unit of bumpiness, scaled to a year. Proposed by William F. Sharpe. Higher means a smoother return for the same gain.

**Formula used here (approximate).** Average daily return ÷ standard deviation of daily returns × √252. Daily return = today's equity ÷ yesterday's equity − 1. This leaves out the **risk-free rate** (the return on cash), which the formal definition subtracts. Leaving it out flatters strategies when cash rates are high.

**Cell.** With column V = daily return: `=AVERAGE(Data!V3:V2608)/STDEV(Data!V3:V2608)*SQRT(252)`. Notebook: `sharpe(curve)` from Cell 17.

**ETF-X.** Net **−0.09**. Gross 0.36. Buy and hold 0.55. Holdout net −0.80.

**Too small when.** A rough standard error for an annual Sharpe ratio, under simplifying assumptions (returns independent and roughly normal), is √((1 + SR² ÷ 2) ÷ years). This is from Andrew Lo, "The Statistics of Sharpe Ratios", *Financial Analysts Journal*, 2002. ETF-X gross: √((1 + 0.36² ÷ 2) ÷ 9.988) = √(1.065 ÷ 9.988) = 0.33. Two standard errors is ±0.65, so −0.29 to +1.02, which includes zero. Ten years of data does not pin down a Sharpe ratio of 0.36.

**Bad reading.** "Sharpe 2 in my backtest." From how many years? Gross or net? With or without the risk-free rate? Is the sample one calm period? A high backtest Sharpe from a short or tuned sample is one of the most common ways people fool themselves.

---

## One-line summary card

Copy this under every result in your log:

```
H-00n vX | instrument | dates | costs: base/2x | trades N |
net $ (gross $) | expectancy $ +/- band | max DD % ($) | PF | win % | exposure % |
[Level 3: Sharpe, time under water] | variants tried so far: N
```
