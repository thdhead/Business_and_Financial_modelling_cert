# The Captstone (in progress)
**Purpose:** produce a complete, end‑to‑end decision recommendation from a provided dataset.

**What you do:**
- build a model and recommend a business strategy based on the model outputs
- present a recommendation in a professional-quality PowerPoint with data + analysis

**Capstone workflow:**
- 1) Start by computing returns and summary statistics from historical financial data and use those outputs to support later portfolio/allocation reasoning
- 2) Finish with a decision recommendation and slide deck
- 3) Make a slide deck demonstraating the importance of portfolio diversification

## You will find here:
1) An Excel file that is a small “financial analytics lab” built around historical market data. It takes daily price series for **10 individual equities** plus a **market index**, converts them into daily returns, summarizes the behavior of each asset (return and risk), and then uses those results to:
- compare assets side-by-side,
- build and evaluate portfolios (2-asset and 10-asset),
- estimate **CAPM** parameters (alpha and beta) via regressions against the market.

The core date range is **2010-06-30 → 2016-06-30**.

---

## Data
### Daily market data sheets (one per asset)
The workbook contains one worksheet per asset:

- **AAPL, BIDU, COP, DIS, GOOG, TSLA, TTM, XOM, MSFT, WFC** (stocks)
- **DJI** (market proxy / index)

Each of these sheets follows the same pattern:
1. **Raw daily OHLCV + Adjusted Close** (date, open/high/low/close, volume, adjusted close).
2. **Daily return series** created from the price history (both close-based and adjusted-close-based returns).
3. A **summary panel** with descriptive statistics of the return series (mean, standard deviation, variance, min/max, skew/kurtosis, etc.) and a **Sharpe Ratio** style summary.

> Note: the summary panels are presented as “results tables”. If you replace the underlying price/return history, the summary panels should be refreshed to reflect the new data.

---

## Sheet-by-sheet walkthrough

### 1) `AAPL`, `BIDU`, …, `WFC` (individual stock sheets)
**Purpose:** Turn raw prices into return data and basic risk/return statistics.

**What happens here:**
- Daily prices are laid out in a standard market-data format.
- A daily return series is created to make assets comparable over time.
- A descriptive statistics block summarizes the distribution of returns (central tendency, volatility, tails, and range).
- A Sharpe-style metric is calculated as a quick “return per unit of risk” indicator (risk-free assumption is treated as 0).

**How other sheets use it:**
- Portfolio sheets pull the *mean return* and *volatility* from these summary blocks.
- Correlation/covariance tables reference the daily return columns.

---

### 2) `DJI` (market/index sheet)
**Purpose:** Provide the “market return” series used as a benchmark.

**What happens here:**
- The index is treated like an asset: prices → daily returns.
- These market returns are used as the independent variable for CAPM regressions.

---

### 3) `CAPM`
**Purpose:** Estimate CAPM-style parameters for each stock relative to the market.

**What happens here:**
- A consolidated table places **each stock’s daily returns** next to the **DJI daily returns** for the same dates.
- For each stock, a linear regression is run against the market returns.
- The sheet stores the standard regression outputs:
  - **alpha (intercept)**
  - **beta (sensitivity to the market)**
  - basic fit diagnostics (e.g., R², standard error, ANOVA-style summaries)

**Layout note:**
- Regressions are saved in blocks (several outputs stacked vertically and arranged in two columns across the sheet).

**Maintenance note:**
- Regression output cells behave like “captured results.” If you change the return data, you’ll want to rerun the regressions and paste/replace the outputs.

---

### 4) `MSFT_WFC_Port`
**Purpose:** Two-asset portfolio exploration (weights, risk, return).

**What happens here:**
- The sheet pulls the **mean return** and **volatility** for **MSFT** and **WFC** from their individual sheets.
- It also derives the **relationship between the two assets** (how they move together) using the paired daily returns.
- A weight grid (from 0%→100% in steps) evaluates many portfolio mixes and reports for each mix:
  - expected portfolio return
  - portfolio variance / standard deviation
  - Sharpe-style score

**Use case:**
- Quickly see how changing the allocation between two assets changes risk and performance.

---

### 5) `10_Stock_Port`
**Purpose:** Multi-asset (10-stock) portfolio assembly + risk model.

**What happens here:**
- Builds a **Correlation Table** across all 10 stocks (based on daily returns).
- Builds a **Covariance Table** across all 10 stocks (based on daily returns).
- Includes an area where **portfolio weights** are specified for each stock.
- Produces portfolio-level outputs:
  - expected portfolio return (from the weighted means)
  - portfolio variance and standard deviation (from the covariance structure)
  - a Sharpe-style score
- Also includes a compact “asset characteristics” section that references CAPM-style parameters (beta/alpha) for convenience when thinking about systematic risk.

**How to use it:**
- Edit the weights in the weights column (they should represent the allocation you want; they can also be negative if you’re testing short positions).
- Interpret the portfolio summary values to compare different weight sets.

---

### 6) `AAPL Monthly`
**Purpose:** Monthly-return version of the AAPL workflow.

**What happens here:**
- A smaller dataset at a monthly frequency is used to compute monthly returns.
- The same style of descriptive statistics panel is provided to compare what “return behavior” looks like at a different sampling frequency.

---

### 7) `tests`
**Purpose:** Sandbox / validation sheet.

**What happens here (now):**
- Recreates a two-asset portfolio (MSFT/WFC) using a simple probability-weighted setup over a set of weight scenarios.
- Computes portfolio returns for each scenario and then summarizes them into expected return and variability, as a way to cross-check results conceptually.

---

## Assumptions and interpretation notes
- Returns are treated as **simple period-over-period returns** (not log returns).
- Sharpe-style ratios are computed using a **0 risk-free baseline**.

---

## Typical workflow
1. **Update data (optional):**
   - Replace the OHLCV/Adj Close columns in each asset sheet with new data.
   - Keep the column structure the same so downstream references still point to the correct return series.

2. **Verify alignment:**
   - Ensure the assets share the same trading dates (or handle missing dates consistently), especially if you plan to use the correlation/covariance and CAPM sheets.

3. **Refresh summary outputs:**
   - Update descriptive stats panels on each asset sheet so the mean/volatility used by portfolio sheets remains correct.

4. **Portfolio experiments:**
   - Use `MSFT_WFC_Port` to explore two-asset mixes.
   - Use `10_Stock_Port` to set weight vectors and compare portfolio-level performance.

5. **CAPM refresh (when needed):**
   - If return data changed, rerun the regressions and replace the stored outputs in `CAPM`.

---

## Quick map of “where to change things”
- Change / paste new price data: **each asset sheet** (`AAPL`, `MSFT`, …, `DJI`)
- Change 2-asset weights grid: **`MSFT_WFC_Port`**
- Change 10-asset portfolio weights: **`10_Stock_Port`**
- Update alpha/beta outputs: **`CAPM`** (rerun regressions when underlying data changes)
