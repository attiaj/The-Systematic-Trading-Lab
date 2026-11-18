# The Systematic Trading Lab

This repository holds the research vault and the notebooks of the course **[The Systematic Trading Lab](https://www.quantreo.com/systematic-trading-lab/)**. The course teaches one method: find an idea, design it as a systematic strategy, diagnose the backtest honestly, document every step, and then decide whether the strategy deserves a place in a portfolio. Each part of that method is in this repository:

- an **Obsidian vault** (`vault/`) that is the research desk: idea journal, reading notes, strategy sheets, research log, portfolio table, templates and a toolbox of reference notes;
- four **Jupyter notebooks** (`notebooks/`) that build, test and judge two real strategies (FOMC Drift, killed; Crypto XS Momentum, alive on paper) and then decide what one of them is worth to a book of strategies;
- two **data scripts** (`data/`) that download the market data from its original public sources, plus small reference files and the portfolio return series used by the last notebook.

The guiding rule, stated in the vault and applied throughout: *write what you expect before you look at the result.*

---

## Contents

1. [Where this repository comes from](#where-this-repository-comes-from)
2. [Repository map](#repository-map)
3. [What the repository can do](#what-the-repository-can-do)
4. [How it works: the research pipeline](#how-it-works-the-research-pipeline)
5. [Workflow for a team member](#workflow-for-a-team-member)
6. [Data](#data)
7. [Things to know](#things-to-know)
8. [Disclaimer](#disclaimer)
9. [Original README (Quantreo)](#original-readme-quantreo)

---

## Where this repository comes from

This is Jacob Attia's copy ([attiaj/the-systematic-trading-lab](https://github.com/attiaj/the-systematic-trading-lab)) of the course repository written by Lucas Inglese / Quantreo ([Quantreo/the-systematic-trading-lab](https://github.com/Quantreo/the-systematic-trading-lab)). The notebooks, vault, scripts and reference data are Quantreo's course material. This copy is used by a small team to work through the course and to run its own research with the same method.

The two remotes are:

| Remote | URL | Role |
| --- | --- | --- |
| `origin` | https://github.com/attiaj/the-systematic-trading-lab | The team's copy. Push here. |
| `upstream` | https://github.com/Quantreo/the-systematic-trading-lab | The course repository. Pull course updates from here. Never push. |

To bring in new course material (new sections, fixes) and publish it to the team's copy:

```
git checkout main
git pull upstream main
git push
```

If the pull reports conflicts, they will usually be in vault notes that both the course and the team edited (see [Git workflow](#git-workflow) for how to avoid that).

---

## Repository map

```
the-systematic-trading-lab/
├── README.md                  this file
├── requirements.txt           pinned Python packages
├── .python-version            3.12
├── .gitignore                 excludes downloaded data, venvs, Obsidian workspace files
├── data/                      download scripts and their sources
│   ├── get_fomc_minute.py     FX minute bid/ask around FOMC meetings (Dukascopy)
│   ├── get_crypto_daily.py    daily Binance USDT perpetuals (Binance Vision)
│   └── SOURCES.md             sources, what each script builds, terms of use
├── data_fomc/                 FOMC Drift data
│   └── event_calendar.csv     FOMC and ECB announcement times (included)
│                              + dukascopy_fomc_minute.parquet (downloaded, not tracked)
├── data_trend/                Crypto XS Momentum data
│   ├── perp_meta.csv          832 USDT perp symbols: type, sector, listing date (included)
│   ├── symbols_prices.csv     the 450 symbols whose prices and funding are downloaded (included)
│   ├── universe_today.csv     40 of today's largest names, the survivorship control (included)
│                              + prices_funding.parquet, volume_all_perps.parquet (downloaded, not tracked)
├── data_portfolio/            inputs of notebook 03, all included
│   ├── README.md
│   ├── portfolio_1_returns.csv
│   ├── portfolio_2_returns.csv
│   ├── xs_momentum_returns.csv
│   └── fomc_drift_trades.csv
├── notebooks/
│   ├── 01_fomc_drift.ipynb               a strategy tested and killed
│   ├── 02_crypto_xs_momentum.ipynb       a strategy built brick by brick
│   ├── 02_tutorial_step_by_step.ipynb    the same strategy, every step drawn
│   ├── 03_portfolio.ipynb                what a strategy is worth to a book
│   └── figures/                          GIFs written by the notebooks
└── vault/                     Obsidian vault (open this folder as a vault)
    ├── README.md              the vault's own start page
    ├── .obsidian/             shared Obsidian settings (templates folder, plugins)
    ├── 1 Ideas/               Idea Journal + Reading Notes/
    ├── 2 Strategies/          _Index + one sheet per strategy, in Testing/ Live/ Parked/ Killed/
    ├── 3 Research Log/        every variant ever run
    ├── 4 Portfolio/           Strategy Table, entry checklists, live monitoring notes
    ├── Templates/             blank notes to start from
    └── Toolbox/               reference notes used while working
```

How the pieces relate:

- The **data scripts** fill `data_fomc/` and `data_trend/`. Notebook 01 reads `data_fomc/`, both notebook 02s read `data_trend/`.
- Notebooks 01 and 02 produced the return series in `data_portfolio/` (`fomc_drift_trades.csv` and `xs_momentum_returns.csv`). Notebook 03 reads only `data_portfolio/`, so it runs without any download.
- Each strategy sheet in the vault names its notebook in its frontmatter (`code:` field), and each notebook ends with the verdict that is recorded in the sheet, the [Research Log](vault/3%20Research%20Log/Research%20Log.md) and the [Strategy Table](vault/4%20Portfolio/Strategy%20Table.md).

| Vault note | Notebook | Data |
| --- | --- | --- |
| [FOMC Drift](vault/2%20Strategies/Killed/FOMC%20Drift.md) (Killed) | [01_fomc_drift](notebooks/01_fomc_drift.ipynb) | `data_fomc/` |
| [Crypto XS Momentum](vault/2%20Strategies/Testing/Crypto%20XS%20Momentum.md) (Testing) | [02_crypto_xs_momentum](notebooks/02_crypto_xs_momentum.ipynb), [02_tutorial_step_by_step](notebooks/02_tutorial_step_by_step.ipynb) | `data_trend/` |
| [Crypto XS Momentum Checklist](vault/4%20Portfolio/Crypto%20XS%20Momentum%20Checklist.md), [FOMC Drift Monitoring](vault/4%20Portfolio/FOMC%20Drift%20Monitoring.md), [Strategy Table](vault/4%20Portfolio/Strategy%20Table.md) | [03_portfolio](notebooks/03_portfolio.ipynb) | `data_portfolio/` |
| [TSMOM 12-Month Trend](vault/2%20Strategies/Testing/TSMOM%2012-Month%20Trend.md) (Testing, Pass 2 open) | none yet: this is the exercise | none included |

---

## What the repository can do

### Notebooks

All notebooks use relative paths such as `../data_fomc`, so they must be run with `notebooks/` as the working directory (the default when you open them from that folder in Jupyter or VS Code). The numbers quoted below are the outputs saved in the notebooks; your rerun should match if your downloaded data covers the same dates.

#### `01_fomc_drift.ipynb`: FOMC Drift, from hypothesis to verdict

The first strategy of the course: one idea, eight trades a year, real bid/ask prices, and a kill criterion written before the result.

- **Hypothesis.** Before a scheduled Fed decision, funds cut their dollar exposure in advance; liquidity providers take the other side and charge a premium. The strategy takes the liquidity provider's side: long the other currencies against the dollar from 24 hours before the announcement, out 5 minutes before it, so it never holds the decision itself.
- **Data.** Dukascopy minute bars with bid and ask for six pairs (EUR/USD, GBP/USD, AUD/USD, NZD/USD, USD/CAD, USD/CHF) around every scheduled FOMC meeting, and the calendar in `data_fomc/event_calendar.csv`. 117 meetings from 2012-01-25 to 2026-09-16.
- **Method.** A look-ahead-safe "last bar at or before" lookup (12-minute tolerance for gaps); trade returns net of the spread (buy the ask, sell the bid) and gross mid-to-mid for comparison; the average short-USD path over the 24 hours before the announcement; EUR/USD alone (v1) and an equal-weight 6-leg short-USD basket (v2, long EUR/GBP/AUD/NZD against USD, short USD/CAD and USD/CHF, at least 5 legs with data). The sample is split before looking: in-sample 2012-2018, out-of-sample 2019-2024, "fresh" data 2025-2026.
- **Diagnostics.** Mean per trade in basis points, t-stat, hit rate; the pre-registered three-zone OOS-vs-IS rule (confirms / ambiguous / contradicts); a leg-by-leg table of recent meetings; equity, drawdown and yearly returns at a 10 % volatility target; a bootstrap of the 117 trades for the range of Sharpe and max drawdown; the live exit criterion (drawdown of the 10 %-vol equity below −20 %).
- **Result.** The basket earned about +11 bp per trade both in-sample and out-of-sample (t about 2.8), then −18.5 bp per trade on 2025-2026 (t −3.37, 2 winners in 14). Full sample: Sharpe 0.74, CAGR 7.1 %, max drawdown −29.5 %. The drawdown stop fired on 2025-10-29; the seven trades after it averaged −14.3 bp. **Status: killed.**

#### `02_crypto_xs_momentum.ipynb`: Crypto XS Momentum, brick by brick

The second strategy, built one section per "brick" (lessons 2.4 to 2.9 of the course).

- **Hypothesis.** Inside crypto, relative strength persists for weeks. A long/short book captures it without the market bet.
- **Data.** Daily close, funding rate and quote volume for Binance USDT perpetuals: 450 names with prices, 832 names with volume (dead contracts included), 2019-12-31 to 2026-08-24.
- **Universe (section 2).** A point-in-time top 20: crypto perps only, listed at least 180 days (count restarts after a gap of more than 14 days, for recycled tickers), ranked on trailing 30-day volume lagged one day, with hysteresis (enter at rank 20, leave beyond rank 25). The control is a **frozen** universe: today's top 20 applied to the whole past.
- **Signal (section 3).** Vol-adjusted daily returns (return divided by a blended EWMA volatility), summed over 7, 30 and 90 days skipping today's bar, each smoothed with an EMA of span h/4, divided by √h, averaged and multiplied by the forecast diversification multiplier (FDM 1.37 from an average correlation of 0.30 between speeds). The score is then made cross-sectional: demeaned with weights proportional to 1/volatility so that dollar positions sum to zero, scaled to an average absolute forecast of 10 on past data only, capped at ±20 and re-centred.
- **Sizing (section 4).** Inverse volatility to a 20 % annual target, 1/n across the day's members, an instrument diversification multiplier (IDM) measured each year on past data only and capped at √20 (1.0 in the first year), and a 10 % position buffer. P&L is split into price, funding (a long pays, a short collects) and costs at 5 bp one way.
- **Diagnostic (section 5).** Sharpe 0.74, t-stat 1.69, CAGR 12.4 %, vol 17.8 %, max drawdown −22.8 %, turnover 31.6× per year, breakeven cost 47 bp; P&L of +52.6 % price, +24.1 % funding, −8.2 % costs. A 63-day block bootstrap gives a Sharpe range of 0.19 to 1.52 (5 % to 95 %).
- **Survivorship (section 6).** The frozen universe shows Sharpe 0.82 but makes almost all its money on price (83 % vs 2 % funding), against a third on funding for the honest universe.
- **Regimes and concentration (section 7).** High-dispersion months earn +1.78 % a month against +0.42 % in low-dispersion months; the top 5 names make 69 % of the P&L, the top 10 days 60 % (Sharpe 0.32 without them); 36 % of the P&L comes from names not in today's top 20.
- **Variants (section 8).** Buffer sweep 0 / 5 / 10 / 20 % and cost sweep 1 / 5 / 10 / 20 bp (Sharpe 0.82 at 1 bp down to 0.48 at 20 bp).

#### `02_tutorial_step_by_step.ipynb`: the same strategy, every step drawn

The tutorial version of notebook 02: the same data and code, with a chart for every step, in five parts that follow the lessons.

| Part | Lesson | What it shows |
| --- | --- | --- |
| 1. The signal | 2.5 | Vol-adjusting (BTC vs 1000PEPE), the three speeds on SOL, smoothing and the count of sign flips, the √h scaling and FDM, ranking against the universe, why the demeaning must be weighted by 1/vol (a plain average leaves the book net short), scale and cap, the 20 most-held names over the last year, and an animated weekly view of the book saved to `notebooks/figures/signal_book.gif`. |
| 2. Sizing | 2.6 | Signal to dollars on one real day in a $1M book, inverse volatility through time, the strategy vol without the IDM (about 4 %) and the correlation rule that explains it, the IDM measured each 1 January, the buffer on SOL, and the effect of each brick (no IDM / no buffer / both). |
| 3. Reading the backtest | 2.7 | P&L split (funding is 31 % of the gross), turnover and its cost by hand, breakeven cost, block bootstrap of the Sharpe, return / vol / drawdown with the 1.5× exit line, the t-stat, then the Sharpe last. |
| 4. When it works, when it breaks | 2.8 | Dispersion on three real days and through time, P&L by halves and thirds of dispersion, the strength of the link (correlation, rank correlation, t-stat, no predictive power for next month), year-by-year price vs funding, concentration by coin and by day. |
| 5. What the Sharpe is worth | 2.10 | Nine design tries and the best Sharpe luck alone would give, correlation between rebuildable variants and the effective number of tries, the Bailey and López de Prado luck line and the haircut Sharpe (0.56 with 1.8 effective tries). |

Running it top to bottom takes a few minutes and writes the GIF.

#### `03_portfolio.ipynb`: from one strategy to a book

Answers "what is Crypto XS Momentum worth to a book?", then how to combine strategies, how to admit one, and when to cut one. It reads only `data_portfolio/`.

| Part | Lesson | Sections | What it does |
| --- | --- | --- | --- |
| 1 | 3.1 What you trade is a portfolio | 1 | Synthetic example with exact correlations: a Sharpe 1.5 candidate correlated 0.95 with the book adds nothing; a Sharpe 0.5 candidate correlated −0.3 lifts the book from 1.58 to 1.88. |
| 2 | 3.2 The marginal Sharpe rule | 2-6 | Tests XS Momentum against Portfolio 1 (Carry, Momentum, Low vol) and Portfolio 2 (Carry, Low vol) on their common history (2021-06-29 to 2026-08-24). Applies the rule *Sharpe(new) > correlation(new, book) × Sharpe(book)*, checks correlation in the book's worst 25 % of months, runs a paired 63-day block bootstrap (book improves in 62 % of histories for Portfolio 1, 91 % for Portfolio 2) and repeats it each quarter with past data only. |
| 3 | 3.3 Combine simply | 7-9 | Equal risk weights plus an IDM between strategies; shows max-Sharpe weights fitted on the first half losing to equal weights on the second half (1.66 vs 2.08); one risk budget per strategy family. |
| 4 | 3.4 Keep the portfolio alive | 10-11 | A new strategy enters at half weight (cost when right vs saving when wrong, across three "worlds"); book-level risk: IDM (2.14), loss limit at 1.5× the worst book drawdown, correlations on the book's worst days. |
| 5 | 3.5 Is the edge fading? | 12-16 | Three live tests written before going live: rolling 12-month Sharpe against the bootstrap 5th percentile, drawdown against 1.5× and 2× the worst backtest drawdown, and a CUSUM with an alarm calibrated for 5 % false alarms. Applied to XS Momentum from 2025 (all green) and FOMC Drift from 2025 (watch in July 2025, halve in September, cut on 2025-10-29). Ends with the "ship of Theseus" simulation (a book that adds and cuts strategies vs a frozen book, 40 simulated worlds) saved to `notebooks/figures/book_theseus.gif`, and an exercise. |

### Data scripts

Both scripts are run from the repository root, use parallel HTTP downloads with retries, and accept a `--workers` option (default 12; keep it moderate).

| Script | Source | What it builds | Options |
| --- | --- | --- | --- |
| [`data/get_fomc_minute.py`](data/get_fomc_minute.py) | Dukascopy public tick feed (`.bi5` hourly tick files) | `data_fomc/dukascopy_fomc_minute.parquet`: for each scheduled FOMC announcement in `event_calendar.csv`, from 25 hours before to 6 hours after, one row per minute per pair with bid and ask open/high/low/close and the tick count. Six pairs. About 117 windows × 31 hours × 6 pairs ≈ 22,000 files. | `--end` (default `2026-09-16 23:59` UTC), `--workers`, `--compare <old.parquet>` to diff against a previous file |
| [`data/get_crypto_daily.py`](data/get_crypto_daily.py) | Binance Vision monthly kline (1d) and funding-rate files for USDT-margined futures | `data_trend/prices_funding.parquet`: daily close, daily funding (sum of the day's payments) and quote volume for the 450 symbols in `symbols_prices.csv`, from 2019-12-31. `data_trend/volume_all_perps.parquet`: daily quote volume for all 832 symbols in `perp_meta.csv`, dead ones included, from 2020-01-01. About 40,000 files. | `--end` (default `2026-08-24`), `--workers`, `--compare-dir <folder>` to diff against previous files |

### Reference and portfolio data (included)

| File | Columns | Content |
| --- | --- | --- |
| `data_fomc/event_calendar.csv` | `date, event, announce_utc` | 243 scheduled announcements: 119 FOMC (2012-01-25 to 2026-12-09) and 124 ECB. Only FOMC rows are used by the code. |
| `data_trend/perp_meta.csv` | `symbol, underlying_type, contract_type, sector, onboard` | 832 USDT perpetual symbols with type (used to keep crypto only), sector and listing time. |
| `data_trend/symbols_prices.csv` | `symbol` | The 450 symbols whose prices and funding are downloaded. |
| `data_trend/universe_today.csv` | `symbol, base, sector` | 40 of today's largest names; the frozen-universe control picks its top 20 from these. |
| `data_portfolio/portfolio_1_returns.csv` | `date, Carry, Momentum, Low vol` | Daily net returns of a real crypto book of three strategies, 2021-02-18 to 2026-08-24. |
| `data_portfolio/portfolio_2_returns.csv` | `date, Carry, Low vol` | The same book without its Momentum strategy. |
| `data_portfolio/xs_momentum_returns.csv` | `date, price, funding, cost, total` | Daily P&L of Crypto XS Momentum from notebook 02, 2021-06-29 to 2026-08-24. |
| `data_portfolio/fomc_drift_trades.csv` | `announce_utc, basket_return` | The 117 basket returns of FOMC Drift from notebook 01, one per meeting. |

Portfolio returns are fractions (0.01 = 1 %), each strategy at its own volatility; notebook 03 rescales them. See [`data_portfolio/README.md`](data_portfolio/README.md).

### The vault

The vault is a set of linked Markdown notes. It is meant to be opened in [Obsidian](https://obsidian.md) (the links are `[[wiki links]]`), but every note is plain Markdown and readable anywhere. Start at [`vault/README.md`](vault/README.md).

**The four folders you fill**

| Folder | Notes | What they hold |
| --- | --- | --- |
| [`1 Ideas/`](vault/1%20Ideas) | [Idea Journal](vault/1%20Ideas/Idea%20Journal.md) | One entry per idea, newest first: date, title, mechanism in one sentence, family, source, status (new → reading → derived → sheet → dropped), revisit date, sheet link. Open entries are marked **new** or **reading**: pre-ECB/BoE drift, G10 FX carry, commodity carry, month-end cash needs, month-end USD flows at the London fix, token unlocks, COT positioning. Dropped ideas (weekend effect, buy-the-dip) are kept with the reason. |
| | [`Reading Notes/`](vault/1%20Ideas/Reading%20Notes) | Nine filled reading notes: Lucca and Moench 2015 (pre-FOMC drift), Savor and Wilson 2013, Ai and Bansal 2018, Cieslak, Morse and Vissing-Jorgensen 2019, Mueller, Tahbaz-Salehi and Vedolin 2017 (the five behind FOMC Drift); Moskowitz, Ooi and Pedersen 2012 (time-series momentum), Liu, Tsyvinski and Wu 2022 (crypto factors), Carver 2023 trend chapters (the three behind Crypto XS Momentum); Koijen et al. 2018 (carry, derived into two journal ideas). |
| [`2 Strategies/`](vault/2%20Strategies) | [_Index](vault/2%20Strategies/_Index.md) and one sheet per strategy | The folder is the status: `Testing/`, `Live/`, `Parked/`, `Killed/` (moving the note changes the status; `Killed/` is never emptied). Sheets: [FOMC Drift](vault/2%20Strategies/Killed/FOMC%20Drift.md) (killed, fully filled), [Crypto XS Momentum](vault/2%20Strategies/Testing/Crypto%20XS%20Momentum.md) (testing, paper at half weight, fully filled), [TSMOM 12-Month Trend](vault/2%20Strategies/Testing/TSMOM%2012-Month%20Trend.md) (Pass 1 written, Pass 2 left blank as the exercise). `Live/` and `Parked/` are empty. |
| [`3 Research Log/`](vault/3%20Research%20Log) | [Research Log](vault/3%20Research%20Log/Research%20Log.md) | One line per variant run (`date · what changed → result · verdict`), grouped by strategy, with a count of tries and the luck line at the top of each section. Filled for both course strategies. |
| [`4 Portfolio/`](vault/4%20Portfolio) | [Strategy Table](vault/4%20Portfolio/Strategy%20Table.md), [Crypto XS Momentum Checklist](vault/4%20Portfolio/Crypto%20XS%20Momentum%20Checklist.md), [FOMC Drift Monitoring](vault/4%20Portfolio/FOMC%20Drift%20Monitoring.md) | The book actually traded (Portfolio 2 of notebook 03 with XS Momentum at half weight, risk weights, exit criteria, five book rules), the entry decision for XS Momentum against both books, and the worked live-monitoring example of how FOMC Drift was caught. |

**Templates** ([`vault/Templates/`](vault/Templates)). Obsidian's Templates plugin is configured to use this folder with dates as `YYYY-MM-DD`; `{{title}}` and `{{date}}` fill in automatically.

| Template | Use it for |
| --- | --- |
| [Reading Note Template](vault/Templates/Reading%20Note%20Template.md) | Any source (paper, book chapter, fund letter, blog, own observation): mechanism in one sentence and its type, what was actually tested, numbers to remember, what surprised me, what it could secretly be, what may have changed since publication, how I would test it, and a derivations table that moves one brick at a time. |
| [Strategy Sheet Template](vault/Templates/Strategy%20Sheet%20Template.md) | One strategy, in two passes. **Pass 1** (before any code): 1.1 mechanism, 1.2 source and crowding, 1.3 expected behaviour, 1.4 universe, 1.5 horizon and data, 1.6 instrument traps, 1.7 backtest kill criterion (with the three-zone OOS rule), 1.8 live exit criterion, 1.9 test plan. **Pass 2** (after the diagnostic): 2.0 implementation to 2.9 what I did not test, then 2.10 verdict (keep / kill / park) and a changelog. |
| [Portfolio Checklist Template](vault/Templates/Portfolio%20Checklist%20Template.md) | Deciding whether a kept strategy enters the book: the marginal Sharpe rule, bad-times correlation, block bootstrap, sanity checks, decision (≥ 90 % enter at half weight, 60-90 % paper, < 60 % reject). |
| [Live Monitoring Template](vault/Templates/Live%20Monitoring%20Template.md) | A live strategy: performance (rolling 12-month Sharpe, drawdown, CUSUM), mechanics (hit rate, gain/loss, turnover, costs), structure (qualitative), decision rules (watch / halve / cut) and a review log. |
| [Strategy Table Template](vault/Templates/Strategy%20Table%20Template.md) | A new book: one row per strategy with risk weights and drawdown levels, per-strategy and book-level risk rules. |

**Toolbox** ([`vault/Toolbox/`](vault/Toolbox)): reference notes opened while working.

| Note | What it gives you |
| --- | --- |
| [My Research Routine](vault/Toolbox/My%20Research%20Routine.md) | The whole method as a schedule: weekly read / derive / test slots (5 to 8 hours), a monthly book review, a quarterly step back, healthy pipeline paces, and four non-negotiables. |
| [Strategy Families](vault/Toolbox/Strategy%20Families.md) | Seven families (trend, event-driven, carry, reversal, value, low vol, seasonality and flows), each with who pays, works when, breaks when, payoff, instrument traps, and reading. Feeds box 1.3. |
| [Instrument Traps](vault/Toolbox/Instrument%20Traps.md) | Why price return differs from position return: dividends, roll yield, perp funding, bond coupons, FX carry, bid/ask, survivorship, look-ahead in membership, each with a number. Feeds box 1.6. |
| [Reading the Diagnostic](vault/Toolbox/Reading%20the%20Diagnostic.md) | Every number in the diagnostic table (Sharpe, t-stat, bootstrap range, CAGR and vol, drawdown, turnover, breakeven, P&L split, concentration, regime), with how to read it and its trap, using the XS Momentum numbers. |
| [Deflated Sharpe](vault/Toolbox/Deflated%20Sharpe.md) | The luck line (expected best Sharpe of N no-edge tries over T years), the effective number of correlated tries, the haircut, and what counts as a try. Feeds box 2.7. |
| [Reading Sources](vault/Toolbox/Reading%20Sources.md) | Papers by family with links, where to search (SSRN, Quantpedia, AQR, Man Institute and others), books, fund letters. |
| [Data Sources](vault/Toolbox/Data%20Sources.md) | Free and paid data sources by use (crypto, futures, equities, FX, macro, calendars, COT, implied vol) and data traps. |
| [Glossary](vault/Toolbox/Glossary.md) | Every term used in the course, from "mechanism" and "the six bricks" to FDM, IDM, luck line and CUSUM. |

---

## How it works: the research pipeline

The vault's own diagram:

```
 1 Ideas  →  2 Strategies  →  4 Portfolio
 read,        Pass 1, build,     the book,
 derive       test, verdict      live monitoring
                   ↓
            3 Research Log
            every variant you run
```

Step by step, with the notes and notebooks involved at each stage:

1. **Read.** Pick a source from [Reading Sources](vault/Toolbox/Reading%20Sources.md) and fill a note from the Reading Note Template in `1 Ideas/Reading Notes/`. The first question is always the **mechanism**: who pays, and why (a risk premium, a constraint, a behavioural bias, or a scheduled flow). Add one row to the [Idea Journal](vault/1%20Ideas/Idea%20Journal.md). Do not test on the same day.
2. **Derive.** Take a journal idea that still looks good a week later. Break it into the **six bricks** (economic intuition, universe, signal, horizon, sizing, rebalancing) and move one brick at a time. Example: FOMC Drift moves the universe brick of Lucca and Moench from US equities to a short-USD FX basket; Crypto XS Momentum moves the universe and signal-method bricks of Moskowitz, Ooi and Pedersen from futures and time-series to crypto perps and cross-sectional.
3. **Write Pass 1 before any code.** Create a strategy sheet from the template in `2 Strategies/Testing/`. Use [Strategy Families](vault/Toolbox/Strategy%20Families.md) for "works when / breaks when" (box 1.3) and [Instrument Traps](vault/Toolbox/Instrument%20Traps.md) for box 1.6. Fix the kill criterion, the live exit criterion, the in-sample / out-of-sample split, the planned variants and the cost assumption now. Pass 1 is not edited after a result is seen. Add the strategy to [_Index](vault/2%20Strategies/_Index.md).
4. **Get the data.** Use or adapt the scripts in `data/`, or a source from [Data Sources](vault/Toolbox/Data%20Sources.md). The universe must be point-in-time, and the P&L must include everything that makes position return differ from price return (spread, funding, roll, dividends).
5. **Build and test in a notebook.** Follow the structure of notebooks 01 and 02: data, universe, signal, sizing (inverse vol, vol target, diversification multipliers), backtest net of costs with the P&L split into its sources. **Every run gets a line in the [Research Log](vault/3%20Research%20Log/Research%20Log.md) as it runs**, failures included.
6. **Diagnose.** The backtest diagnoses, it does not validate. Read the numbers in the order of [Reading the Diagnostic](vault/Toolbox/Reading%20the%20Diagnostic.md): P&L split and turnover first, then breakeven cost, the bootstrap range rather than the point Sharpe, concentration by asset and by day, behaviour by regime against Pass 1, and only then the Sharpe. Apply the out-of-sample three-zone rule. Count your tries and apply the luck-line haircut from [Deflated Sharpe](vault/Toolbox/Deflated%20Sharpe.md).
7. **Document the verdict.** Fill Pass 2 of the sheet and tick keep, kill or park. Move the note to the matching folder (`Live/`, `Killed/`, `Parked/`, or leave it in `Testing/` while on paper) and update `_Index`.
8. **Portfolio decision.** A kept strategy is judged against the book, never alone. Fill a Portfolio Checklist: the marginal Sharpe rule, correlation in the book's bad months, and a block bootstrap of the gain (≥ 90 % of histories: enter at half weight; 60-90 %: stay on paper and re-test each quarter; below 60 %: reject). Notebook 03, sections 2 to 6, is the worked procedure. If it enters, add a row to the [Strategy Table](vault/4%20Portfolio/Strategy%20Table.md) with its family, risk weight and drawdown levels (halve at 1.5× the worst backtest drawdown, cut at 2×).
9. **Monitor live.** Create a Live Monitoring note. Each review compares live results with what the backtest allows (rolling 12-month Sharpe, drawdown, CUSUM), and the status changes only on the written rules: watch, halve, cut. A half-weight strategy reaches full weight after at least six months if the checks are green. The [FOMC Drift Monitoring](vault/4%20Portfolio/FOMC%20Drift%20Monitoring.md) note and notebook 03, section 14, show a real cut.

The two course strategies are the reference for how much detail each step needs: FOMC Drift went through every step and was killed live by its pre-registered stop; Crypto XS Momentum went through every step and was admitted at half weight to Portfolio 2 while staying on paper for Portfolio 1.

---

## Workflow for a team member

### Setup

1. **Clone the team's copy and add the course remote.**

   ```
   git clone https://github.com/attiaj/the-systematic-trading-lab.git
   cd the-systematic-trading-lab
   git remote add upstream https://github.com/Quantreo/the-systematic-trading-lab
   ```

2. **Create a virtual environment with Python 3.12** (the version in `.python-version`; `.venv/` is already git-ignored).

   Windows (PowerShell):

   ```
   py -3.12 -m venv .venv
   .venv\Scripts\Activate.ps1
   ```

   macOS / Linux:

   ```
   python3.12 -m venv .venv
   source .venv/bin/activate
   ```

3. **Install the pinned packages.**

   ```
   pip install -r requirements.txt
   ```

   This installs numpy, pandas, pyarrow (parquet files), matplotlib, pillow (GIF export), requests (data scripts) and jupyter. In VS Code or PyCharm you only need `ipykernel`, which the editor offers to install the first time you open a notebook.

4. **Download the market data** (from the repository root; needed for notebooks 01 and 02, not for 03).

   ```
   python data/get_fomc_minute.py      # FOMC Drift  -> data_fomc/dukascopy_fomc_minute.parquet
   python data/get_crypto_daily.py     # Crypto XS Momentum -> data_trend/prices_funding.parquet, data_trend/volume_all_perps.parquet
   ```

   To extend the data past the course's end dates, pass `--end`, for example `python data/get_crypto_daily.py --end 2026-11-15`. Keep `--workers` moderate to respect the sources' rate limits.

5. **Open the vault in Obsidian.** *File → Open folder as vault* (or "Open folder as vault" on the start screen), then choose the `vault/` folder, not the repository root. Read [`vault/README.md`](vault/README.md) first. To start a note from a template, create an empty note, then open the command palette (Ctrl+P on Windows/Linux, Cmd+P on macOS) and run *Templates: Insert template*.

6. **Run the notebooks** from `notebooks/`, in order: 01, 02 (or the tutorial), 03.

### Routine for researching a new strategy

This follows [My Research Routine](vault/Toolbox/My%20Research%20Routine.md), adapted to a shared repository.

1. **Read.** In `vault/1 Ideas/Reading Notes/`, create a note named after the source (for example `Author Year Short Title`) and insert the Reading Note Template. Fill at least the mechanism in one sentence and question 4 (how I would test it with my data). Add a dated entry at the top of the [Idea Journal](vault/1%20Ideas/Idea%20Journal.md) with status **new** or **reading**.
2. **Wait, then derive.** After about a week, pick an entry that still looks good (yours or an open one). Fill the derivations table of its reading note, one brick changed per row, and choose one.
3. **Write Pass 1.** In `vault/2 Strategies/Testing/`, create a note named after the strategy and insert the Strategy Sheet Template. Fill every Pass 1 box, including 1.7 (kill criterion), 1.8 (exit criterion) and 1.9 (sample split, variants, cost in bp). Set the journal entry's status to **sheet** and link it. Add a row to [_Index](vault/2%20Strategies/_Index.md). Commit this before writing code, so the history shows Pass 1 came first.
4. **Get the data.** Prefer a script in `data/` modelled on the existing two, writing its output into a `data_<name>/` folder. Do not commit downloaded market data; add its pattern to `.gitignore` like `data_fomc/*.parquet`. Record the source and terms in [`data/SOURCES.md`](data/SOURCES.md).
5. **Build a notebook.** Add `notebooks/NN_<strategy>.ipynb`, starting from the structure of notebook 01 (event strategy) or 02 (cross-sectional strategy). Put the strategy's constants (cost, vol target, buffer, universe size) in the first code cell. Set the sheet's `code:` frontmatter field to the notebook path.
6. **Log as you go.** After each run, add a line under the strategy's section in the [Research Log](vault/3%20Research%20Log/Research%20Log.md): `date · what changed → result · verdict`. Mark stress tests and controls so they are not counted as tries.
7. **Diagnose and fill Pass 2.** Produce the diagnostic table of the sheet (IS / OOS / full), the P&L split, concentration, regime table and the bootstrap range. Count tries and apply the haircut. Tick the verdict and move the note to the right folder; update `_Index`.
8. **If kept, take it to the book.** Export the strategy's daily returns to a CSV in `data_portfolio/` (same format as `xs_momentum_returns.csv`), rerun sections 3 to 6 of notebook 03 against it (the exercise in section 16), and fill a note from the Portfolio Checklist Template in `vault/4 Portfolio/`. If it enters, add its row to the Strategy Table and create its Live Monitoring note.
9. **Review.** Monthly, update the Strategy Table and each Live Monitoring note. Quarterly, rerun the bootstrap for strategies on paper and reread the Research Log.

A good first exercise for a new team member is the one the course leaves open: Pass 2 of [TSMOM 12-Month Trend](vault/2%20Strategies/Testing/TSMOM%2012-Month%20Trend.md). Agree within the team on who owns it before starting, since only one copy of the sheet exists.

### Git workflow

- **Work on a branch.** One branch per strategy or task, for example `git checkout -b strategy/pre-ecb-drift`. Open a pull request into `main` on `origin`.
- **Commit notes and notebooks together.** A commit that changes a notebook should also carry the Research Log lines and sheet boxes it produced. Commit Pass 1 on its own first, before any code, so the order is visible in the history.
- **Keep notebook diffs readable.** Consider clearing large outputs before committing, or at least avoid committing reruns that change nothing but outputs.
- **Do not commit** downloaded data (`*.parquet`), virtual environments, or personal Obsidian layout (`vault/.obsidian/workspace*.json` is already ignored). The shared `.obsidian/*.json` settings are tracked; change them only deliberately.
- **Shared notes conflict most.** The Idea Journal, Research Log, `_Index` and Strategy Table are edited by everyone. Pull before editing them, keep edits small, add lines rather than rewriting existing ones, and push soon after.
- **Pull course updates regularly** and publish them to the team:

  ```
  git checkout main
  git pull upstream main
  git push
  ```

  Then update your branch from `main` (`git merge main` or `git rebase main`). To keep these pulls clean, avoid editing the course's own worked examples (the FOMC Drift and Crypto XS Momentum notes, the reading notes and the Toolbox) unless you mean to; put the team's work in new notes.

---

## Data

| Data | Included? | Source | Terms |
| --- | --- | --- | --- |
| FX minute bid/ask around FOMC meetings | No, downloaded by `data/get_fomc_minute.py` | Dukascopy Bank SA historical tick data, https://www.dukascopy.com | Dukascopy's terms of use apply to your download: https://www.dukascopy.com/swiss/english/legal-pages/terms-of-use/ |
| Crypto perpetuals, daily close, funding, quote volume | No, downloaded by `data/get_crypto_daily.py` | Binance Vision public datasets, https://data.binance.vision | CC BY-NC-SA 4.0 under the Binance Vision Dataset Terms: your own non-commercial research and backtesting |
| FOMC / ECB calendar, perp symbol lists, today's universe | Yes (`data_fomc/`, `data_trend/` CSVs) | Built for the course; the calendar is from the public Federal Reserve and ECB calendars | Small reference lists, not market data |
| Portfolio returns (two books, XS Momentum P&L, FOMC Drift trades) | Yes (`data_portfolio/`) | Built for the course; the two strategy series come from notebooks 01 and 02 | |

The market data is not redistributed in this repository: each user downloads it from the original source, for their own use. Full details are in [`data/SOURCES.md`](data/SOURCES.md).

---

## Things to know

- **Saved outputs come from the course author's run.** Rerunning on data downloaded with the default `--end` dates should reproduce them; extending `--end` will change the numbers.
- **Small differences between notes and notebooks.** The `_Index` and Research Log quote a full-sample Sharpe of 0.78 for FOMC Drift, while notebook 01 and the FOMC Drift sheet report 0.74. The Crypto XS Momentum sheet's frontmatter says "276 names", while its Pass 2 and notebook 02 report 450 names with prices and 832 with volume. The notebooks are the computed source.
- **Referenced but not included.** The Crypto XS Momentum sheet mentions a research notebook `07_momentum_crypto_xs.ipynb` that is not in this repository. The [Data Sources](vault/Toolbox/Data%20Sources.md) note contains placeholders (`[QUANT LAKE LINK]`, `[COUPON CODE]`) for a separate Quantreo programme.
- **Figures.** `notebooks/figures/signal_book.gif` is written by the tutorial notebook and `book_theseus.gif` by notebook 03. `book_slots.gif` is included but no notebook in this repository generates it.
- **Templates location.** Notebook 03 refers to `4 Portfolio/Portfolio Checklist Template`; the template itself is in `vault/Templates/`.

---

## Disclaimer

Educational material only. Nothing here is investment advice, and no strategy in this repository is a recommendation to trade.

---

## Original README (Quantreo)

The README of the upstream course repository, kept verbatim with its headings demoted by two levels.

### The Systematic Trading Lab

The research vault and notebooks of the course **[The Systematic Trading Lab](https://www.quantreo.com/systematic-trading-lab/)**: find, design, diagnose and document a systematic strategy, the way a desk does, then decide if it deserves a place in a portfolio.

#### What is inside

| Folder | What it holds |
| --- | --- |
| `vault/` | An Obsidian vault: idea journal, strategy sheets, research log, portfolio, templates and toolbox. Open it with *File → Open folder as vault*. |
| `data_portfolio/` | Daily returns of two crypto books, of Crypto XS Momentum, and the FOMC Drift trades. Used by notebook 03, no download needed. |
| `notebooks/` | `01_fomc_drift`: a strategy tested and killed. `02_crypto_xs_momentum`: a strategy built brick by brick. `02_tutorial_step_by_step`: the same, every step drawn on real data. `03_portfolio`: what a strategy is worth to a book, how to combine, when to cut. |
| `data/` | The scripts that download the market data, and their sources. |

#### Setup

Python 3.12.

```
pip install -r requirements.txt
python data/get_fomc_minute.py      # FOMC Drift
python data/get_crypto_daily.py     # Crypto XS Momentum
```

The market data is not in this repository: you download it from the original sources. See `data/SOURCES.md` for the sources and their terms of use.

#### The course

The videos, exercises and Discord are in the course: [quantreo.com/systematic-trading-lab](https://www.quantreo.com/systematic-trading-lab/)

#### Disclaimer

Educational material only. Nothing here is investment advice, and no strategy in this repository is a recommendation to trade.
