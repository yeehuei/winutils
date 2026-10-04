# 03 — Expert: a small research programme, and a defended "no"

**Goal of this level.** By the end you can defend a "do not trade" result. You will run one idea through a complete, pre-committed process, deploy only to IBKR's paper account, and write a decision memo.

**Time.** Weeks 9–12, about 6 hours a week. A real paper-trading record takes longer than four weeks. This level teaches the process and has you rehearse the mechanics. Running a full paper test of a rule that passes is your next project, not part of these 12 weeks.

**Assumed from Levels 1–2.** Rule cards, spreadsheet and notebook backtests that agree, in-sample and out-of-sample, cost stress, sizing from a stop, the noise band, walk-forward, and the five curve-fit checks.

**New in this level.** You may now open `data/etfx-holdout-2024-2025.csv`, **once**, in Module E3, after you have written the pre-registration in Module E2.

Modules:

- E1 — The hypothesis log
- E2 — Pre-registration
- E3 — Held-out data
- E4 — Cost stress and breakeven cost
- E5 — The parameter neighbour check
- E6 — Sharpe ratio and time under water
- E7 — Paper deployment on IBKR, with a written stop rule
- E8 — The decision memo

---

## E1 — The hypothesis log

### The idea

A **research programme** is a set of hypotheses you test in order, with a written record of every test. The **hypothesis log** is that record. In Level 1 it was a list. Now it becomes the backbone of your research.

### Why it matters

Two things fool researchers more than anything else:

- **Forgetting how many things you tried.** If you test 20 ideas, one will look good by luck. Unless you log all 20, you will believe the one.
- **Forgetting which data you have already seen.** Data you have looked at cannot test an idea fairly. The log tracks which data each idea has touched.

### The rule

Every entry has these fields. Every run of every variant adds a line under **Tests run**, including runs you think "don't count".

```
ID              H-00n
Opened          date
Observation     what you noticed, in one line
Hypothesis      falsifiable statement, with "it is false if ..."
Mechanism       why it might work. "None" is allowed but must be written.
Rule card       version and date of each version
Data touched    instrument, date range, and how: looked / tuned / tested once
Tests run       one line per run: date, version, data, settings, trades, net, note
Status          open / failed / closed / passed to paper
Next step       one line
```

Keep a separate **data ledger**: one line per instrument and date range, saying which hypotheses have touched it. A range touched by any hypothesis is no longer clean for a new test of a **related** idea.

### Worked example

The H-001 entry, after Levels 1 and 2, is in `04` Stage 3a. Its "Tests run" section would have at least these lines:

```
1  spreadsheet  v1 3/5  ETF-X 2014-2023  net   160 trades  -456.25
2  spreadsheet  v1 3/5  ETF-X 2014-2023  gross 160 trades +1451.21
3  spreadsheet  v1 3/5  ETF-X 2014-2023  slip x2       160  -2043.72
4  notebook     v1 3/5  ETF-X 2014-2023  match check   160  -456.25
5  notebook     v1 3/5  IS 2014-2020                   110  +788.40
6  notebook     v1 3/5  OOS 2021-2023                   50 -1244.65
7-22 notebook   grid 16 settings, IS                    (table in 04 2b)
23 notebook     v1 2/5  OOS 2021-2023                   87  -964.01
24-28 notebook  weekday filter x5, IS and OOS           (table in 04 2d)
29 notebook     walk-forward 3y/1y, 7 windows x 16      124 -1873
30 notebook     walk-forward 2y/1y, 8 windows x 16      171 -2515
31-34 notebook  cost stress x0..x3                      160 (table in 04 2f)
35 notebook     risk sizing $100                        160  -349.50
```

That is over 30 lines, and the walk-forward lines hide 240 inner runs. When you next see a variant that "works", remember this count.

Data ledger line: `ETF-X 2014-01-02 to 2023-12-29: touched by H-001 (looked, tuned, tested). Not clean for mean-reversion ideas on ETF-X.`

### Clicks and cells

Use whatever you will keep using: a text file, a notebook page, a spreadsheet tab. One rule: **never delete a line**. To correct a line, add a new line that says what was wrong.

If you use a spreadsheet, make a `Log` tab with one column per field above, plus a `Tests` tab with columns: ID, run number, date, version, data, settings, trades, net, note.

### Exercise

You have a new idea: "ETF-X tends to rise in the first five trading days of each month." Write its hypothesis log entry, as far as **Status**. Data touched should be honest. Then say which data, if any, is still clean for testing it.

<details><summary>Answer</summary>

```
ID              H-003
Opened          (today)
Observation     ETF-X seems to rise early in each month.
Hypothesis      Buying at the open of the 1st trading day of each month and
                selling at the close of the 5th makes more than costs on
                average. False if net expectancy <= $0, or fewer than 20 trades.
Mechanism       Possibly monthly cash inflows into index funds. Weak; ETF-X is
                synthetic, so any real-world mechanism does not apply to it.
Rule card       v1 (today)
Data touched    ETF-X 2014-2023: LOOKED (all of it, during H-001), not tuned
                for this idea. ETF-X 2024-2025: touched once by H-001.
Tests run       none yet
Status          open
```

**Which data is clean?** Strictly, none on ETF-X. You have looked at the 2014–2023 chart many times. The 2024–2025 holdout was opened for H-001, so you have seen that period too. A clean test of H-003 needs data you have not looked at: for a real fund, a period you have not charted. The honest answer is "no clean data on ETF-X", and the log should say so.

The mechanism line matters too. For a synthetic series, no real-world story can be the reason. Writing that down stops you from inventing one later.
</details>

> **You are fooling yourself if…**
> - your log only contains the runs you liked;
> - you cannot say how many settings you tried before the current one;
> - you have no record of which date ranges you have already looked at;
> - you "cleaned up" the log by deleting failed variants.

---

## E2 — Pre-registration

### The idea

**Pre-registration** means writing down, before a test, exactly what you will run and what result counts as a pass. Date it. Then run the test once.

### Why it matters

After a result, it is easy to decide that the threshold "should have been" a bit lower, or that one bad year "doesn't count". Pre-registration makes those moves visible. They become a new version with a new date, not a quiet edit.

### The rule

A pre-registration states:

1. **Rule.** Card ID and version, all settings fixed.
2. **Data.** The exact file or date range, and that it has not been opened.
3. **Costs.** Base costs, and the stressed costs it must also survive.
4. **Pass criteria.** All must hold. Use numbers.
5. **Fail action.** What you will **not** do: usually no variants on this data.
6. **Pass action.** The next step. Never "trade live". At most "paper-trade under plan P-00n".

Save it so the date cannot be changed quietly. Email it to yourself, or save it to a cloud folder that keeps file history. This protects you from yourself. It is not proof for anyone else.

**Choosing pass criteria.** They are a judgement made in advance. Some guides:

- **Net profit > 0 at base and at double slippage.** Survives cost uncertainty.
- **Profit factor ≥ 1.1.** A margin above breakeven. The exact number is a convention.
- **A minimum trade count, set from the data length and your Level 2 trade rate.** H-001 traded about 16 times a year, so two years gives about 30. A floor of 20 allows for a quiet period.
- **Max drawdown no worse than a level you would actually sit through.**
- **Optional, strict:** expectancy minus the noise band > 0. With 20–40 trades this almost never passes, even for a real edge. Use it when you have enough trades.

A small held-out sample can easily **reject** a rule, but can only weakly **confirm** one. A pass sends you to paper trading, not to live money.

### Worked example

The H-001 pre-registration is in `04` Stage 3b. Read it now, before E3.

### Clicks and cells

Write your pre-registration for the H-001 v1 sealed test. Copy `04` Stage 3b, put in today's date, and email it to yourself. Do not open the holdout file until the email has been sent.

### Exercise

Find four problems in this pre-registration:

```
Rule      H-004, the version that worked best in-sample
Data      2022-2025, mostly unseen
Pass if   it looks reasonably profitable after costs
If fail   try a couple of tweaks and re-run
```

<details><summary>Answer</summary>

1. **Rule not fixed.** "The version that worked best" must be a version number with every setting written.
2. **Data not clean.** "Mostly unseen" means partly seen. State an exact range that has never been opened, or admit there is none.
3. **Pass criteria not numeric.** "Reasonably profitable" can be argued either way after the fact. Use numbers: net > 0 at 1× and 2× costs, profit factor ≥ 1.1, at least N trades, max drawdown no worse than X%.
4. **Fail action re-uses the test data.** "Tweaks and re-run" on the same data turns the test into tuning. The fail action should be "close H-004 on this data".

Also missing: costs, and the pass action.
</details>

> **You are fooling yourself if…**
> - you write the pre-registration after opening the data "just to check the format";
> - your pass criteria contain words like "reasonable" or "decent";
> - you adjust the thresholds once you see how close you came;
> - your pass action is "go live".

---

## E3 — Held-out data

### The idea

**Held-out data** (also called a **holdout**) is data kept sealed for one final, pre-registered test. It differs from Level 2's out-of-sample: nobody, including you, has looked at it in any form for this idea or a related one. Not as a chart, a table, or a summary.

### Why it matters

In Level 2 your out-of-sample period was already contaminated: you had seen it in Level 1. A holdout is the one test your earlier choices cannot have leaked into.

### The rule

1. Decide the holdout before any research on the idea. For real data, the most recent period is the natural choice.
2. Do not chart it, summarise it, or read news about how the instrument did in it, if you can avoid that.
3. Pre-register (E2), then open it once and run exactly the pre-registered test.
4. Afterwards, mark it **spent** in the data ledger for this idea and related ones.

**What counts as "opening" it:** loading it into any tool, looking at its chart, looking at its total return, or running any rule on it. If you did any of these, it is not a holdout. Say so in the log.

### Worked example

H-001 v1 on `etfx-holdout-2024-2025.csv`. Full table in `04` Stage 3c. Data checks first:

```
rows 523 | first 2024-01-01 | last 2025-12-31
missing values 0 | zero or negative prices 0
duplicate dates 0 | dates out of order 0
largest one-day close change 0.0243 on 2025-09-02
```

Result: **29 trades, net −$830.08**, expectancy −$28.62 ± $42.17, profit factor 0.51, max drawdown −12.53%, gross −$484.75. It fails criteria 1, 2 and 3 of the pre-registration. **H-001 is closed.**

### Clicks and cells

Only after your pre-registration email is sent. Copy the holdout file into your `backtest` folder. Your notebook needs Cells 1, 2, 4, 5 and 8 from Level 2 run first.

**Cell 15: the sealed test, run once.**

```python
hold = pd.read_csv("etfx-holdout-2024-2025.csv")[["Date", "Open", "High", "Low", "Close"]]
check(hold)
t, c = backtest(hold, SETTINGS)          # SETTINGS must be exactly H-001 v1: 3, 5, 0.05, ...
print(report(t, c, SETTINGS["start_equity"]))
```

Before running, print `SETTINGS` and compare it line by line with the pre-registration.

### Exercise

*Illustration, not ETF-X.* A different pre-registered rule, on its holdout, gives: 25 trades, net +$300 at base costs, +$90 at double slippage, profit factor 1.15, max drawdown −8% against a −12% limit. Standard deviation of trade profits $110. The pre-registration is the same as H-001's (`04` Stage 3b).

1. Does it pass?
2. What does the pass show, and what does it not show?
3. What is the next step?

<details><summary>Answer</summary>

1. **Yes.** Net > 0 at base (+$300) and at double slippage (+$90). Profit factor 1.15 ≥ 1.10. 25 trades ≥ 20. Drawdown −8% is within the limit. The **−12%** is from this rule's own pre-registration; H-001 used −15%.
2. Expectancy = 300 ÷ 25 = **$12**. Noise band = 2 × 110 ÷ √25 = 2 × 110 ÷ 5 = **± $44**. So −$32 to +$56. The pass shows the rule **did not fail** on unseen data. It does **not** show the rule has an edge. The band includes zero.
3. Paper-trade under a written plan (E7). Not live. Record whether paper fills match the backtest's cost assumptions.
</details>

> **You are fooling yourself if…**
> - you "just looked at the chart" of the holdout period before testing;
> - you ran the holdout, it failed, and you ran a variant on it;
> - your holdout is the same period as an earlier out-of-sample test you have already seen;
> - you read a holdout pass as proof.

---

## E4 — Cost stress and breakeven cost

### The idea

**Cost stress** re-runs a fixed rule at higher costs. **Breakeven cost** is the cost level at which net profit is zero. Together they tell you how much room you have between your assumptions and failure.

### Why it matters

Your slippage assumption is a guess until you have fills. If a rule only works when every fill is better than you can reliably get, it does not work.

### The rule

1. Report net profit at 0×, 1×, 2× and 3× base slippage, with commissions on in all but 0×.
2. Find the breakeven slippage by trying values. Report it as "% per side".
3. Compare breakeven with your **measured** slippage from paper or real fills on the days the rule would trade (Module I4).
4. Require breakeven to be at least **twice** your measured slippage before paper trading. This margin is a judgement; write your own in the pre-registration.

### Worked example

H-001 v1 on ETF-X:

| | Gross (0×) | Base (1×) | 2× | 3× | Breakeven slippage per side |
|---|---|---|---|---|---|
| In-sample 2014–2020 | +$2,100.23 | +$788.40 | −$303.44 | −$1,395.27 | ≈ 0.087% |
| Full 2014–2023 | +$1,451.21 | −$456.25 | −$2,043.72 | −$3,631.18 | ≈ 0.035% |
| Holdout 2024–2025 | −$484.75 | −$830.08 | −$1,117.41 | −$1,404.74 | none: loses at zero cost |

Read down the last column. Each later period has less room than the one before, and the holdout has none. Note also that even the in-sample "good" period lost money at double slippage. A shrinking breakeven over time is a warning of **regime change**: the market's behaviour, as it touches your rule, has changed. See `06-bias-checklist.md`.

The in-sample and full-sample breakevens were found by trying slippage values 0.002 percentage points apart.

### Clicks and cells

**Cell 16: breakeven search.** It tries slippage values in small steps until net profit turns negative.

```python
def breakeven_slip(prices, s, step=0.00002, limit=0.005):
    slip = 0.0
    while slip <= limit:
        t, c = backtest(prices, dict(s, slip=slip))
        if t.pnl.sum() < 0:
            return round(slip - step, 6), round(slip, 6)
        slip += step
    return None

print(breakeven_slip(prices, SETTINGS))   # full sample
print(breakeven_slip(ins, SETTINGS))      # in-sample
```

For the full sample it prints `(0.00034, 0.00036)`: breakeven between 0.034% and 0.036%. In-sample it prints `(0.00086, 0.00088)`. If it returns `None`, the rule stays positive up to 0.5%. If net is already negative at zero slippage, it returns `(-2e-05, 0.0)`, which means "no breakeven". That is what the holdout gives.

### Exercise

Your real-fund rule's breakeven slippage is 0.06% per side. You paper-traded 30 entries on the days the rule fired. Your measured average slippage is 0.025% per side. Does it meet the "twice" margin? What else would you check?

<details><summary>Answer</summary>

Twice 0.025% is 0.05%. Breakeven 0.06% ≥ 0.05%, so **yes**, narrowly.

Also check:

- the **spread** of your fills, not just the average. If a quarter of fills are worse than 0.06%, those trades lose;
- whether fills worsen on high-volatility days, which may be the days the rule trades most;
- that your commission plan in the backtest is the one you actually pay.
</details>

> **You are fooling yourself if…**
> - you only report the base-cost line;
> - your measured slippage comes from different days than the rule's trade days;
> - your breakeven is close to your measured cost and you call that "positive";
> - you lowered the cost assumption after the first stress run.

---

## E5 — The parameter neighbour check

### The idea

A **parameter** is a number in your rule that you could set differently, such as n_down = 3. The **neighbour check** runs the settings next to yours. If they give similar results, you are on a **plateau**. If yours is good and its neighbours are bad, you are on a **spike**.

### Why it matters

A spike usually means the setting fitted noise. Real effects rarely depend on n_down being exactly 3 and not 2 or 4.

### The rule

1. Run a grid that includes at least one step either side of each parameter.
2. Do it on **in-sample** data only. Never on the holdout.
3. Plateau: most neighbours have the same sign and similar size. Spike: your setting is much better than all neighbours.
4. A spike → reject. A plateau → **not rejected by this check**. It is necessary, not sufficient.

### Worked example

H-001 in-sample grid (from `04` Stage 2b), net $:

| n_down / max_hold | 3 | 5 | 7 | 10 |
|---|---|---|---|---|
| 2 | 642 | 1,050 | 1,015 | 1,015 |
| **3** | 736 | **788** | 755 | 755 |
| 4 | 626 | 418 | 418 | 418 |
| 5 | −260 | −220 | −300 | −300 |

Around 3/5, all eight neighbours are positive, from +$418 to +$1,050. **Plateau. Check passed.** Then out-of-sample and holdout failed anyway.

Why a plateau can still fail: neighbours share the same data, so they share the same luck. If the early years had a temporary effect, every neighbour learns it. The neighbour check catches fragile settings. It does not catch a fragile period.

### Clicks and cells

Cell 10 from Level 2 is the grid. To check neighbours of one setting, read the 3 × 3 block around it.

### Exercise

*Illustration.* An in-sample grid, net $:

| n / h | 3 | 5 | 7 |
|---|---|---|---|
| 2 | −120 | −80 | −95 |
| 3 | −60 | **+940** | −40 |
| 4 | −110 | −70 | −130 |

Plateau or spike? What do you do?

<details><summary>Answer</summary>

**Spike.** 3/5 is +$940. All eight neighbours are negative, from −$40 to −$130. Reject. Do not test 3/5 on the holdout. It would waste the holdout on a setting that has already failed a cheaper check.
</details>

> **You are fooling yourself if…**
> - you ran the neighbour check on the holdout;
> - you treat a plateau as proof;
> - your grid is so coarse that "neighbours" are very different rules;
> - you only show the neighbours that agree.

---

## E6 — Sharpe ratio and time under water

### The idea

The **Sharpe ratio**, proposed by William F. Sharpe, compares return with how bumpy that return is. In the simple form used here:

**Sharpe ≈ (average daily return ÷ standard deviation of daily returns) × √252**

Here 252 is roughly the number of US trading days in a year, and √252 ≈ 15.87. "Daily return" is the day's percentage change in equity.

The formal definition subtracts a **risk-free rate**, the return on cash such as short-term government bills. This simple form leaves it out, which overstates Sharpe when cash rates are high. It also assumes daily returns are roughly independent. Treat the result as approximate.

**Time under water** is how long equity stays below a previous high. Max drawdown says how deep. Time under water says how long.

### Why it matters

Net profit ignores the ride. Two rules with the same profit can differ greatly in how much they swing. Sharpe compresses that into one number for comparing strategies. Time under water tells you whether you would really have stuck with the rule.

### The rule

1. Compute Sharpe from daily equity, net of costs.
2. Compare it with buying and holding the same fund over the same dates.
3. Do not compare Sharpe across different date ranges or instruments without saying so.
4. Report the longest time under water in calendar days, and whether the last high was ever regained.

### Worked example

ETF-X, 2014-01-02 to 2023-12-29, all from daily equity:

| | Sharpe | End equity | Max drawdown |
|---|---|---|---|
| H-001 v1, gross | 0.36 | $11,451.21 | −11.06% |
| H-001 v1, net | −0.09 | $9,543.75 | −20.11% |
| Buy and hold (98 shares at the 2014-01-03 open, plus slippage) | 0.55 | $19,849.92 | −30.59% |
| H-001 v1, net, on holdout 2024–2025 | −0.80 | $9,169.92 | −12.53% |

Buy and hold ignores its two $1 commissions. That makes no visible difference.

Time under water, H-001 v1 net: equity peaked at $11,686.15 on 2018-08-22. It was still below that high on 2023-12-29, the last day: **1,955 calendar days** later, over five years, and never recovered.

Buy and hold earned more with a higher Sharpe, but had a deeper drawdown. Comparing is not choosing. You can see what H-001 was competing against.

### Clicks and cells

**Cell 17: Sharpe and buy-and-hold.**

```python
def sharpe(curve):
    daily = curve.equity.pct_change().dropna()
    return daily.mean() / daily.std() * math.sqrt(252)

def buy_and_hold(prices, s):
    shares = math.floor(s["notional"] / prices.Open.iloc[1])
    entry = prices.Open.iloc[1] * (1 + s["slip"])
    eq = s["start_equity"] + (prices.Close - entry) * shares
    eq.iloc[0] = s["start_equity"]
    return pd.DataFrame({"Date": pd.to_datetime(prices.Date), "equity": eq, "in_mkt": 1})

t, c = backtest(prices, SETTINGS)
bh = buy_and_hold(prices, SETTINGS)
print("rule Sharpe", round(sharpe(c), 2), "| buy-and-hold Sharpe", round(sharpe(bh), 2))

peak = c.equity.cummax()
last_high = c.Date[c.equity == peak].iloc[-1]
print("last equity high", last_high.date(), "| days since, to end of data", (c.Date.iloc[-1] - last_high).days)
```

**Spreadsheet version.** Add a column V `Ret` with `=S3/S2-1` from row 3 down. Then:
`=AVERAGE(Data!V3:V2608)/STDEV(Data!V3:V2608)*SQRT(252)`.

### Exercise

*Illustration.* A strategy's daily returns average 0.04% with a standard deviation of 1.0%. Compute its approximate Sharpe. Then say one reason the true figure could be lower.

<details><summary>Answer</summary>

0.0004 ÷ 0.01 = 0.04. × √252 = 0.04 × 15.87 = **0.63**.

It could be lower because:

- the risk-free rate was not subtracted;
- daily returns may be correlated, which makes the √252 scaling overstate it;
- the sample may include a lucky window.

Any one of these is a correct answer.
</details>

> **You are fooling yourself if…**
> - you quote a Sharpe from gross equity;
> - you compare your Sharpe with a different period's buy-and-hold;
> - a high Sharpe on 30 trades impresses you;
> - you never asked how long you would have been under water.

---

## E7 — Paper deployment on IBKR, with a written stop rule

### The idea

**Paper trading** places simulated orders in IBKR's paper account, at live prices, with no real money. It tests the things a backtest cannot: whether you follow the rule every day, whether your orders go in correctly, and what fills you get.

### Why it matters

A backtest assumes perfect discipline and the fills you assumed. Paper trading shows whether either holds. It is also where most people discover the rule needs them to be awake at 09:25 New York time.

**What paper trading cannot show.** IBKR's paper account simulates fills from market quotes. It does not model your order's effect on the market or your place in the queue. Its fills can be **better** than real ones. Treat paper slippage as a lower bound on real slippage.

### The rule

Before the first paper order, write a **paper plan** with:

1. **Rule card version.** Unchanged from the one that passed its holdout.
2. **Duration.** A minimum number of trades and a minimum calendar period, whichever ends later.
3. **Daily routine.** When you check the close, when you place orders, and how.
4. **Stop rule for the paper test.** End it early if any of these happens:
   - paper drawdown exceeds 1.5 × the backtest's max drawdown;
   - average measured slippage exceeds the backtest's breakeven slippage;
   - two order errors (wrong ticker, side, size, or order type);
   - you override the rule once, in either direction.
5. **Comparison.** At the end, re-run the backtest on exactly the paper dates, then compare trade by trade with the paper record.
6. **No automation.** Every order is placed by hand. This plan never automates live orders.

The 1.5× drawdown and "two errors" thresholds are judgements. Choose your own **before** starting, and write them down.

### Worked example: a paper plan

*Illustration: a hypothetical rule H-0xx that passed its holdout. H-001 did not pass and is not paper-traded.*

```
PAPER PLAN  P-001 for H-0xx v2                     Written: (date)
Rule card     H-0xx v2, unchanged.
Account       IBKR paper account (ID starts DU...). Checked before every order.
Size          Risk $100 per trade, shares from the stop (Module I5).
Duration      At least 30 trades AND at least 6 months.
Routine       After the US close: update prices, run the notebook to the last
              row, read the signal. If entry or exit: enter a market-on-open
              order (TIF = OPG) for the next session before IBKR's cutoff.
              After the open: record fill price, time, official open.
Fill log      date, side, shares, fill, official open, slippage %, notes.
Stop the paper test early if:
              - paper drawdown worse than 1.5 x backtest max drawdown
              - average slippage over 10+ fills > breakeven slippage
              - 2 order errors
              - 1 override of the rule
At the end    Re-run the backtest over the paper dates. Compare trade list,
              entry/exit dates and fills. Write a decision memo (E8).
Not allowed   Live orders. Automation. Changing the rule mid-test.
```

### Clicks and cells

Menu names below are as of writing. IBKR changes them. If one has moved, use the search or help box in TWS or Client Portal.

**Set up the paper account.**

1. Log in to **Client Portal**. Open the user menu (top right) → **Settings** → **Account Settings**. Find **Paper Trading Account**. There you can see the paper username, and reset the paper account.
2. On the same settings area, look for the option to **share market data with the paper account**. Without it, the paper account may only show delayed prices.

**Log in to the paper account in TWS.** On the TWS login screen, switch from **Live Trading** to **Paper Trading**, and use the paper username. Before **every** order, check that the account ID shown in TWS begins with **DU**, which is how paper accounts are usually labelled. If you are unsure, do not transmit.

**Place a market-on-open order (MOO).** A **market-on-open** order fills in the opening auction at the official open price. That matches a backtest that buys "at the open".

1. In the TWS order entry line, enter the ticker.
2. Action **BUY**, Quantity, Order Type **MKT**.
3. Set **Time in Force** (how long the order stays active) to **OPG**. That makes it an at-the-open order.
4. Click **Transmit** before the exchange's cutoff for opening-auction orders. Cutoffs differ by exchange; check IBKR's order-type notes for your fund's listing exchange.
5. If the paper account rejects or delays OPG orders, place a plain market order in the first minute after the open, and log the official open next to your fill.

**If you are outside the US**, convert the times. The US regular session opens at 09:30 New York time.

**Rehearsal (required).** Do this once, even though H-001 failed. It is mechanics practice, not a strategy test:

1. Paper-buy **1 share** of your real fund with a MOO order.
2. After the open, find the fill (Module I4 "Finding a fill") and the official open.
3. Next day, paper-sell it with a MOO order.
4. Log both fills and compute slippage.

### Exercise

Your rehearsal log reads:

| Date | Side | Shares | Fill | Official open |
|---|---|---|---|---|
| Day 1 | Buy | 1 | 512.43 | 512.30 |
| Day 2 | Sell | 1 | 509.85 | 509.90 |

*Illustration.* Compute slippage on each fill, as a percentage, and say whether each is in your favour.

<details><summary>Answer</summary>

- Buy: (512.43 − 512.30) ÷ 512.30 = 0.13 ÷ 512.30 = **0.025%** against you. You paid more than the open.
- Sell: (509.90 − 509.85) ÷ 509.90 = 0.05 ÷ 509.90 = **0.010%** against you. You received less than the open.
- Average **0.018%** per side. Two fills say almost nothing. A fill in the opening auction should normally be at the official open, so differences like these may also come from how the paper account simulates fills. Log them anyway, and look for the pattern over 30 or more fills.
</details>

> **You are fooling yourself if…**
> - you started paper trading without a written stop rule;
> - you skipped a signal because "the market felt wrong", and kept going;
> - you treat paper fills as proof of real fills;
> - you placed an order without checking the account ID.

---

## E8 — The decision memo

### The idea

A **decision memo** is one page that states the decision, the evidence, and the limits of that evidence. It is written so a sceptical reader can check every claim.

### Why it matters

Writing forces clarity. "Do not trade" is the most common correct decision. You should be able to defend it as firmly as a "trade".

### The rule

The memo contains, in order:

1. **Decision.** One line. Trade on paper / do not trade / close the hypothesis.
2. **Question.** What was tested.
3. **Evidence.** Numbered. Each item gives dates, trade count, costs, net result.
4. **What this does not show.** At least two items.
5. **What would change the decision.** Specific and testable, never "if it starts working".
6. **Cost of being wrong.** Both directions: missing a real edge, and trading a false one.
7. **Signed and dated.**

### Worked example

The H-001 memo is in `04` Stage 3e. Read it, then read Stage 3f, which handles the three common objections.

### Clicks and cells

Copy the memo template from `04` Stage 3e into your log as a new entry type. Keep memos next to the hypothesis entries they close.

### Exercise

Write the decision memo for your **real-fund** run of H-001 (Levels 1–3 on SPY or your chosen fund). Then check it against this list.

<details><summary>Answer (checklist)</summary>

There is no single right memo. Yours should pass all of these:

- [ ] The decision is one line and uses "trade on paper", "do not trade" or "close".
- [ ] Every evidence item has dates, trade count and costs.
- [ ] The holdout result is reported exactly as run, once. No variant was run on it.
- [ ] "What this does not show" has at least two items, and one of them is about the sample size or noise band.
- [ ] "What would change the decision" names new data, not a tweak.
- [ ] If the decision is "trade on paper", a paper plan with a stop rule is attached.
- [ ] Nothing in the memo says or implies "this will make money".
- [ ] A friend who has read only this memo could tell you why you decided it.
</details>

> **You are fooling yourself if…**
> - your memo's "evidence" is one equity chart;
> - "what this does not show" is empty;
> - "what would change the decision" is "if the market changes";
> - you wrote the memo before running the holdout.

---

## Level 3 check: defend "do not trade"

Ask a friend to argue for trading H-001, using any of these:

1. "Monday-only made money out-of-sample."
2. "Your slippage assumption is too high for a liquid ETF."
3. "Drop 2022 — it was an unusual year."
4. "The in-sample grid was a nice plateau, so the rule is robust."
5. "29 trades in the holdout is too few to judge."

Answer each in two or three sentences, with numbers from `04`.

<details><summary>Model answers</summary>

1. Monday was chosen from five weekdays with no mechanism. Its 20 out-of-sample trades had expectancy +$5.34 ± $39.02, so the band includes zero. Its expectancy fell 71% from in-sample (`04` 2d).
2. Breakeven slippage on 2014–2023 is about 0.035% per side. On the holdout, the rule lost **gross**: −$484.75 at zero cost. There is no cost assumption under which the sealed test passes (`04` 3c, 3d).
3. The rule card has no clause for unusual years. Removing a period after seeing it is selection on results. Even without it, 2017–2023 excluding 2022 is still −$802.69 (−$1,871.30 + $1,068.61) (`04` 2g).
4. A plateau is necessary, not sufficient. Neighbours share the same in-sample period and its luck. Out-of-sample, every one of the 16 settings lost (`04` 2b, 2c, and the walk-forward in 2e).
5. Agreed that 29 trades cannot prove an edge. But the pre-registration set the minimum at 20, before the test, and the rule failed three other criteria. A small sample can reject a rule more easily than it can confirm one. Also, the holdout is not the only evidence: the full sample, out-of-sample and walk-forward all lost.

For answer 4, the claim that all 16 settings lost out-of-sample comes from the 2021–2023 grid. Run Cell 10 on `oos` instead of `ins` to see it: every cell is negative, from −$255 to −$1,412.
</details>

When you can answer all five without notes, you have finished the programme. See the definition of done in `00`.
