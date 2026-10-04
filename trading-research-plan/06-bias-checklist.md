# 06 — Bias checklist

A **bias** here means a systematic way a backtest looks better than reality. Each item below gives what it is, how it shows up, an ETF-X or illustrative example, and **one detection step** you can run. Do every step before you believe a result. In Level 3 they are required before any pre-registered test.

Tick each box in your log, with the date and what you found.

---

## 1. Look-ahead

**What it is.** Using information in a decision that you would not have had at the time of the decision.

**How it shows up.**

- Buying at today's close because today's close is down. You only know the close once it has happened, too late to trade at it.
- Using the day's high or low to decide something before the day is over.
- Adjusted prices that use information from later dates, such as a split announced afterwards. Usually harmless for daily rules, but know that it exists.
- In a spreadsheet: a formula in row *t* that refers to row *t+1*.

**Example.** H-001 sees the setup at the close and buys at the **next** open (Module B6, rule 2). If you changed the rule to buy at the setup day's close, the backtest would get prices you could not have traded.

**Detection step.** Trace one trade by hand. For the entry and the exit, write each number the decision used and the moment it became known. Then write the moment of the fill. Every number must be known **before** the fill. In a spreadsheet, click the cells in the Buy and Sell columns and confirm their formulas only point to the same row or rows above.

- [ ] Done. Trade traced: ______

---

## 2. Overfitting (curve-fitting)

**What it is.** Shaping the rule to past noise by trying many variants and keeping the best. The more variants you try, the better the best one looks, even if none has an edge.

**How it shows up.** Results that fall sharply out-of-sample; "best" settings that are isolated spikes; settings that change every walk-forward window; filters with no reason behind them.

**Example.** The "Monday only" filter: in-sample profit factor 1.94 on 38 trades, chosen from five weekdays. Out-of-sample expectancy fell 71%, and its noise band included zero (`04` Stage 2d). The tuned 2/5 setting: +$1,049.82 in-sample, −$964.01 out-of-sample (`04` Stage 2c).

**Detection step.** Write the number of variants tried, counted from your hypothesis log, next to the result. If it is more than one, the result only counts once it has passed a single, pre-registered run on data no variant has touched.

- [ ] Done. Variants tried: ______

---

## 3. Survivorship

**What it is.** Testing only on instruments that still exist or did well, because they are the ones you can easily find today. Funds that closed, merged or failed are missing from today's lists, so the past looks kinder than it was.

**How it shows up.** "I tested on today's 20 biggest ETFs." Some of them became big **because** they did well in your test period. Many that did badly are no longer listed.

**Example.** *Illustration.* You pick five sector funds that are large today and test 2014–2023. The funds that shrank or closed in that period are not in your sample. Any rule that buys "strong sectors" will look better than it would have in 2014, when you could not know which funds would survive.

For a single, very large index fund such as SPY, survivorship risk is small. It still matters **why** you picked the instrument.

**Detection step.** For each instrument, answer in writing: "Would I have picked this on the first day of my test, using only what I knew then?" Check its launch date, and that it existed and was liquid at the start of your test.

- [ ] Done. Instrument, launch date, liquid at test start? ______

---

## 4. Cost omission

**What it is.** Leaving out commission, spread, slippage or fees, or using costs lower than you will really pay.

**How it shows up.** A rule with many small trades that looks great gross. Any result reported without "gross" or "net" written next to it.

**Example.** ETF-X H-001: +$1,451.21 gross, −$456.25 net, breakeven slippage about 0.035% per side (`04` Stage 2f). The same rule with $100 risk sizing had a larger cost share because of the $1 commission minimum (Module I5).

**Detection step.** Run and report the cost stress: 0×, 1×, 2× and 3× base slippage, with commissions from your actual IBKR plan. If the 2× line is negative, write "fragile to costs" next to the result.

- [ ] Done. Net at 1×: ______ 2×: ______ Breakeven: ______

---

## 5. Fill fantasy

**What it is.** Assuming fills you could not have got. This is related to cost omission, but it is about **which price** and **whether** you get filled, not just how much it costs.

**How it shows up.**

- Assuming you fill at the midpoint between bid and ask.
- Assuming a limit order always fills when the price touches the limit. In reality the price can touch your limit without your order filling.
- Assuming the stop fills exactly at the stop price after a gap.
- Assuming large orders fill at the same price as small ones.
- Assuming paper fills equal real fills.

**Example.** H-001 checks its stop at the close and exits at the next open, so a gap can make the loss bigger than planned. Module B2's illustration: planned $497.63, gapped loss $649.16.

**Detection step.** For each fill in your rule, name the IBKR order type that would produce it, for example "market-on-open, TIF = OPG". Then compare at least 10 paper fills of that type with the backtest's assumed price (Module E7). If you cannot name an order type, the fill is fantasy.

- [ ] Done. Entry order type: ______ Exit order type: ______ Measured slippage: ______

---

## 6. Regime change

**What it is.** The market's behaviour, as it matters to your rule, changes over time. A **regime** is a period with fairly stable behaviour. An effect real in one regime can vanish or reverse in the next.

**How it shows up.** Profits bunched in early years; a shrinking breakeven cost over time; walk-forward that picks different settings in each window; a holdout that fails even gross.

**Example.** ETF-X H-001: 2014–2016 made +$1,415.05, and the next seven years lost −$1,871.30 (`04` Stage 2g). Breakeven slippage fell from about 0.087% in-sample to about 0.035% on the full sample, and to "none" on the holdout (Module E4). ETF-X was built with an effect that reversed in March 2021 (`04`, "The reveal").

**Detection step.** Make a year-by-year table of trades and net profit. If more than half of total profit comes from less than a third of the years, write "regime-dependent" next to the result. Then ask what was different in those years. This threshold is a judgement; set your own in advance.

- [ ] Done. Share of profit from best third of years: ______

---

## Two more checks from the definition of done

### 7. Too few trades

**Detection step.** Compute the noise band on expectancy (`05`). If it includes zero, or the trade count is under 30, write "no conclusion" next to the result.

- [ ] Done. Trades: ______ Expectancy ± band: ______

### 8. One lucky window

**Detection step.** Remove the single best year from the result, without changing anything else, and recompute net profit. If it turns negative, the result depends on one window. This is a **diagnostic only**. Never remove a bad year to improve a result.

ETF-X example: the best year is 2014 (+$486.29). Net without it: −$456.25 − $486.29 = −$942.54.

- [ ] Done. Net without best year: ______
