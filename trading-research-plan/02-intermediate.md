# 02 — Intermediate: test honestly, and reject a curve-fit

**Goal of this level.** By the end you can reject a **curve-fit**: a rule whose good past results come from being shaped to noise in that particular data, not from a real effect.

**Time.** Weeks 5–8, about 6 hours a week.

**You need.** Everything from Level 1, plus a computer where you can install software. The holdout file stays closed.

**Assumed from Level 1.** Bar, fill, spread, slippage, gross and net, expectancy, rule card, research stop rule, and a working spreadsheet backtest of H-001 v1 that shows 160 trades and −$456.25.

Modules:

- I1 — Install a notebook and reproduce your spreadsheet
- I2 — Adjusted prices for a real fund
- I3 — In-sample and out-of-sample
- I4 — Costs and slippage, properly
- I5 — Position sizing from a stop
- I6 — Reading the numbers together
- I7 — Walk-forward, in words and in one run
- I8 — Rejecting a curve-fit

---

## I1 — Install a notebook and reproduce your spreadsheet

### The idea

A spreadsheet is good for seeing one rule. It is slow for running the same rule 16 times on different settings. **Python** is a free programming language. A **notebook** (JupyterLab) is a page in your web browser where you type short blocks of Python, called **cells**, and see the result under each one. **pandas** is a free Python add-on, called a **library** or **package**, for tables of data.

You will not learn programming in general. You will copy, run and change a small number of cells.

### Why it matters

The first job of any new tool is to give the **same answer as the old one**. If your notebook and spreadsheet disagree, one of them has a bug, and you do not yet know which.

### The rule

**Match check.** On the practice file, with H-001 v1 settings, the notebook must report 160 trades and net profit −$456.25. Do not go past this module until it does.

### Worked example

The output you should see after Cell 5 below:

```
trades                    160.00
net profit $             -456.25
CAGR %                     -0.47
max drawdown %            -20.11
win rate %                 52.50
profit factor               0.93
expectancy $/trade         -2.85
noise band +/- $           16.27
exposure %                 11.09
```

### Clicks and cells

**Install Python.** These are the steps at the time of writing. If python.org has changed its installer, follow its default prompts, then confirm with the version check in step 4.

1. Go to `python.org` → **Downloads**. Download the current Python 3 installer for your system.
2. **Windows:** run the installer. On the first screen, **tick "Add python.exe to PATH"**, then click **Install Now**.
   **Mac:** open the downloaded `.pkg` and click through. Then open **Applications → Python 3.x** and double-click **Install Certificates.command**. Without this, downloading prices can fail with a certificate error.
3. Open a terminal. A **terminal** is a window where you type commands.
   **Windows:** press the Windows key, type `cmd`, press Enter.
   **Mac:** press Cmd+Space, type `Terminal`, press Enter.
4. Check the install. Type the line for your system and press Enter:
   - Windows: `py --version`
   - Mac: `python3 --version`

   You should see `Python 3.` and a number. If Windows says `py` is not recognised, try `python --version`, and use `python` instead of `py` in all the steps below.
5. Install the libraries. Type one line and press Enter. It takes a few minutes.
   - Windows: `py -m pip install jupyterlab pandas matplotlib yfinance`
   - Mac: `python3 -m pip install jupyterlab pandas matplotlib yfinance`
6. Make a folder for your work and start JupyterLab:
   - Windows:
     ```
     mkdir %USERPROFILE%\backtest
     cd %USERPROFILE%\backtest
     py -m jupyterlab
     ```
   - Mac:
     ```
     mkdir ~/backtest
     cd ~/backtest
     python3 -m jupyterlab
     ```
   Your browser opens JupyterLab. Leave the terminal window open while you work. Close it when you are done.
7. Copy `etfx-practice-2014-2023.csv` into that `backtest` folder. It appears in the file list on the left of JupyterLab.
8. In JupyterLab click **Python 3 (ipykernel)** under **Notebook**. A new notebook opens.

**Running a cell.** Paste a block of code into a cell. Press **Shift+Enter** to run it. The result appears below. A new empty cell appears. Run cells in order, top to bottom. If you change a cell, run it again, then run everything below it again. If things get confusing, use **Kernel → Restart Kernel and Run All Cells**.

Lines starting with `#` are comments for you. Python ignores them.

**Cell 1: load the tools.**

```python
import math
import pandas as pd
```

**Cell 2: settings.** These match your spreadsheet's Settings tab.

```python
SETTINGS = {
    "n_down": 3,            # down closes in a row needed for a setup
    "max_hold": 5,          # exit after this many days held
    "stop": 0.05,           # stop distance below entry fill (5%)
    "notional": 10_000,     # dollars per trade when risk_usd is None
    "risk_usd": None,       # set to e.g. 100 to size from the stop (Module I5)
    "slip": 0.0005,         # slippage per side (0.05%)
    "comm_per_share": 0.005,
    "comm_min": 1.00,
    "start_equity": 10_000,
}
```

**Cell 3: load prices.**

```python
prices = pd.read_csv("etfx-practice-2014-2023.csv")
prices = prices[["Date", "Open", "High", "Low", "Close"]].dropna().reset_index(drop=True)
print(len(prices), "rows from", prices.Date.iloc[0], "to", prices.Date.iloc[-1])
```

Expected: `2607 rows from 2014-01-02 to 2023-12-29`.

**Cell 4: the rule, one day at a time.** Each line mirrors a spreadsheet column. Read the comments.

```python
def backtest(prices, s):
    O = prices["Open"].tolist(); C = prices["Close"].tolist(); D = prices["Date"].tolist()
    streak = 0; setup_y = 0; pos_y = 0; exit_y = 0       # _y = "yesterday"
    entry = 0.0; shares = 0; held = 0; realised = 0.0
    trades = []; equity = [s["start_equity"]]; in_mkt = [0]
    for t in range(1, len(prices)):
        streak = streak + 1 if C[t] < C[t-1] else 0      # columns F, G
        setup = 1 if streak >= s["n_down"] else 0         # column H
        buy = (pos_y == 0 and setup_y == 1)               # column I: act today on yesterday's signal
        sell = (pos_y == 1 and exit_y == 1)               # column J
        if sell:                                          # columns P, Q
            fill = O[t] * (1 - s["slip"])
            comm = 2 * max(s["comm_min"], s["comm_per_share"] * shares)
            pnl = (fill - entry) * shares - comm
            realised += pnl
            trades.append({"entry_date": entry_date, "exit_date": D[t], "entry_fill": entry,
                           "exit_fill": fill, "shares": shares, "days": held, "pnl": pnl})
        pos = 1 if buy else (0 if sell else pos_y)        # column K
        if buy:                                           # columns L, M, N
            entry = O[t] * (1 + s["slip"]); entry_date = D[t]; held = 1
            if s["risk_usd"] is None:
                shares = math.floor(s["notional"] / O[t])
            else:
                shares = math.floor(s["risk_usd"] / (entry * s["stop"]))
        elif pos == 1:
            held += 1
        ex = 0                                            # column O
        if pos == 1:
            if C[t] > C[t-1] or C[t] <= entry * (1 - s["stop"]) or held >= s["max_hold"]:
                ex = 1
        equity.append(s["start_equity"] + realised + ((C[t] - entry) * shares if pos == 1 else 0))
        in_mkt.append(pos)
        setup_y, pos_y, exit_y = setup, pos, ex
    curve = pd.DataFrame({"Date": pd.to_datetime(prices["Date"]), "equity": equity, "in_mkt": in_mkt})
    return pd.DataFrame(trades), curve

trades, curve = backtest(prices, SETTINGS)
print(trades.head())
```

The first row printed should be the 2014-01-10 trade with `pnl` −113.848715.

**Cell 5: the metric sheet.** Each line is defined in `05-metric-sheet.md`.

```python
def report(trades, curve, start_equity):
    n = len(trades)
    wins = trades[trades.pnl > 0]; losses = trades[trades.pnl <= 0]
    years = (curve.Date.iloc[-1] - curve.Date.iloc[0]).days / 365.25
    end = curve.equity.iloc[-1]
    peak = curve.equity.cummax()
    out = {
        "trades": n,
        "net profit $": trades.pnl.sum(),
        "CAGR %": ((end / start_equity) ** (1 / years) - 1) * 100,
        "max drawdown %": (curve.equity / peak - 1).min() * 100,
        "win rate %": len(wins) / n * 100,
        "profit factor": wins.pnl.sum() / abs(losses.pnl.sum()),
        "expectancy $/trade": trades.pnl.mean(),
        "noise band +/- $": 2 * trades.pnl.std() / math.sqrt(n),
        "exposure %": curve.in_mkt.iloc[1:].mean() * 100,
    }
    return pd.Series(out).round(2)

print(report(trades, curve, SETTINGS["start_equity"]))
```

**Cell 6: a chart of equity.**

```python
curve.plot(x="Date", y="equity", title="ETF-X, H-001 v1, equity in $ (net)")
```

### Exercise

Set `"slip": 0` and `"comm_per_share": 0` and `"comm_min": 0` in Cell 2. Re-run Cells 2 to 5. What net profit and win rate do you get? Does it match your spreadsheet's gross run from B7? Put the settings back afterwards.

<details><summary>Answer</summary>

Net profit **+$1,451.21**, win rate **60.0%**, 160 trades. It matches the gross run in `04` Stage 1d. If yours does not, compare the first five trades line by line with your spreadsheet's Q column.
</details>

> **You are fooling yourself if…**
> - you skipped the match check because "the code looks right";
> - you edited a cell and did not re-run the cells below it;
> - you copied code from somewhere else that you cannot explain line by line;
> - your notebook gives a different trade count and you picked whichever looked better.

---

## I2 — Adjusted prices for a real fund

### The idea

**yfinance** is a free, unofficial Python library that downloads prices from Yahoo Finance's public pages. With `auto_adjust=True` it returns Open, High, Low and Close all adjusted for splits and dividends. That fixes the dividend problem from Level 1's GOOGLEFINANCE data.

### Why it matters

A rule that reacts to down days will see fake down days on every ex-dividend date in unadjusted data. Those trades could never have made the money the backtest says, because the "drop" was the dividend being paid to you.

### The rule

1. Download once. Save to a CSV file. Run backtests from the CSV, not from a fresh download each time.
2. Run the four data checks from B5 in code.
3. **Never load data after your test period.** For this level that means nothing after 2023-12-31.

### Worked example

The data checks on the practice file give:

```
rows 2607 | first 2014-01-02 | last 2023-12-29
missing values 0 | zero or negative prices 0
duplicate dates 0 | dates out of order 0
largest one-day close change 0.0305 on 2017-07-03
```

### Clicks and cells

**Cell 7: download a real fund once and save it.** Change `"SPY"` to the ticker you checked in Module B1.

```python
import yfinance as yf
raw = yf.download("SPY", start="2014-01-01", end="2024-01-01", auto_adjust=True, progress=False)
if raw.empty:
    raise SystemExit("Download failed: see the note under this cell.")
if isinstance(raw.columns, pd.MultiIndex):          # newer yfinance versions return two header rows
    raw.columns = raw.columns.get_level_values(0)
real = raw.reset_index()[["Date", "Open", "High", "Low", "Close"]]
real["Date"] = pd.to_datetime(real["Date"]).dt.strftime("%Y-%m-%d")
real.to_csv("spy-adjusted-2014-2023.csv", index=False)
print(len(real), real.Date.iloc[0], real.Date.iloc[-1])
```

The `end` date in yfinance is not included, so `2024-01-01` stops at the last day of 2023.

If this fails, run `py -m pip install --upgrade yfinance` (Mac: `python3 -m pip ...`) in the terminal and try again. yfinance depends on Yahoo's pages and sometimes breaks. If it still fails, use your Google Sheets data. Save it with **File → Download → Comma-separated values**, and write in your log that it is not dividend-adjusted.

**Cell 8: data checks.** Works on any price table.

```python
def check(p):
    d = pd.to_datetime(p.Date)
    chg = p.Close.pct_change()
    print("rows", len(p), "| first", p.Date.iloc[0], "| last", p.Date.iloc[-1])
    print("missing values", int(p.isna().sum().sum()),
          "| zero or negative prices", int((p[["Open", "High", "Low", "Close"]] <= 0).sum().sum()))
    print("duplicate dates", int(d.duplicated().sum()), "| dates out of order", int((d.diff().dt.days <= 0).sum()))
    print("largest one-day close change", round(chg.abs().max(), 4), "on", p.Date[chg.abs().idxmax()])

check(prices)
```

To run your rule on the real fund, change Cell 3's file name to `"spy-adjusted-2014-2023.csv"` and re-run Cells 3 to 6. Change it back to the practice file before Module I3.

### Exercise

Your Level 1 SPY spreadsheet used GOOGLEFINANCE prices, probably not dividend-adjusted. Your notebook now uses adjusted prices for the same dates.

1. Before running anything, write down which way you expect the trade count to move, and why.
2. Run both. Record the trade counts and net profits in your log.

<details><summary>Answer</summary>

1. A reasonable prediction: unadjusted data has about four extra small "drops" a year, one per quarterly ex-dividend date. Some of these complete a three-down-close streak. So unadjusted data may show **slightly more** trades. Across 10 years the difference should be small: a handful of trades, not dozens. Each quarterly drop is only around a third to a half of a percent for a broad index fund.
2. There is no single right number. Your data source and date of download decide it. The right answer is a written comparison: "Unadjusted: N1 trades, $X net. Adjusted: N2 trades, $Y net. Difference explained by: ..." If the difference is large, look for a data error before blaming dividends.
</details>

> **You are fooling yourself if…**
> - each run downloads fresh data, so you cannot reproduce last week's result;
> - you never ran the data checks on the real file;
> - your file quietly includes 2024 because you left out the `end` date;
> - you assume "adjusted" means "correct". It means "adjusted by that source's method".

---

## I3 — In-sample and out-of-sample

### The idea

Split your data in two before you look at results:

- **In-sample (IS):** the part you may look at, tune and re-run as often as you like.
- **Out-of-sample (OOS):** the part you run **once**, on the final rule, to check whether the in-sample result survives.

The split is by **date**. In-sample is earlier, out-of-sample later, so you are always testing on a "future" the rule has not seen.

### Why it matters

Every time you look at a result and change the rule, you fit the rule a little more to that data. After enough changes, in-sample results describe your changes, not the market. Out-of-sample is the only part of the data your changes have not touched.

### The rule

1. Choose the split before running anything. A common choice is the first 70% in-sample and the last 30% out-of-sample.
2. Write the split in your rule card.
3. Tune only on in-sample.
4. Run out-of-sample once. If you change the rule after seeing out-of-sample, it is no longer out-of-sample. Say so in your log.

### Worked example

ETF-X: in-sample 2014-01-02 to 2020-12-31 (1,826 days), out-of-sample 2021-01-01 to 2023-12-29 (781 days). That is 70% and 30% of 2,607 days. Full numbers are in `04`, Stage 2a:

- In-sample: 110 trades, net **+$788.40**.
- Out-of-sample: 50 trades, net **−$1,244.65**.

**Honesty note.** In Level 1 you already saw all of 2014–2023, so this split is a drill, not a real test. Level 3 uses a file you have not opened.

### Clicks and cells

**Cell 9: split and run both halves.**

```python
cut = "2020-12-31"
ins = prices[prices.Date <= cut].reset_index(drop=True)
oos = prices[prices.Date > cut].reset_index(drop=True)
for name, part in [("in-sample", ins), ("out-of-sample", oos)]:
    t, c = backtest(part, SETTINGS)
    print("----", name)
    print(report(t, c, SETTINGS["start_equity"]))
```

**Spreadsheet version.** Make two copies of your whole spreadsheet (**File → Make a copy**).

- **In-sample copy:** on `Data`, delete every row after 2020-12-31.
- **Out-of-sample copy:** on `Data`, delete rows 2 to the last 2020 row, so that row 2 is 2021-01-01. Row 2's formulas now show `#REF!` because the rows they pointed to are gone. Reset row 2 as in B6 Step 3: `0` in F2 to R2, `=Settings!B8` in S2, `=S2` in T2, `0` in U2.

The summary formulas on `Settings` work unchanged in both copies. Fiddly, but it shows you exactly what the code does. Each half starts flat, like the code.

### Exercise

Run the tuning grid below on **in-sample only**. Which setting is best, and what does it make out-of-sample?

**Cell 10: in-sample grid.**

```python
rows = []
for n_down in [2, 3, 4, 5]:
    for max_hold in [3, 5, 7, 10]:
        s = dict(SETTINGS, n_down=n_down, max_hold=max_hold)
        t, c = backtest(ins, s)
        rows.append({"n_down": n_down, "max_hold": max_hold, "net": round(t.pnl.sum()), "trades": len(t)})
grid = pd.DataFrame(rows)
print(grid.pivot(index="n_down", columns="max_hold", values="net"))
```

<details><summary>Answer</summary>

Best in-sample: **n_down = 2, max_hold = 5**, +$1,049.82 on 234 trades. Out-of-sample (re-run Cell 9 with `SETTINGS["n_down"] = 2`): **−$964.01** on 87 trades. Reset `n_down` to 3 afterwards.

The tuned rule did better than the original in-sample, by about $261, and still lost out-of-sample.
</details>

> **You are fooling yourself if…**
> - you chose the split date after seeing where the equity curve turned;
> - you ran out-of-sample, changed something, and ran it again;
> - your in-sample and out-of-sample overlap;
> - you split randomly by day instead of by date. Trades next to each other in time then end up on both sides.

---

## I4 — Costs and slippage, properly

### The idea

Every trade pays:

- **Commission:** set by your broker's price plan.
- **Spread and slippage:** set by the market and by how you trade.
- **Regulatory and exchange fees:** small charges on some orders. On sales there is usually a tiny US regulatory fee. These are ignored here because they are a few cents on a $10,000 trade. Check the current rates on IBKR's site if you trade much larger sizes.

### Why it matters

Short-term rules trade often and aim for small gains per trade. Costs are a fixed bite out of each one. Stage 2f in `04` shows ETF-X going from +$1,451 to −$456 because of costs alone.

### The rule

1. Use your **actual** IBKR price plan for commission.
2. Assume slippage of at least the typical spread at the time you trade, plus a margin.
3. Always run the **cost stress**: 0×, 1×, 2× and 3× your base slippage.
4. Compute the **breakeven slippage**: the slippage at which net profit is zero. Compare it with fills you have actually received.

### Worked example

**IBKR Pro "Fixed" plan for US stocks and ETFs, at the time of writing:** $0.005 per share, minimum $1.00 per order, maximum 1% of trade value. Check IBKR's current commissions page for your plan. The "Tiered" plan and the "Lite" plan differ. Lite is not offered in every country.

| Order | Per-share charge | Charged |
|---|---|---|
| 20 shares | 20 × 0.005 = $0.10 | **$1.00** (the minimum) |
| 99 shares | $0.495 | **$1.00** |
| 300 shares | $1.50 | **$1.50** |
| 2,000 shares | $10.00 | **$10.00** |

**Slippage from a fill.** *Illustration.* Your market-on-open buy filled at $450.12. The official opening price was $450.00. Slippage = (450.12 − 450.00) ÷ 450.00 = **0.027%**. For a sell, a fill **below** the open is slippage against you.

**Cost stress on ETF-X**, H-001 v1, 2014–2023 (from `04` Stage 2f): gross +$1,451.21, base −$456.25, double −$2,043.72, triple −$3,631.18. Breakeven slippage is about **0.035% per side**. It was found by trying values: −$456.25 at 0.05%, positive at 0.034%, negative at 0.036%.

### Clicks and cells

**Cell 11: cost stress.**

```python
for mult in [0, 1, 2, 3]:
    s = dict(SETTINGS, slip=0.0005 * mult)
    if mult == 0:
        s.update(comm_per_share=0, comm_min=0)
    t, c = backtest(prices, s)
    pf = t[t.pnl > 0].pnl.sum() / abs(t[t.pnl <= 0].pnl.sum())
    print(f"costs x{mult}: net {t.pnl.sum():,.2f}   profit factor {pf:.2f}   trades {len(t)}")
```

**Finding a fill on IBKR** (for when you paper-trade in Level 3; menu names as of writing):

- **TWS:** the **Trades** tab in the Activity or Orders panel lists each execution with price and time. Right-click a trade for details.
- **Client Portal:** **Portfolio** or **Trade** menu → **Orders & Trades** → **Trades**. Or **Performance & Reports → Statements** → a **Trade Confirmation** report for a date.
- **The official open** for comparison: the daily bar's Open in a chart in TWS, or the Open column of next day's yfinance download.

### Exercise

You paper-trade 10 opens of a fund. Your fills against the official open are, in % per side: 0.02, 0.05, 0.01, 0.08, 0.03, 0.04, 0.02, 0.06, 0.03, 0.06.

1. What is the average slippage per side?
2. Would H-001 v1 on ETF-X break even at that slippage?
3. What is wrong with deciding from 10 fills?

<details><summary>Answer</summary>

1. Sum = 0.40. Average = 0.40 ÷ 10 = **0.040%** per side.
2. No. Breakeven is about 0.035%. At 0.040% the rule loses, somewhere between the −$456.25 at 0.05% and $0 at 0.035%.
3. Ten fills is a small sample, and fills vary with the day's volatility. Calm days fill tightly. The days the rule trades, after three down closes, may be the more nervous days, with wider spreads. Measure fills **on the days the rule would actually trade**, not on random days.
</details>

> **You are fooling yourself if…**
> - your backtest has no slippage because "it's a liquid ETF";
> - you used IBKR Lite's $0 commission but you are on a plan that charges;
> - you measured slippage on calm days and applied it to a rule that trades after selloffs;
> - you never ran the cost stress, or ran it and only reported the 0× line.

---

## I5 — Position sizing from a stop

### The idea

In Level 1 you bought $10,000 of fund each trade, and your risk per trade came out at about $500. **Sizing from a stop** works backwards. You choose how many dollars you are willing to risk, and the share count follows.

**Shares = round down(dollars at risk ÷ (entry fill − stop price)).**

### Why it matters

Risk becomes a decision, not an accident of price. A wider stop means fewer shares, and the dollar risk stays the same.

### The rule

1. Pick a fixed dollar risk per trade, often 0.5% to 1% of the account. This is a common convention, not a law.
2. Compute shares from it. Round **down**.
3. Re-check costs. Smaller positions make the $1 commission minimum a bigger share of each trade.
4. Remember that gaps past the stop can still exceed the planned risk (Module B2).

### Worked example

Account $10,000. Risk 1% = **$100**. First ETF-X trade: entry fill 100.35015, stop 5% below.

- Risk per share = 100.35015 × 0.05 = **$5.0175**.
- Shares = round down(100 ÷ 5.0175) = round down(19.93) = **19**.
- Position = 19 × 100.35 ≈ **$1,907**, not $10,000.

Full run on ETF-X, 2014–2023, with $100 risk per trade:

| | $10,000 per trade (Level 1) | $100 risk per trade |
|---|---|---|
| Trades | 160 | 160 |
| Gross profit | +$1,451.21 | +$277.17 |
| Net profit | −$456.25 | **−$349.50** |
| Average cost per trade | $11.92 | $3.92 |
| Cost as % of a typical position | ≈ 0.12% | ≈ 0.2% |
| Worst trade | −$475.42 | −$96.68 |
| Max drawdown | −20.11% | −5.81% |

The loss is smaller in dollars because the positions are smaller. The rule did not get better. In fact costs take a **bigger** share of each trade, because the $1 minimum commission is now about 0.05% of a $1,900 position on each side.

### Clicks and cells

**Cell 12: risk-based sizing.**

```python
s = dict(SETTINGS, risk_usd=100)
t, c = backtest(prices, s)
print(t[["entry_date", "entry_fill", "shares", "exit_fill", "pnl"]].head(3))
print(report(t, c, SETTINGS["start_equity"]))
print("worst trade $", round(t.pnl.min(), 2))
```

**Spreadsheet version.** Add `risk dollars` in Settings A9 with `100` in B9. Change M3 to:
`=IF(I3=1,ROUNDDOWN(Settings!$B$9/(B3*(1+Settings!$B$5)*Settings!$B$3),0),IF(K3=1,M2,0))`
and copy down.

### Exercise

*Illustration.* Account $25,000. You risk 0.5% per trade. Entry fill $50.00. Stop 4% below the entry fill. IBKR Fixed commissions.

1. Shares?
2. Position size in dollars?
3. Commission per side, and round-trip commission as % of the position?

<details><summary>Answer</summary>

1. Risk = 0.005 × 25,000 = $125. Risk per share = 50.00 × 0.04 = $2.00. Shares = round down(125 ÷ 2.00) = round down(62.5) = **62**.
2. 62 × 50.00 = **$3,100**.
3. Per-share charge 62 × 0.005 = $0.31, so the **$1.00 minimum** applies on each side. Round trip $2.00 ÷ 3,100 = **0.065%**, before any slippage.
</details>

> **You are fooling yourself if…**
> - smaller losses after resizing make you think the rule improved;
> - you round shares up;
> - you set the stop distance from what makes the share count look nice;
> - your planned risk ignores the gap case.

---

## I6 — Reading the numbers together

### The idea

No single number tells you whether a backtest is good. Read them as a group, in a fixed order, and always with the trade count.

Here is one new tool. The **noise band** on expectancy is ± 2 × (standard deviation of trade profits) ÷ √(number of trades). **Standard deviation** measures how spread out the individual trade results are; spreadsheets compute it with `STDEV`. The band is a rough range within which the true average per trade could plausibly sit.

This is an approximation. It assumes trades are independent and their results are not extremely lopsided. Neither is exactly true. Treat it as a minimum width of your uncertainty, not a precise range.

### Why it matters

A rule with 38 trades, a 63% win rate and a profit factor of 1.94 looks excellent. The noise band can show it is indistinguishable from zero.

### The rule

Read in this order:

1. **Trade count and dates.** Under 30 trades: describe, do not conclude. 30 to 100: only large effects show through. These cut-offs are rules of thumb.
2. **Net profit and expectancy, with the noise band.** If the band includes zero, the result has not shown an edge.
3. **Max drawdown against net profit.** If the worst fall is bigger than the total profit, you would likely have quit during the fall.
4. **Profit factor and win rate together.** A high win rate with a profit factor near 1 means small wins and big losses.
5. **Exposure.** Low exposure means few days at risk. Do not compare its profit directly with a strategy that is always invested.

### Worked example

Four results from ETF-X, all net:

| | Full, 3/5 | In-sample, 3/5 | In-sample, Monday only | Out-of-sample, Monday only |
|---|---|---|---|---|
| Trades | 160 | 110 | 38 | 20 |
| Net profit | −$456.25 | +$788.40 | +$706.47 | +$106.74 |
| Expectancy | −$2.85 | +$7.17 | +$18.59 | +$5.34 |
| Noise band | ± $16.27 | ± $19.35 | ± $30.94 | ± $39.02 |
| Band includes 0? | Yes | Yes | Yes | Yes |
| Max drawdown | −$2,350.61 | −$1,177.14 | −$491.92 | −$406.78 |
| Profit factor | 0.93 | 1.21 | 1.94 | 1.18 |
| Win rate | 52.5% | 55.5% | 63.2% | 60.0% |

Reading the "In-sample, Monday only" column in order:

1. 38 trades: in the 30–100 range, so only a large effect would show.
2. +$18.59 ± $30.94, so −$12.35 to +$49.53. Includes zero. No edge shown.
3. Drawdown $492 against $706 profit. Fine on its own, but see 1 and 2.
4. Profit factor 1.94 and win rate 63% look strong. Step 2 already says they could be luck.
5. Exposure 3.1%. It was in the market very little, which is why the drawdown is small.

Verdict: **not evidence**. Module I8 finishes the job.

### Clicks and cells

The noise band is already in Cell 5's report. In your spreadsheet, add to the Settings summary:

- D16 `Std dev of trades`, E16 `=STDEV(FILTER(Data!Q3:Q,Data!J3:J=1))`. In Excel 365 use `=STDEV.S(FILTER(Data!Q3:Q2608,Data!J3:J2608=1))`.
- D17 `Noise band +/-`, E17 `=2*E16/SQRT(E1)`.

On ETF-X full sample: E16 = 102.89, E17 = 16.27.

### Exercise

*Illustration.* A backtest reports: 42 trades, net +$1,260, win rate 71%, profit factor 2.1, max drawdown −$1,900, standard deviation of trade profits $140. Read it in the five steps. Is it evidence of an edge?

<details><summary>Answer</summary>

1. 42 trades: in the 30–100 range, so only a large effect would show.
2. Expectancy = 1,260 ÷ 42 = **$30.00**. Noise band = 2 × 140 ÷ √42 = 2 × 140 ÷ 6.48 = **± $43.20**. Range −$13.20 to +$73.20. **Includes zero.**
3. Max drawdown $1,900 is **larger** than total net profit $1,260. You would have spent part of the test underwater by more than the whole gain.
4. 71% win rate and profit factor 2.1 look good but rest on 42 trades.
5. Exposure not given. Ask for it.

**Not evidence of an edge.** Possibly worth a pre-registered test on new data, if there is a reason to expect the effect.
</details>

> **You are fooling yourself if…**
> - you lead with win rate or profit factor;
> - you never compute the noise band because the result "looks obviously good";
> - a drawdown larger than total profit doesn't bother you because "it recovered";
> - you compare a 3%-exposure rule's drawdown with buy-and-hold's.

---

## I7 — Walk-forward, in words and in one run

### The idea

A single in-sample/out-of-sample split gives you one test. **Walk-forward analysis** gives you several, in order through time:

1. Choose a training window, for example 3 years, and a test window, for example 1 year.
2. Tune the rule on the training window. Pick the best setting by a criterion you fixed in advance, such as highest net profit.
3. Run that setting, unchanged, on the test window. Record the result.
4. Slide both windows forward by one test window. Repeat.
5. Join the test-window results. That joined record is the walk-forward result. Training results are never counted.

Robert Pardo's *The Evaluation and Optimization of Trading Strategies* (2nd ed., Wiley, 2008) is the standard reference.

### Why it matters

It copies what you would really do: re-tune now and then, then trade forward. It shows whether **the tuning process** works, not just one lucky setting. It also shows whether the chosen settings stay stable.

### The rule

1. Fix the window lengths, the setting grid and the selection criterion before running.
2. Count only test windows.
3. Report the setting chosen in each window. If it jumps around, the "optimum" is mostly noise.

### Worked example

ETF-X, 3-year training, 1-year test, 16 settings, picked by highest training net profit. Full table in `04` Stage 2e. Summary:

- 7 test years (2017–2023), **124 trades, −$1,873** total.
- Picked settings: 2/5, 2/5, 4/3, 4/3, 4/3, 2/7, 5/3. Unstable.
- In 2023 the best training result was −$10. The process still traded. A better process would say in advance "if the best training result is not positive, do not trade next year". You may only add that rule **before** running, not after.

### Clicks and cells

**Cell 13: walk-forward.**

```python
TRAIN_YEARS = 3
results = []
for test_year in range(2014 + TRAIN_YEARS, 2024):
    train = prices[(prices.Date >= f"{test_year - TRAIN_YEARS}-01-01") &
                   (prices.Date <= f"{test_year - 1}-12-31")].reset_index(drop=True)
    test = prices[(prices.Date >= f"{test_year}-01-01") &
                  (prices.Date <= f"{test_year}-12-31")].reset_index(drop=True)
    best = None
    for n_down in [2, 3, 4, 5]:
        for max_hold in [3, 5, 7, 10]:
            t, c = backtest(train, dict(SETTINGS, n_down=n_down, max_hold=max_hold))
            if best is None or t.pnl.sum() > best[0]:
                best = (t.pnl.sum(), n_down, max_hold)
    t, c = backtest(test, dict(SETTINGS, n_down=best[1], max_hold=best[2]))
    results.append({"test year": test_year, "picked n_down": best[1], "picked max_hold": best[2],
                    "train net": round(best[0]), "test trades": len(t), "test net": round(t.pnl.sum())})
wf = pd.DataFrame(results)
print(wf)
print("walk-forward total:", wf["test net"].sum(), "on", wf["test trades"].sum(), "trades")
```

Each window starts flat. A trade open at a window's end is not counted. That is a small simplification. Note it in your log.

Doing walk-forward in a spreadsheet is possible but slow: one copy of the sheet per window and setting, 7 × 16 = 112 runs. That is why this level moves to the notebook.

### Exercise

Change `TRAIN_YEARS` to 2 and re-run. Report the number of test years, total trades, total net, and how many different settings were picked.

<details><summary>Answer</summary>

Test years 2016–2023 (**8 years**), **171 trades**, total **−$2,515**. Picked settings: 3/3, 2/5, 2/5, 4/3, 2/5, 2/7, 4/5, 5/3, so **six different settings** in eight windows. Shorter training made it worse and even less stable. Do not now try 4, 5 and 6 years to find the one that works. That is tuning the walk-forward itself.
</details>

> **You are fooling yourself if…**
> - you count training-window profits in the result;
> - you change the grid or criterion after seeing the walk-forward and run it again;
> - you try several window lengths and report the best one;
> - the picked settings change every window and you call the average "robust".

---

## I8 — Rejecting a curve-fit

### The idea

A curve-fit is a rule whose past results come from fitting noise. You cannot prove a rule is curve-fit. You can run five checks, and reject it when it fails them.

### Why it matters

Curve-fit rules look **better** than honest ones in a backtest. That is what makes them dangerous. The better a backtest looks, the harder you should check it.

### The rule: five checks

1. **Mechanism.** Is there a reason, written before testing, why this should work?
2. **Variants tried.** How many versions did you try before this one? Count all of them, including the ones you dropped.
3. **Sample.** Enough trades? Does the noise band exclude zero?
4. **Decay.** How much did expectancy fall from in-sample to out-of-sample?
5. **Stability.** Do neighbouring settings give similar results? Did walk-forward pick similar settings each time?

Fail two or more: reject. Fail "mechanism" plus any other: reject. These thresholds are a judgement, not a law. Write your own in advance and stick to them.

### Worked example

"Monday only" filter on H-001 (data from `04` Stage 2d):

| Check | Finding | Pass? |
|---|---|---|
| Mechanism | None written. Monday was picked because it scored best. | Fail |
| Variants tried | 5 weekdays; plus 16 settings before that | Fail |
| Sample | 38 in-sample, 20 out-of-sample trades; both bands include zero | Fail |
| Decay | Expectancy +$18.59 → +$5.34, a 71% fall | Fail |
| Stability | Tuesday and Wednesday negative, Thursday and Friday small | Fail |

**Reject.** It made a little money out-of-sample. That does not rescue it.

### Clicks and cells

**Cell 14: the weekday experiment (a deliberate curve-fit, for practice).** This adds a weekday filter on a copy of the function, so your main `backtest` stays unchanged.

```python
def backtest_weekday(prices, s, weekday):
    # weekday: 0 = Monday ... 4 = Friday. Entries on other days are skipped.
    p = prices.copy()
    p["wd"] = pd.to_datetime(p.Date).dt.dayofweek
    O = p["Open"].tolist(); C = p["Close"].tolist(); D = p["Date"].tolist(); W = p["wd"].tolist()
    streak = 0; setup_y = 0; pos_y = 0; exit_y = 0
    entry = 0.0; shares = 0; held = 0; trades = []
    for t in range(1, len(p)):
        streak = streak + 1 if C[t] < C[t-1] else 0
        setup = 1 if streak >= s["n_down"] else 0
        buy = (pos_y == 0 and setup_y == 1 and W[t] == weekday)
        sell = (pos_y == 1 and exit_y == 1)
        if sell:
            fill = O[t] * (1 - s["slip"])
            pnl = (fill - entry) * shares - 2 * max(s["comm_min"], s["comm_per_share"] * shares)
            trades.append({"exit_date": D[t], "pnl": pnl})
        pos = 1 if buy else (0 if sell else pos_y)
        if buy:
            entry = O[t] * (1 + s["slip"]); held = 1; shares = math.floor(s["notional"] / O[t])
        elif pos == 1:
            held += 1
        ex = 0
        if pos == 1 and (C[t] > C[t-1] or C[t] <= entry * (1 - s["stop"]) or held >= s["max_hold"]):
            ex = 1
        setup_y, pos_y, exit_y = setup, pos, ex
    return pd.DataFrame(trades)

for wd, name in enumerate(["Mon", "Tue", "Wed", "Thu", "Fri"]):
    a = backtest_weekday(ins, SETTINGS, wd); b = backtest_weekday(oos, SETTINGS, wd)
    print(f"{name}: IS {len(a)} trades {a.pnl.sum():8.2f} | OOS {len(b)} trades {b.pnl.sum():8.2f}")
```

The IS and OOS columns should match `04` Stage 2d.

### Exercise

A friend reads your walk-forward table (`04` Stage 2e) and says: "4/3 was picked in 2019, 2020 and 2021, and those test years made +$450 − $5 + $270 = +$715. So trade 4/3." Run the five checks. You may run 4/3 on the full practice file: 82 trades, net +$122.64, expectancy +$1.50, standard deviation $95.02.

<details><summary>Answer</summary>

| Check | Finding | Pass? |
|---|---|---|
| Mechanism | None beyond H-001's. Nothing says 4/3 is special. | Fail |
| Variants tried | 4/3 was chosen **by looking at test-window results**. That turns test data into training data. | Fail |
| Sample | 25 trades in the three years quoted (5 + 10 + 10). Full sample: +$1.50 ± 2 × 95.02 ÷ √82 = ± **$20.99**. Includes zero. | Fail |
| Decay | Not testable. There is no unseen data left for 4/3 in this file. | — |
| Stability | 4/3 was picked 3 times in 7 windows. Neighbours 4/5 and 3/3 vary in sign across periods (`04` Stage 2b and the OOS grid). | Fail |

**Reject.** The friend's error is selecting on test results. Once you choose by looking at the test windows, those windows are in-sample.
</details>

> **You are fooling yourself if…**
> - you can't say how many variants you tried;
> - the reason for the rule was written after the result;
> - "it was still positive out-of-sample" is your main argument;
> - you pick a setting by reading walk-forward test results.

---

## Level 2 check

1. In one paragraph, explain to someone who has not read this plan why H-001's in-sample tuning was a curve-fit. Use at least three numbers from `04` Stage 2.
2. Re-run Cells 9, 10, 11 and 13 on your real-fund file, 2014–2023 only. Log the results. Write which of the five curve-fit checks each one passes or fails.

When both are done, read `04` Stage 2, then move to `03-expert.md`.
