# 08 — Glossary

Every term used in these notes, defined once. Where two words mean the same thing, both are listed and one points to the other. The module where the term is first used or taught is in brackets.

---

**Adjusted price.** A price series changed so that dividends and share splits do not appear as fake price jumps. Most free sources multiply all earlier prices by a factor at each dividend or split. Different sources use slightly different methods. (B1)

**Ask** (also **offer**). The lowest price at which someone is currently willing to sell. (B1)

**Average loss.** Total net loss of losing trades ÷ number of losing trades. (B3, `05`)

**Average win.** Total net profit of winning trades ÷ number of winning trades. (B3, `05`)

**Backtest.** Applying a written rule to past prices, one day at a time, as if living through each day, to see what trades it would have made and what they would have earned. A description of the past under stated assumptions, not a forecast. (`00`, B6)

**Bias** (in a backtest). A systematic way a backtest looks better than reality. See `06`. (`06`)

**Bid.** The highest price someone is currently willing to pay. (B1)

**Breakeven slippage** (also **breakeven cost**). The slippage per side at which a rule's net profit is zero. Found by trying values. (I4, E4)

**Buy and hold.** Buying an instrument once and keeping it for the whole test. Used as a comparison. (E6)

**CAGR** (compound annual growth rate). The steady yearly growth rate that would turn the starting equity into the ending equity: (end ÷ start) ^ (1 ÷ years) − 1. (B7, `05`)

**Calendar days.** All days, including weekends and holidays. Used for years in CAGR and for time under water. (`05`)

**Cell** (notebook). One block of Python code in a notebook, run with Shift+Enter. (I1)

**Cell** (spreadsheet). One box in a spreadsheet, named by column letter and row number, such as F3. (B2)

**Client Portal.** IBKR's website for managing your account. (`00`, B1)

**Close.** The last official price of a trading day. (B1)

**Commission.** The fee a broker charges per order. On IBKR's **Fixed** plan for US stocks and ETFs, at the time of writing: $0.005 per share, minimum $1.00, maximum 1% of trade value. The **Tiered** plan charges per share on a sliding scale plus exchange and other fees. The **Lite** plan charges no commission on US stocks and ETFs but is not offered in all countries. Check IBKR's current pages. (B1, I4)

**Correlated.** Moving together. Daily returns are correlated if a good day tends to be followed by another good day, or a bad day by a bad day. (E6)

**Cost stress.** Re-running a fixed rule at higher costs (for example 2× and 3× base slippage) to see how quickly the result breaks. (I4, E4)

**CSV file** (comma-separated values). A plain text file of a table, one row per line, columns separated by commas. Opens in any spreadsheet. (`00`, B5)

**Curve-fit** (also **overfit**). A rule whose good past results come from being shaped to noise in that particular data, not from a real effect. See **overfitting**. (`02`, I8)

**Data checks.** Four checks run before any backtest: row count, date order, blanks and zeros, largest one-day move. (B5, I2)

**Data ledger.** A list of each instrument and date range, with which hypotheses have already touched it. (E1)

**Decision memo.** A one-page written decision with evidence, limits of the evidence, and what would change the decision. (E8)

**Dividend.** Cash a company or fund pays to its shareholders. (B1)

**Drawdown.** A fall in equity from a previous high. **Max drawdown** is the worst such fall in the test, as a percentage of the high or in dollars. (B7, `05`)

**DU.** The usual start of an IBKR paper-account ID. Check it before every paper order. (E7)

**Entry.** The trade that opens a position. **Entry fill** is the price actually paid, including slippage. (B4, B2)

**Equity.** Account value: starting equity plus closed-trade profits, plus any open position valued at the latest close. An **equity curve** is equity drawn over time. (B6, I1)

**ETF** (exchange-traded fund). A fund whose shares trade on a stock exchange like a stock. Many track an index. (`00`)

**ETF-X.** The synthetic practice ETF used in these notes. Not real market data. (`00`)

**Ex-dividend date.** The first day a new buyer of a share no longer gets the upcoming dividend. The price usually drops by about the dividend that morning. (B5)

**Exit.** The trade that closes a position. **Exit fill** is the price actually received, after slippage. **Exit signal** is the condition, checked at a close, that triggers an exit at the next open. (B4, B6)

**Expectancy.** Average net profit per trade: net profit ÷ trade count. Equivalently, win rate × average win − loss rate × average loss. (B3, `05`)

**Exposure.** Share of trading days on which a position was held at the close. (B7, `05`)

**Falsifiable.** Able to be shown wrong by data. A hypothesis must be falsifiable. **Unfalsifiable** means no possible result could show it wrong. (B4)

**Fill.** The trade that actually happened: price, quantity and time. (B1)

**Fill fantasy.** Assuming fills you could not have got, such as at the midpoint, or at a limit price the market only touched. (`06`)

**Fixed-dollar sizing.** Using the same dollar amount, or the same dollar risk, for every trade, regardless of how the account has grown or shrunk. (`05`)

**Gap.** When a price opens well above or below the previous close, skipping the prices in between. A gap can carry the price past a stop. (B2)

**GOOGLEFINANCE.** A Google Sheets function that fetches prices. Its daily history appears not to be dividend-adjusted; check before relying on it. (B5)

**Grid.** A table of results for every combination of a set of parameter values. (I3, E5)

**Gross.** Before costs. (B1)

**Held-out data** (also **holdout**). Data kept sealed, not looked at in any form, for one final pre-registered test. Once opened it is **spent**. (E3)

**High.** The highest price traded during a day. (B1)

**Hypothesis.** A falsifiable claim about the market, stated with what result would show it false. (B4)

**Hypothesis log.** A permanent written record of every hypothesis, every rule version, every test run and its result. Lines are never deleted. (B4, E1)

**IBKR.** Interactive Brokers, the broker you already use. (`00`)

**Independent** (trades or returns). The result of one does not affect or predict another. Several approximations in these notes assume it. (I6)

**In-sample.** The part of the data you may look at and tune on. (I3)

**JupyterLab.** A free program that runs notebooks in your web browser. (I1)

**Kernel.** The part of JupyterLab that runs your Python code. "Restart Kernel" clears everything and starts fresh. (I1)

**Leverage.** Trading with borrowed money, so positions are larger than your cash. Not used in this plan. (`01` rule card)

**Library** (also **package**). An add-on that gives Python extra tools. pandas and yfinance are libraries. (I1)

**Limit order.** An order to buy at or below, or sell at or above, a stated price. It may not fill. (`00`, `06`)

**Liquid.** Trades in large volume with a small spread. You can usually buy or sell a normal-size order quickly near the quoted price. (`00`)

**Long.** Owning shares, so you gain if the price rises. **Long-only** means the rule only buys and later sells; it never sells short. (B1, `00`)

**Look-ahead.** Using information in a decision that would not have been known at the time. (B4, `06`)

**Loss rate.** 1 − win rate. (B3)

**Low.** The lowest price traded during a day. (B1)

**Market order.** An order to buy or sell at the best available price now. (`00`, E7)

**Market-on-open** (MOO). A market order that executes in the opening auction, at the official opening price. On IBKR: order type MKT with Time in Force OPG. (E7)

**max_hold.** H-001's parameter for the most days a trade is held before a time exit. (`04`)

**Mean reversion.** The belief that a price that moved sharply one way tends to move partly back. (`04`)

**Mechanism.** A reason, written before testing, why an effect might exist. (I8, E1)

**n_down.** H-001's parameter for how many lower closes in a row make a setup. (`04`)

**Name box.** The small box left of the formula bar in Google Sheets and Excel, where you can type a cell range such as F4:U2608 to select it. (B5)

**Neighbour check** (also **parameter neighbour check**). Running the settings next to your chosen ones to see whether results form a plateau or a spike. (E5)

**Net.** After costs. (B1)

**Noise band.** Expectancy ± 2 standard errors: a rough range for the true average profit per trade. Approximate; it assumes independent trades that are not extremely lopsided. (I6, `05`)

**Notebook.** A document in JupyterLab made of cells of code and their results. (I1)

**Observation.** Something you noticed in the market, before it is written as a hypothesis. (B4)

**Open.** The first official price of a trading day. On US exchanges it is usually set in the **opening auction**, a process that matches orders submitted before the open at a single price. (B1, E7)

**OPG.** IBKR's Time in Force setting for orders that execute only at the open. (E7)

**Order.** An instruction to a broker to buy or sell. (B1)

**Out-of-sample.** Data kept aside and run once on a final rule to check whether in-sample results survive. Not the same as a holdout if you have already seen it. (I3)

**Overfitting.** Shaping a rule to past noise by trying many variants and keeping the best. Produces a curve-fit. (I8, `06`)

**P&L** (profit and loss). Dollars made or lost. (B1)

**Paper account.** IBKR's simulated account, at live prices, with no real money. (E7)

**Paper plan.** A written plan for a paper-trading test, including duration, routine and a stop rule. (E7)

**Paper trading.** Placing simulated orders in a paper account. Fills are simulated and can be better than real ones. (`00`, E7)

**pandas.** A free Python library for tables of data. (I1)

**Parameter.** A number in a rule that could be set differently, such as n_down = 3. (E5)

**Planned risk.** Shares × (entry fill − stop price) + commissions. Not a maximum loss, because of gaps. (B2)

**Plateau.** A region of a parameter grid where nearby settings give similar results. Necessary, not sufficient. (`04`, E5)

**Position.** What you currently hold: how many shares, bought at what price. (B1)

**Position sizing.** Deciding how many shares to buy. **Sizing from a stop**: shares = round down(dollars at risk ÷ (entry fill − stop price)). (I5)

**Pre-registration.** Writing down the exact rule, data, costs and numeric pass/fail criteria before a test, dated, then running it once. (E2)

**Price bar** (also **bar**). One period's open, high, low and close for one instrument. Here, one day. (B1)

**Profit factor.** Sum of winning trades ÷ |sum of losing trades|. Above 1 means net profit. (B7, `05`)

**Python.** A free programming language. (I1)

**Regime.** A period in which the market's behaviour, as it matters to your rule, is fairly stable. **Regime change** is a shift from one regime to another. (`04`, `06`)

**Research programme.** A set of hypotheses tested in order, with a written record of every test. (E1)

**Research stop rule.** A line on a rule card stating, before testing, what result makes you stop testing that idea. (B4)

**Risk-free rate.** The return on cash, such as short-term government bills. Subtracted in the formal Sharpe ratio; left out in this plan's simple version. (E6)

**Risk per share.** Entry fill − stop price. (B2)

**Round trip.** A buy and the matching sell. One trade. (B1, `05`)

**Rule.** A hypothesis written as instructions precise enough that two people would make the same trades. (B4)

**Rule card.** The written rule, with nine parts: instrument, data, setup, entry, exit, size, costs, not traded, research stop rule. (B4)

**Rule of thumb.** A practical guide, not a law. Sample-size cut-offs in these notes are rules of thumb. (`05`)

**S&P 500.** An index of about 500 large US companies. SPY tracks it. (`00`)

**Setup.** The condition, checked at a close, that makes the rule want to enter at the next open. (B4)

**Sharpe ratio.** Return per unit of variability, proposed by William F. Sharpe. Here approximated as average daily return ÷ standard deviation of daily returns × √252, without subtracting the risk-free rate. Used from Level 3. (E6, `05`)

**Short selling.** Selling shares you do not own, hoping to buy them back cheaper. Not used in this plan. (B4)

**Signal.** A rule's output at a close: setup or exit signal. (B1)

**Slippage.** The difference between the price assumed in a test and the price actually received, including the spread and any move before the fill. Expressed here as a percentage of price on each side. (B1, I4)

**Spent** (data). Held-out data that has been opened once. It can no longer test that idea or related ones. (E3)

**Spike.** A setting whose result is much better than all its neighbours. Usually a sign of fitting noise. (E5)

**Split** (share split). A company or fund changing its number of shares, for example 2-for-1, which halves the price without changing value. Adjusted prices remove its effect. (B1)

**Spread.** Ask minus bid. (B1)

**SPY.** An ETF that tracks the S&P 500. Used in these notes as the example real fund. (`00`)

**Standard deviation.** A measure of how spread out a set of numbers is around their average. Spreadsheets: `STDEV`. Python: `.std()`. (I6)

**Standard error.** Standard deviation ÷ √(count). A measure of how uncertain an average is. (`05`)

**Stop.** A price set in advance at which you accept you were wrong and sell. In this plan it is checked at the close and executed at the next open. (B2)

**Streak.** The number of down closes in a row up to today. (B6)

**Survivorship** (survivorship bias). Testing only on instruments that still exist or did well, because the ones that failed are missing from today's lists. (`06`)

**Synthetic data.** Data generated by a computer, not recorded from a real market. ETF-X is synthetic. (`00`)

**Terminal.** A window where you type commands to the computer: Command Prompt on Windows, Terminal on Mac. (I1)

**Tests run.** The section of a hypothesis-log entry with one line per test, used to count variants tried. (E1)

**Threshold.** A number fixed in advance that a result must beat. (E2)

**Time in Force** (TIF). How long an order stays active. OPG means "at the open only". (E7)

**Time under water.** How long equity stays below a previous high. (E6, `05`)

**Trade count.** Number of completed round trips. (B7, `05`)

**Trading day.** A day the exchange is open. About 252 a year in the US. (B5)

**TWS** (Trader Workstation). IBKR's desktop trading program. (`00`, B1)

**Variant.** A changed version of a rule: different parameters, a filter, a different exit. Every variant tried counts toward overfitting. (I8, E1)

**Volatility.** How much a price moves around, usually measured by the standard deviation of daily returns. (I4, E4)

**Walk-forward analysis.** Repeatedly tuning on a training window and testing on the next window, sliding forward through time, and counting only test windows. Robert Pardo's book is the standard reference. (I7)

**Win rate.** Share of trades with net profit above zero. (B3, `05`)

**yfinance.** A free, unofficial Python library that downloads prices from Yahoo Finance's public pages. It can break when Yahoo changes. (I2)
