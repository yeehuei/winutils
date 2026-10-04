# 01 — Beginner: one rule, by hand, with costs

**Goal of this level.** By the end you can explain one trade's risk in dollars. You will also have written one rule and run it on ten years of prices in a spreadsheet, with costs.

**Time.** Weeks 1–4, about 6 hours a week. See `07-twelve-week-schedule.md`.

**You need.** IBKR TWS or Client Portal (for looking at quotes only), Google Sheets, and the file `data/etfx-practice-2014-2023.csv`.

**Do not open** `data/etfx-holdout-2024-2025.csv`. It is for Level 3.

Modules:

- B1 — The words you need
- B2 — One trade's risk in dollars
- B3 — Expectancy in plain arithmetic
- B4 — From an observation to a written rule
- B5 — Getting prices into a spreadsheet
- B6 — Building the backtest, row by row
- B7 — Reading the result, and stopping

Then a level check at the end.

---

## B1 — The words you need

### The idea

A backtest is only as clear as its words. These are the words used in every later module. Each is also in `08-glossary.md`.

- **Price bar.** One day's prices for one instrument: **open** (first trade price of the day), **high**, **low**, **close** (last official price of the day).
- **Long.** You own shares and profit if the price rises. This plan only goes long.
- **Position.** What you hold right now: how many shares, bought at what price.
- **Order.** An instruction to your broker to buy or sell.
- **Fill.** The trade that actually happened: price, quantity, time. Your order is what you asked for. The fill is what you got.
- **Bid.** The highest price someone is currently willing to pay.
- **Ask.** The lowest price someone is currently willing to sell at. Also called the "offer".
- **Spread.** Ask minus bid. If you buy at the ask and immediately sell at the bid, you lose the spread.
- **Commission.** The fee your broker charges per order.
- **Slippage.** The difference between the price you assumed in your test and the price you actually got. It includes the spread, and any move between your decision and your fill.
- **Gross** means before costs. **Net** means after costs.
- **P&L.** Profit and loss, in dollars.
- **Adjusted price.** A price series changed so that dividends and share splits do not show up as fake price jumps.

### Why it matters

Most beginner backtests are wrong in vocabulary before they are wrong in maths. "I bought at the close" is not the same as "I filled at the close". You usually cannot know the close until after the market shuts.

### The rule

When you describe any trade, state four things: the order type, the assumed fill price, the costs, and whether the P&L is gross or net.

### Worked example: the cost of the spread

*Illustration, not a real quote.* A fund shows bid $100.00, ask $100.02.

- Buy 100 shares at the ask: 100 × $100.02 = $10,002.00.
- Sell them at once at the bid: 100 × $100.00 = $10,000.00.
- Loss from the spread alone: **$2.00**. That is 0.02 × 100.
- As a percentage of the price: 0.02 ÷ 100.01 ≈ **0.02%**. Here 100.01 is the midpoint between bid and ask.

Add commission and you have lost money on a trade where the price did not move.

### Worked example: why "adjusted" matters

*Illustration.* A fund closes at $100.00. Overnight it pays a $1.00 per-share dividend, so the next morning it opens near $99.00, even if nothing else changed.

- In an **unadjusted** series, that looks like a 1% drop. A rule that buys after down days would see a fake signal.
- Most free sources make an **adjusted** series by multiplying every earlier price by (1 − dividend ÷ previous close). Here that is 1 − 1 ÷ 100 = 0.99. The old $100.00 close becomes $99.00, and the fake drop disappears.

That is the usual method. Sources differ in details, so treat an adjusted series as close, not exact.

### Clicks and cells

Look at a real spread in IBKR:

- **TWS:** in a watchlist, type a ticker such as `SPY` into an empty row and press Enter. Choose the stock or ETF listing if asked. The **Bid** and **Ask** columns show the current quote.
- **Client Portal:** use the search box at the top, type the ticker, open the quote page. Bid and ask are shown near the price.

Write the bid, ask and time on paper. Do this at three different times of day, including the first five minutes after the US open (09:30 New York time).

**Check that you can trade it.** In TWS, set up a buy order for 1 share. **Do not transmit it.** Some account types and countries do not allow certain funds. For example, retail clients in the EU and UK usually cannot buy US-listed ETFs. If IBKR blocks the order, pick a large, liquid US stock or a fund you are allowed to trade, and use that ticker wherever these notes say SPY.

### Exercise

*Illustration.* A fund shows bid $449.98, ask $450.01. You buy 20 shares at the ask and sell at the bid. Commission is $1.00 per order. What is your total round-trip cost in dollars, and as a percentage of the $9,000 position?

<details><summary>Answer</summary>

- Spread: (450.01 − 449.98) × 20 = 0.03 × 20 = $0.60.
- Commission: 2 orders × $1.00 = $2.00.
- Total: **$2.60**.
- As a percentage: 2.60 ÷ 9,000 = **0.029%**.

Notice that the commission is bigger than the spread here. On small orders, the $1 minimum dominates.
</details>

> **You are fooling yourself if…**
> - you assume you can buy at the close you just used to make the decision;
> - you call a result "profit" without saying gross or net;
> - you use unadjusted prices for a rule that reacts to daily moves;
> - you look up the spread once, at a calm moment, and assume it is always that small.

---

## B2 — One trade's risk in dollars

### The idea

Before a trade, you should know roughly how much you lose if you are wrong. That needs three numbers: entry price, **stop** price, and number of shares. A stop is the price at which you have decided, in advance, that you were wrong and will sell.

### Why it matters

Without a dollar figure, "risk" is a feeling. With one, you can compare it to your account and decide whether the trade is too big.

### The rule

**Planned risk ($) = shares × (entry fill − stop price) + commissions.**

Then add one sentence: how the stop is executed, and what happens if the price jumps past it.

In this plan the stop is checked at the **close**. If the close is at or below the stop, you sell at the **next open**. So the real exit can be worse than the stop.

### Worked example

The first trade in the practice file (more detail in `04`, Stage 1c):

- Entry: open 100.30 on 2014-01-10. Slippage 0.05%, so entry fill = 100.30 × 1.0005 = **100.35015**.
- Shares: round down(10,000 ÷ 100.30) = **99**.
- Stop: 5% below entry fill = 100.35015 × 0.95 = **95.33**.
- Risk per share: 100.35015 − 95.33 = **$5.02**.
- Planned risk: 5.02 × 99 = $496.73, plus $2.00 commission = **about $499**.

As a share of a $10,000 account: 499 ÷ 10,000 ≈ **5%**. Stage 2 and Module I5 show how to make this smaller by buying fewer shares.

### Clicks and cells

Make a risk calculator in a new Google Sheet. Type labels in column A and values or formulas in column B:

| Cell | Type this |
|---|---|
| A1 | `Entry open` |
| B1 | `100.30` |
| A2 | `Slippage per side` |
| B2 | `0.0005` |
| A3 | `Stop distance` |
| B3 | `0.05` |
| A4 | `Dollars per trade` |
| B4 | `10000` |
| A5 | `Entry fill` |
| B5 | `=B1*(1+B2)` |
| A6 | `Shares` |
| B6 | `=ROUNDDOWN(B4/B1,0)` |
| A7 | `Stop price` |
| B7 | `=B5*(1-B3)` |
| A8 | `Commission per order` |
| B8 | `=MAX(1,0.005*B6)` |
| A9 | `Planned risk $` |
| B9 | `=B6*(B5-B7)+2*B8` |

B9 should show 498.73: the $496.73 above plus $2.00 commission.

### Exercise

The fifth trade in the practice file enters at the open of 2014-03-26, which is 96.19.

1. Compute shares, entry fill, stop price and planned risk in dollars.
2. Suppose the price closed below the stop and the next open was $90.00. *This is an illustration; it did not happen.* What is the actual loss, with exit slippage and commissions?

<details><summary>Answer</summary>

1. Shares = round down(10,000 ÷ 96.19) = round down(103.96) = **103**. Entry fill = 96.19 × 1.0005 = **96.2381**. Stop = 96.2381 × 0.95 = **91.4262**. Risk per share = **$4.8119**. Planned risk = 4.8119 × 103 = $495.63, plus $2.00 = **$497.63**.
2. Exit fill = 90.00 × 0.9995 = 89.955. Loss = (89.955 − 96.2381) × 103 − 2.00 = **−$649.16**. That is about 30% more than planned. A gap past a close-checked stop is the normal way losses exceed plans.
</details>

> **You are fooling yourself if…**
> - your risk number assumes you always exit exactly at the stop;
> - you set the stop after the trade starts going against you;
> - you leave commission out because "it's only a dollar";
> - you cannot say your risk in dollars before you place the order.

---

## B3 — Expectancy in plain arithmetic

### The idea

**Expectancy** is the average profit per trade. You can compute it two ways that give the same answer:

- total net profit ÷ number of trades; or
- (win rate × average win) − (loss rate × average loss).

**Win rate** is the share of trades that made money. **Loss rate** is 1 − win rate. Here "average loss" is written as a positive number.

### Why it matters

A high win rate feels good and tells you little. A rule can win 90% of the time and lose money if the losses are big. Expectancy combines how often and how much.

### The rule

Always compute expectancy **after costs**. If it is zero or negative, the rule does not pay for itself.

### Worked example

From the full ETF-X run in `04`, net of costs: 160 trades, win rate 52.5%, average win $70.34, average loss $83.75.

- 0.525 × 70.34 = 36.93
- 0.475 × 83.75 = 39.78
- Expectancy = 36.93 − 39.78 = **−$2.85 per trade**
- Check: total net −$456.25 ÷ 160 = **−$2.85**. Same answer.

Before costs, the same trades had expectancy **+$9.07**. Costs averaged $11.92 per trade. The rule's average edge, if it had one, was smaller than what it cost to trade.

### Clicks and cells

In a blank sheet:

| Cell | Type this |
|---|---|
| A1 | `Win rate` |
| B1 | `0.525` |
| A2 | `Average win` |
| B2 | `70.34` |
| A3 | `Average loss (positive)` |
| B3 | `83.75` |
| A4 | `Expectancy` |
| B4 | `=B1*B2-(1-B1)*B3` |

B4 shows −2.85 (rounded to cents).

### Exercise

*Illustration.* A rule wins 40% of the time. Average win $150, average loss $80. These are **gross** figures. Costs are $12 per trade.

1. What is the gross expectancy?
2. What is the net expectancy?
3. Is the win rate a problem?

<details><summary>Answer</summary>

1. 0.40 × 150 − 0.60 × 80 = 60 − 48 = **+$12 per trade** gross.
2. 12 − 12 = **$0** net. It does not pay for itself.
3. No. A 40% win rate is fine if wins are big enough. The problem is that costs eat the whole edge. (Strictly, costs also shrink each win and enlarge each loss, but subtracting $12 per trade from the average gives the same total.)
</details>

> **You are fooling yourself if…**
> - you quote win rate without average win and average loss;
> - you compute expectancy before costs and call it "the edge";
> - you trust an expectancy from 10 trades (Module I6 shows how wide the noise is).

---

## B4 — From an observation to a written rule

### The idea

An **observation** is something you noticed. A **hypothesis** is a claim the data could prove wrong. A **rule** is the hypothesis turned into instructions precise enough that two people would make the same trades.

A hypothesis that cannot be proven wrong is called **unfalsifiable**. "Markets overreact sometimes" is unfalsifiable. "After three lower closes, buying at the next open and selling after the first higher close earns more than costs, on average, over 2014–2023" can be checked and can fail.

### Why it matters

A vague idea can be re-read to fit any result. A written rule cannot. Writing it first is your main defence against fooling yourself.

### The rule

A rule card is complete when it states all of these:

1. **Instrument**: exactly which ticker.
2. **Data**: which prices, which dates. Nothing outside them.
3. **Setup**: what must be true at the close.
4. **Entry**: when and at what price you buy.
5. **Exit**: every way the trade ends, and at what price.
6. **Size**: how many shares.
7. **Costs**: commission and slippage assumptions.
8. **Not traded**: what you will not do.
9. **Research stop rule**: what result makes you stop testing this idea.

### Worked example

See the rule card H-001 v1 in `04`, Stage 1b. Read it now. Notice that the setup only uses information known at the close. The entry happens at the **next** open. That avoids using information you did not yet have, which is called **look-ahead** (see `06-bias-checklist.md`).

### Clicks and cells

Start your **hypothesis log**: a plain text file or notebook page where every idea gets an ID, a date, the rule card, and every result. Create it now. Paste H-001 v1 in as the first entry.

### Exercise

Turn this observation into a hypothesis and a full nine-part rule card:

> "SPY usually goes up after a big drop."

<details><summary>Answer</summary>

One acceptable version. Yours can differ in the numbers, but must have all nine parts.

```
RULE CARD  H-002 v1  "Big drop bounce"            Written: (date)
Hypothesis   After SPY closes down 2% or more from the previous close, buying at
             the next open and selling at the next day's close earns more than
             costs on average, 2014-2023. False if net expectancy <= $0.
Instrument   SPY only.
Data         Adjusted daily prices 2014-01-01 to 2023-12-31. Nothing later.
Setup        Today's close <= yesterday's close x 0.98.
Entry        Buy at next open. Only if flat.
Exit         Check at each close while holding; sell at the next open if
             the close is above the previous close, or at or below entry
             fill x 0.95 (stop), or this is the 3rd day held.
Size         Shares = round down($10,000 / entry open).
Costs        $0.005/share, $1 minimum per order; slippage 0.05% per side.
Not traded   Other tickers. Adding to a position. Missing-data days.
             Shorting. Leverage. Overrides.
Research     If net expectancy <= $0, or fewer than 30 trades, stop.
stop rule
```

Common mistakes: "big drop" not defined as a number; the exit left as "when it recovers"; no dates; no costs; no research stop rule. Also check that every exit can actually happen. A stop that can never be reached, because another exit always fires first, is decoration. Either delete it, or change the exits so it can fire.
</details>

> **You are fooling yourself if…**
> - your rule contains "usually", "big", "strong" or "when it looks right";
> - you wrote the rule after looking at a chart of the exact period you will test;
> - your card has no line for what you will not trade;
> - you have no research stop rule, so no result could make you quit.

---

## B5 — Getting prices into a spreadsheet

### The idea

A backtest needs clean daily prices in date order, oldest first. You will load the practice file first, then a real fund.

### Why it matters

Bad data makes a convincing, wrong backtest. A missing day, a duplicated day, or an unadjusted dividend can create trades that never could have happened.

### The rule

Before any backtest, run four checks:

1. **Count**: the number of rows matches what you expect, roughly 252 trading days a year for US markets.
2. **Order**: dates go up, oldest first, no duplicates.
3. **Blanks and zeros**: no empty or zero prices.
4. **Jumps**: look at the largest one-day change. Find out whether it is real or a data error.

### Worked example

Practice file `etfx-practice-2014-2023.csv`:

- 2,607 data rows, 2014-01-02 to 2023-12-29. ETF-X has no holidays, so about 261 rows a year rather than 252.
- Lowest close 95.41 (2014-04-29). Highest close 204.95 (2023-12-07).
- Largest one-day close-to-close change: **+3.05%** on 2017-07-03 (165.39 to 170.44).

### Clicks and cells

**Load the practice file into Google Sheets.**

1. Go to `sheets.google.com` and click **Blank spreadsheet**.
2. **File → Import → Upload**. Choose `etfx-practice-2014-2023.csv`.
3. Import location: **Replace current sheet**. Separator: **Detect automatically**. Leave "Convert text to numbers, dates, and formulas" ticked. Click **Import data**.
4. Double-click the tab name at the bottom and rename it `Data`.
5. You should see Date in A, Open in B, High in C, Low in D, Close in E, with headers in row 1 and data from row 2 to row 2608.

**Run the four checks.** Click an empty cell far to the right, for example `X1`, and type:

| Cell | Formula | Practice-file answer |
|---|---|---|
| X1 | `=COUNTA(A2:A)` | 2607 |
| X2 | `=COUNTBLANK(B2:E2608)` | 0 |
| X3 | `=COUNTIF(B2:E2608,"<=0")` | 0 |
| X4 | `=SUMPRODUCT(--(A3:A2608<=A2:A2607))` | 0 (dates out of order or repeated) |
| X5 | `=MIN(E2:E2608)` | 95.41 |
| X6 | `=MAX(E2:E2608)` | 204.95 |

For the largest one-day change, type `Chg` in `W1`, then in `W3` type `=E3/E2-1`. Copy `W3` down to `W2608`: click W3, press Ctrl+C (Cmd+C on Mac), type `W4:W2608` in the **Name box** (the small box left of the formula bar), press Enter, then Ctrl+V. Then `=MAX(ABS(W3:W2608))` in X7. In Google Sheets you may need to wrap it as `=ARRAYFORMULA(MAX(ABS(W3:W2608)))`. Answer: 0.0305. **Delete column W afterwards**. The backtest in B6 uses columns F to U, and W stays free.

**Load a real fund (do this after B6 works on the practice file).**

1. Add a new tab with the **+** at the bottom left. Name it `Raw`.
2. In `A1` of `Raw`, type:
   `=GOOGLEFINANCE("SPY","all",DATE(2014,1,1),DATE(2023,12,31),"DAILY")`
   It fills Date, Open, High, Low, Close and Volume, with a header row.
3. Select columns A to E of `Raw`, copy, then go to a new tab and use **Edit → Paste special → Values only**. This freezes the numbers so your result cannot change under you.
4. If the pasted cells show `#N/A`, Google may be blocking copies of its historical data. In that case, skip the paste and point your `Data` tab at `Raw` with formulas, for example `=Raw!A2`. Write in your log that the data is linked, not frozen.

**Limits of GOOGLEFINANCE.** As far as I know, its daily prices are adjusted for splits but **not for dividends**. I cannot confirm that from Google's documentation, so check it yourself. SPY pays a dividend each quarter. On each **ex-dividend date** (the first day a new buyer no longer gets the upcoming dividend), the unadjusted price drops by roughly the dividend. That can create a fake down day. Level 2 replaces this with adjusted prices from Python. Then you will compare the two.

**Excel users.** Excel opens the practice CSV directly (**File → Open**), and every formula in B5–B7 works the same. Excel's `STOCKHISTORY` function can fetch real prices, but it needs a Microsoft 365 subscription. If you do not have that, use Google Sheets for real data.

### Exercise

Using your loaded practice file, how many rows have a close **lower** than the previous close? Write the formula you used.

<details><summary>Answer</summary>

Use `=SUMPRODUCT(--(E3:E2608<E2:E2607))`. Answer: **1,262** down closes. There are also 1,338 up closes and 6 unchanged closes, making 2,606 changes from 2,607 rows.

After B6, check it a second way: it must equal `=SUM(F3:F2608)`. Two different formulas that agree are your evidence that the count is right. Unchanged closes count as "not down" in this rule. A complete rule card would say so.
</details>

> **You are fooling yourself if…**
> - you never looked at the largest one-day move in your data;
> - you used a live formula for prices and your result changed when you reopened the file;
> - you mixed adjusted and unadjusted prices in one sheet;
> - you loaded data past your test period "just to see".

---

## B6 — Building the backtest, row by row

### The idea

A **backtest** applies your rule to past prices one day at a time, as if you were living through each day. Each row is one day. Each column is one question you would ask that day.

### Why it matters

Building it yourself means you know exactly what the result assumes. A backtest you cannot explain row by row is a backtest you cannot trust.

### The rule

Two rules keep a spreadsheet backtest honest:

1. A formula in row *t* may use prices from row *t* and earlier only.
2. A decision made at today's close is executed at **tomorrow's** open, in the next row.

### Worked example

The first ten rows after building, practice file, rule H-001 v1:

| Date | Open | Close | F Down | G Streak | H Setup | I Buy | J Sell | K Pos | L Entry fill | M Shares | N Days | O Exit sig | Q Trade P&L | S Equity |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 2014-01-02 | 100.16 | 100.84 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10,000.00 |
| 2014-01-03 | 101.22 | 101.60 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10,000.00 |
| 2014-01-06 | 101.77 | 102.03 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10,000.00 |
| 2014-01-07 | 101.75 | 101.64 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10,000.00 |
| 2014-01-08 | 101.68 | 101.46 | 1 | 2 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10,000.00 |
| 2014-01-09 | 101.48 | 101.28 | 1 | 3 | **1** | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10,000.00 |
| 2014-01-10 | 100.30 | 98.91 | 1 | 4 | 1 | **1** | 0 | 1 | 100.3502 | 99 | 1 | 0 | 0 | 9,857.43 |
| 2014-01-13 | 99.11 | 99.40 | 0 | 0 | 0 | 0 | 0 | 1 | 100.3502 | 99 | 2 | **1** | 0 | 9,905.94 |
| 2014-01-14 | 99.27 | 100.91 | 0 | 0 | 0 | 0 | **1** | 0 | 0 | 0 | 0 | 0 | **−113.85** | 9,886.15 |
| 2014-01-15 | 100.73 | 99.42 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 9,886.15 |

**Equity** (column S) is the account value: start equity plus closed-trade profits, plus any open position valued at today's close.

Read it across. The setup appears at the close on 01-09. The buy happens on the next row's open. The exit signal appears at the close on 01-13. The sell happens on the next row's open. Equity on 01-10 is 10,000 + (98.91 − 100.35015) × 99 = **9,857.43**. That is the position marked at the close, before exit costs.

### Clicks and cells

**Step 1: a Settings tab.** Add a tab named `Settings`. Type:

| Cell | Label (column A) | Value (column B) |
|---|---|---|
| row 1 | `n_down` | `3` |
| row 2 | `max_hold` | `5` |
| row 3 | `stop` | `0.05` |
| row 4 | `dollars per trade` | `10000` |
| row 5 | `slippage per side` | `0.0005` |
| row 6 | `commission per share` | `0.005` |
| row 7 | `commission minimum` | `1` |
| row 8 | `start equity` | `10000` |

**Step 2: headers.** On the `Data` tab, type these headers in row 1, columns F to U:

`Down`, `Streak`, `Setup`, `Buy`, `Sell`, `Pos`, `EntryFill`, `Shares`, `DaysHeld`, `ExitSig`, `ExitFill`, `TradePnL`, `CumPnL`, `Equity`, `Peak`, `DD`

**Step 3: the first data row (row 2).** Type `0` in every cell from F2 to R2. Then type:

- S2: `=Settings!B8`
- T2: `=S2`
- U2: `0`

**Step 4: the formulas in row 3.** Type each one exactly. The `$` signs keep the Settings references fixed when you copy down.

| Cell | Column meaning | Formula |
|---|---|---|
| F3 | Down: close below yesterday's? | `=IF(E3<E2,1,0)` |
| G3 | Streak: down closes in a row | `=IF(F3=1,G2+1,0)` |
| H3 | Setup at this close? | `=IF(G3>=Settings!$B$1,1,0)` |
| I3 | Buy at this open? (flat yesterday, setup yesterday) | `=IF(AND(K2=0,H2=1),1,0)` |
| J3 | Sell at this open? (held yesterday, exit signal yesterday) | `=IF(AND(K2=1,O2=1),1,0)` |
| K3 | Position held at this close | `=IF(I3=1,1,IF(J3=1,0,K2))` |
| L3 | Entry fill price | `=IF(I3=1,B3*(1+Settings!$B$5),IF(K3=1,L2,0))` |
| M3 | Shares | `=IF(I3=1,ROUNDDOWN(Settings!$B$4/B3,0),IF(K3=1,M2,0))` |
| N3 | Days held | `=IF(K3=1,IF(I3=1,1,N2+1),0)` |
| O3 | Exit signal at this close | `=IF(K3=1,IF(OR(E3>E2,E3<=L3*(1-Settings!$B$3),N3>=Settings!$B$2),1,0),0)` |
| P3 | Exit fill price | `=IF(J3=1,B3*(1-Settings!$B$5),0)` |
| Q3 | Net trade P&L, on the sell row | `=IF(J3=1,(P3-L2)*M2-2*MAX(Settings!$B$7,Settings!$B$6*M2),0)` |
| R3 | Running total of closed trades | `=R2+Q3` |
| S3 | Equity marked at the close | `=Settings!$B$8+R3+IF(K3=1,(E3-L3)*M3,0)` |
| T3 | Highest equity so far | `=MAX(T2,S3)` |
| U3 | Drawdown from that high | `=S3/T3-1` |

Note Q3 uses L2 and M2, the previous row. On the sell row, the position is already closed, so L3 and M3 are back to zero.

The commission formula uses the same share count on both sides, and ignores IBKR's 1%-of-trade-value cap. The cap never binds on a $10,000 trade.

**Step 5: copy down.** Select F3:U3 and copy (Ctrl+C). Type `F4:U2608` in the Name box, press Enter, paste (Ctrl+V). Wait for it to calculate.

**Step 6: check against the worked example.** Your rows 2–11 must match the table above. If row 8 (2014-01-09) does not show Setup = 1, check G3 and H3 first.

### Exercise

The second trade enters at the open on 2014-02-26 (open 99.64) and exits at the open on 2014-02-28 (open 100.60). Compute its net P&L by hand. Then find the row in your sheet and compare.

<details><summary>Answer</summary>

- Shares = round down(10,000 ÷ 99.64) = round down(100.36) = **100**.
- Entry fill = 99.64 × 1.0005 = **99.68982**.
- Exit fill = 100.60 × 0.9995 = **100.5497**.
- Commission = max(1, 100 × 0.005) = $1.00 per side, **$2.00** total.
- Net = (100.5497 − 99.68982) × 100 − 2.00 = 85.99 − 2.00 = **+$83.99**.

Your sheet's Q column on 2014-02-28 should show 83.988.
</details>

> **You are fooling yourself if…**
> - any formula in row *t* refers to row *t+1* (that is look-ahead);
> - your buy uses the same row's close that produced the setup;
> - you hand-edited a cell "to fix" one trade;
> - you did not check your first trade against a hand calculation.

---

## B7 — Reading the result, and stopping

### The idea

A **summary** turns 2,607 rows into a few numbers. You read them together, compare gross with net, and check your result against the research stop rule you wrote in advance.

### Why it matters

This is where most people talk themselves into a rule. The research stop rule exists so the decision is made by the you who wrote the rule, not the you who wants it to work.

### The rule

1. Read the net result first.
2. Then the gross result, to see how much costs took.
3. Then trade count and date range.
4. Then compare with the research stop rule. If it says stop, stop. Write it in your log.

### Worked example

ETF-X, H-001 v1, 2014-01-02 to 2023-12-29. Net: **160 trades, −$456.25, expectancy −$2.85.** Gross: +$1,451.21, expectancy +$9.07.

The research stop rule says: "If the net result is zero or below, stop." **Stop.** The full write-up is in `04`, Stage 1.

### Clicks and cells

On the `Settings` tab, type these labels in column D and formulas in column E:

| Cell | Label (D) | Formula (E) | ETF-X answer (net) |
|---|---|---|---|
| row 1 | `Trades` | `=SUM(Data!J:J)` | 160 |
| row 2 | `Net profit` | `=SUM(Data!Q:Q)` | −456.25 |
| row 3 | `Winners` | `=COUNTIF(Data!Q:Q,">0")` | 84 |
| row 4 | `Win rate` | `=E3/E1` | 52.5% |
| row 5 | `Sum of wins` | `=SUMIF(Data!Q:Q,">0")` | 5,908.43 |
| row 6 | `Sum of losses` | `=SUMIF(Data!Q:Q,"<0")` | −6,364.68 |
| row 7 | `Profit factor` | `=E5/ABS(E6)` | 0.93 |
| row 8 | `Expectancy` | `=E2/E1` | −2.85 |
| row 9 | `Average win` | `=E5/E3` | 70.34 |
| row 10 | `Average loss` | `=E6/(E1-E3)` | −83.75 |
| row 11 | `Max drawdown` | `=MIN(Data!U:U)` | −20.11% |
| row 12 | `Exposure` | `=AVERAGE(Data!K3:K)` | 11.1% |
| row 13 | `End equity` | `=INDEX(Data!S:S,COUNTA(Data!A:A))` | 9,543.75 |
| row 14 | `Years` | `=(INDEX(Data!A:A,COUNTA(Data!A:A))-Data!A2)/365.25` | 9.99 |
| row 15 | `CAGR` | `=(E13/B8)^(1/E14)-1` | −0.47% |

In Excel, replace `Data!K3:K` with `Data!K3:K2608`.

Each line is defined in `05-metric-sheet.md`. **Profit factor** is total wins ÷ total losses. **Max drawdown** is the worst fall from a previous equity high. **Exposure** is the share of days you held a position. **CAGR** is the steady yearly growth rate that would turn the start equity into the end equity.

**To see the gross result:** set Settings B5, B6 and B7 to `0`. Write down the numbers. Then put back `0.0005`, `0.005` and `1`.

### Exercise

Set slippage (Settings B5) to `0.001`, double the base case. Leave commission as it is. What are the trade count and net profit? Why did the trade count not change?

<details><summary>Answer</summary>

**160 trades, net −$2,043.72.** Slippage changes the prices you pay and receive, not when the rule trades. The setup and exit signals use closes, not fills. (With a stop that checks against the entry *fill*, a cost change could in theory change one stop exit. Here the stop was never hit, so nothing moved.) Put B5 back to `0.0005`.
</details>

> **You are fooling yourself if…**
> - you looked at gross first and "remembered" it as the result;
> - you are already thinking of changing n_down to 2;
> - you report net profit without trade count and dates;
> - you treat the stop decision as "pause" rather than "stop".

---

## Level 1 check

Answer without notes. Then compare with the modules.

1. Using the third trade in the practice file (entry open 98.08 on 2014-03-12), explain the trade's planned risk in dollars to a friend. Include: shares, entry fill, stop, planned risk, and what happens if the price gaps below the stop.
2. Rebuild your spreadsheet on SPY 2014–2023 (B5, real fund). Record trade count, gross and net profit, and expectancy in your hypothesis log. Write two sentences on what the result does not prove.

<details><summary>Answer to 1</summary>

Shares = round down(10,000 ÷ 98.08) = **101**. Entry fill = 98.08 × 1.0005 = **98.129**. Stop = 98.129 × 0.95 = **93.223**. Risk per share = **$4.906**. Planned risk = 4.906 × 101 = $495.55, plus $2 commission = **about $497.55**, which is about 5% of a $10,000 account.

The stop is checked at the close and executed at the next open. If the price opens below the stop, I sell at that lower open, minus slippage, and lose more than $497.55. Planned risk is not maximum loss.
</details>

When both are done, read `04-hypothesis-to-backtest.md` Stage 1, then move to `02-intermediate.md`.
