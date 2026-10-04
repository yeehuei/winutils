# 00 — How to use this plan

## Who this is for

You can already place a buy or sell order on Interactive Brokers (IBKR). You know a market order from a limit order, and you can read a position. You have not studied statistics, programming, research design or quantitative finance.

This plan teaches you to test a trading idea honestly. It does not teach you a strategy that makes money. The main worked example loses money on purpose, so you practise stopping.

## What "done" means

When you finish, you can:

1. Turn something you noticed in the market into a **hypothesis**: a claim that the data could prove wrong. Then you write it as a rule with an entry, an exit, a size, and a list of things you will not trade.
2. Run that rule on past prices, with costs, in a free tool you installed yourself. That is a **backtest**.
3. Read the result, including a losing one, and say what it does not prove.
4. Spot the common ways a backtest lies: look-ahead, overfitting, ignored costs, too few trades, one lucky window.
5. Decide whether to **paper-trade** on IBKR, or not. Paper trading means placing simulated orders in a practice account with no real money.

Live trading is out of scope. Do not trade a rule with real money until you have a written paper-trading record for it. This plan does not get you there for any particular rule.

Done does **not** mean a job offer or a profitable live system.

## What you need

| Item | Cost | Used from |
|---|---|---|
| Your IBKR account with access to TWS (Trader Workstation, IBKR's desktop app) or Client Portal (IBKR's website) | Already have | Level 1 (quotes), Level 3 (paper orders) |
| A Google account for Google Sheets. Excel also works for everything except downloading prices. | Free | Level 1 |
| Python, JupyterLab, pandas, matplotlib, yfinance (install steps in Level 2) | Free | Level 2 |
| The two practice price files in `data/` | Included | Level 1 onwards |
| A notebook or text file for your hypothesis log | Free | Level 1 onwards |

You do not need paid data, a Bloomberg terminal, or any paid platform.

## The practice data: read this first

The folder `data/` holds two files for a made-up **ETF** called **ETF-X**. An ETF (exchange-traded fund) is a fund whose shares trade on a stock exchange like a stock. Many track a stock index.

- `etfx-practice-2014-2023.csv` has 2,607 trading days of daily prices: Date, Open, High, Low, Close.
- `etfx-holdout-2024-2025.csv` has 523 more days. **Do not open this file until Level 3, Module E3.** It is your sealed test. Once you open it, it is spent.

ETF-X is **synthetic**. A computer generated it to behave roughly like a **liquid** US stock-index fund. Liquid means it trades in large volume with a small spread, the gap between buying and selling prices. It is not real market history. We use it so every number in these notes can be checked exactly. When you type the formulas into your spreadsheet, you should get the same answers to the cent.

Every result on ETF-X is an **illustration of method**, not evidence about any real market. Each level also has you repeat the work on a real fund you can trade on IBKR, such as SPY. SPY is an ETF that tracks the S&P 500, an index of about 500 large US companies. Your real-data numbers will differ from the notes. That is expected.

The practice calendar has no market holidays. Real data does. The arithmetic is the same.

## The files and their order

| File | What it is | When to read it |
|---|---|---|
| `00-how-to-use-this-plan.md` | This page | First |
| `01-beginner.md` | Vocabulary, risk in dollars, expectancy, one hand-written rule, a spreadsheet backtest | Weeks 1–4 |
| `02-intermediate.md` | Python notebook, in-sample vs out-of-sample, costs, sizing, reading metrics together, walk-forward, rejecting a curve-fit | Weeks 5–8 |
| `03-expert.md` | Hypothesis log, pre-registration, held-out data, cost stress, neighbour check, paper deployment, decision memo | Weeks 9–12 |
| `04-hypothesis-to-backtest.md` | One strategy carried through all three levels to a "do not trade" decision | Read each stage after the matching level |
| `05-metric-sheet.md` | Each performance number: plain formula, minimum sample, bad readings | Reference, from Level 1 |
| `06-bias-checklist.md` | Six ways a backtest lies, one detection step each | Reference, from Level 1; required in Level 3 |
| `07-twelve-week-schedule.md` | Week-by-week plan at about 6 hours a week | Use to pace yourself |
| `08-glossary.md` | Every term, defined once | Look up any word you do not know |

Each level stands alone, but assumes you finished the one before it.

Each module in the three level files has the same parts:

1. **The idea.** What the module is about, in a few lines.
2. **Why it matters.** What goes wrong if you skip it.
3. **The rule.** What you do, stated so you could check yourself.
4. **Worked example.** Numbers, computed in the note or marked as an illustration.
5. **Clicks and cells.** Exactly what to click or type.
6. **Exercise.** One task. The answer is underneath, folded.
7. **You are fooling yourself if.** The traps for that module.

## Hours

About 6 hours a week for 12 weeks, so roughly 72 hours. About two-thirds of that is exercises, not reading. `07-twelve-week-schedule.md` breaks it down.

If you have less time, stretch the weeks. Do not skip exercises. The exercises are the course.

## What you may skip

- **If you already use spreadsheet formulas such as IF, AND and COUNTIF**, skim Module B5's "clicks" and go straight to building the backtest in B6.
- **If you already write Python**, skim Module I1's install steps. Still do the match check in I1. It proves your notebook and spreadsheet agree.
- **Never skip:** the losing worked example in `04`, the costs module (I4), the held-out data module (E3), and the decision memo (E8).
- **Skip for now:** anything about options, futures, short selling, leverage, intraday data or multiple instruments. This plan uses one long-only fund on daily prices. "Long-only" means you only buy and later sell. You never bet on a fall.

## Rules for the whole programme

1. A backtest is a description of the past under stated assumptions. It is not a forecast.
2. Write the rule before you look at results. Change it only by writing a new version with a new date.
3. Every result includes costs. A result without costs is labelled "gross" every time you mention it.
4. Every result states its trade count and date range.
5. A "do not trade" decision is a valid result. It is the most common one.
6. Do not automate live orders. Paper orders in Level 3 are placed by hand.

## Definition of done: a self-check

You are done when you can do all of these without the notes open:

- [ ] Explain one trade's risk in dollars, including the case where the price jumps past your stop.
- [ ] Write a falsifiable hypothesis and a complete rule card for a new idea.
- [ ] Run that rule with costs in a spreadsheet and in your notebook, and get the same trade count in both.
- [ ] Split your data into in-sample and out-of-sample before tuning anything.
- [ ] Read net profit, drawdown, profit factor, win rate and trade count together, and say whether the sample is too small.
- [ ] Name the bias in each of the six checklist items and run its detection step.
- [ ] Write a one-page decision memo that ends in "do not trade", and defend it.
- [ ] Place one paper order on IBKR, find its fill, and compare it to the official opening price.

## A note on sources

Two names come up in the notes:

- **William F. Sharpe** proposed the ratio of return to volatility now called the Sharpe ratio. His 1994 paper "The Sharpe Ratio" is in *The Journal of Portfolio Management*. Sharpe also keeps a copy on his Stanford web page.
- **Robert Pardo** wrote *The Evaluation and Optimization of Trading Strategies* (Wiley, 2nd edition 2008). It is the standard reference for walk-forward analysis. Use a library or buy it. These notes do not reproduce it.

Nothing in this plan recommends a broker, a data vendor, a course or a signal service. IBKR appears only because it is the broker you already use.
