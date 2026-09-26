# Stock Portfolio Risk Analyzer

A Python project that fetches 5 years of real market data for a 6-stock diversified portfolio and performs end-to-end quantitative risk analysis — from Monte Carlo simulation to a Random Forest risk classifier.

Built as a demonstration of financial risk analytics concepts relevant to institutional portfolio management.

---

## Portfolio

| Ticker | Company | Sector |
|--------|---------|--------|
| AAPL | Apple | Technology |
| JPM | JPMorgan Chase | Banking |
| XOM | ExxonMobil | Energy |
| JNJ | Johnson & Johnson | Healthcare |
| MSFT | Microsoft | Technology |
| GS | Goldman Sachs | Financial Services |

Stocks were deliberately chosen across different sectors to enable meaningful correlation and diversification analysis.

---

## Modules

### Module 1 — Data Fetching
- Fetches 5 years of daily closing prices (2020–2025) using `yfinance`
- Uses `auto_adjust=True` to correct for stock splits and dividend payments
- Output: clean DataFrame of shape (trading days × 6 stocks)

### Module 2 — Daily Returns
- Computes daily percentage returns manually using the formula:
  `(Price_today - Price_yesterday) / Price_yesterday`
- Drops the first NaN row

### Module 3 — Monte Carlo Simulation (Value at Risk)
- Defines equal portfolio weights (1/6 each)
- Computes weighted portfolio daily returns
- Generates 10,000 random scenarios from a normal distribution using historical mean and std
- Calculates:
  - **95% VaR** — maximum loss expected on 95% of trading days
  - **99% VaR** — maximum loss expected on 99% of trading days
- Plots histogram of simulated returns with both VaR lines marked

### Module 4 — Correlation Analysis
- Computes pairwise correlation matrix of all 6 stocks' daily returns
- Visualizes as an annotated seaborn heatmap
- Key finding: AAPL–MSFT and JPM–GS show high correlation (same sector exposure)

### Module 5 — Rolling Volatility
- Computes 30-day rolling standard deviation of returns
- Annualizes using the square root of time rule (× √252)
- Plots as a line chart — one line per stock
- Visible spikes during COVID-19 (March 2020) and 2022 rate hike period

### Module 6 — Sharpe Ratio Analysis
- Calculates annualized Sharpe Ratio for each individual stock and the overall portfolio
- Formula: `(Annualized Return - Risk Free Rate) / Annualized Volatility`
- Risk-free rate: 4% (approximate US T-bill rate)
- Portfolio Sharpe (0.73) outperforms 4 of 6 individual stocks — diversification benefit visible in real numbers

### Module 7 — Portfolio Optimization (Efficient Frontier)
- Generates 10,000 random weight combinations across the 6 stocks
- For each combination calculates annualized return, volatility (using covariance matrix), and Sharpe Ratio
- Identifies the optimal portfolio (maximum Sharpe Ratio)
- Plots the efficient frontier as a scatter plot colored by Sharpe Ratio
- Marks both the optimal portfolio and equal-weight portfolio for comparison
- Optimal portfolio Sharpe (0.87) vs equal-weight Sharpe (0.73)

### Module 8 — Random Forest Risk Classifier
- **Features** (5 rolling features on portfolio returns):
  - 5-day and 20-day rolling mean return
  - 5-day and 20-day rolling standard deviation
  - Previous day's return
- **Labels**: High-risk day = portfolio return below the 25th percentile (worst 25% of days)
- **Train/Test Split**: Chronological 80/20 split — no shuffling to prevent data leakage
- **Model**: Random Forest (100 trees, max depth 5, balanced class weights)
- **Results**: 77% overall accuracy, 56% recall on high-risk days
- **Feature Importance**: 5-day rolling mean is the strongest predictor (42% importance)

---

## Key Results

| Metric | Value |
|--------|-------|
| 95% VaR | ~2.5% daily loss |
| 99% VaR | ~3.4% daily loss |
| Portfolio Sharpe Ratio | 0.73 |
| Optimal Portfolio Sharpe | 0.87 |
| RF Overall Accuracy | 77% |
| RF High-Risk Recall | 56% |

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| `yfinance` | Real market data |
| `pandas` | Data manipulation |
| `numpy` | Numerical computation |
| `matplotlib` + `seaborn` | Visualization |
| `scikit-learn` | Random Forest classifier |
| `scipy` | Portfolio optimization |

---

## How to Run

1. Clone the repository
2. Install dependencies:
   ```
   pip install -r requirements.txt
   ```
3. Open `Stock_Portfolio_Analysis.ipynb` in Google Colab or Jupyter
4. Run all cells top to bottom

---

## Project Structure

```
stock-portfolio-analysis/
│
├── Stock_Portfolio_Analysis.ipynb    ← main notebook
├── requirements.txt                  ← dependencies
├── README.md                         ← this file
└── data/
    └── README.md                     ← data is fetched live via yfinance
```

---

## Data

All data is fetched live from Yahoo Finance via the `yfinance` library. No static data files are stored in this repository. Running the notebook will always pull the latest available data for the specified date range (2020-01-01 to 2025-01-01).