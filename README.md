
# Indian Stock Market Microstructure Analysis

A beginner-friendly, end-to-end data science capstone project analyzing historical Indian stock market data (NIFTY 50 / BANK NIFTY / individual NSE-listed stocks) to understand market behavior through data analysis, feature engineering, clustering, and basic machine learning.

> **Scope note:** This is a *research and behavioral analysis* project, not a trading system. All findings describe **historical patterns** and should not be interpreted as financial advice or predictive trading signals.

---

## 📌 Project Goal

Analyze historical Indian stock market data to understand market behavior by:
- Studying price and volume patterns through Exploratory Data Analysis (EDA)
- Engineering features that describe market "microstructure" (volatility, price range, momentum, etc.) from daily OHLCV data, as a simplified proxy for true tick-level microstructure
- Discovering natural **market regimes** (e.g., calm vs. turbulent periods) using unsupervised clustering (KMeans, Hierarchical Clustering)
- Validating those regimes with basic supervised learning and rigorous statistical hypothesis testing

---

## 📁 Project Structure

```
indian-stock-market-microstructure-analysis/
├── data/
│   ├── raw/              # Raw downloaded OHLCV data (CSV, via yfinance) — not committed, see .gitignore
│   └── processed/        # Cleaned / feature-engineered data
├── notebooks/
│   └── Indian_Stock_Market_Microstructure_Analysis.ipynb   # Main analysis notebook (all milestones)
├── images/                # Exported charts/figures for reports or the README
├── reports/               # Written summaries / final report exports (e.g., PDF)
├── src/                   # (Optional) reusable Python scripts/functions, if refactored out of the notebook
├── requirements.txt
├── .gitignore
└── README.md
```

---

## 🛠️ Tech Stack

| Purpose | Library |
|---|---|
| Data acquisition | `yfinance` |
| Data manipulation | `pandas`, `numpy` |
| Visualization | `matplotlib`, `seaborn`, `plotly` (optional) |
| Statistics | `scipy` |
| Machine Learning | `scikit-learn` |
| Environment | Jupyter Notebook |

---

## 🚀 Getting Started

### 1. Clone the repository
```bash
git clone <your-repo-url>
cd indian-stock-market-microstructure-analysis
```

### 2. Set up a virtual environment (recommended)
```bash
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Launch Jupyter and run the notebook
```bash
jupyter notebook notebooks/Indian_Stock_Market_Microstructure_Analysis.ipynb
```

Run the cells **top to bottom** — the notebook downloads data via `yfinance` in an early cell (requires an internet connection), then proceeds through cleaning, EDA, feature engineering, hypothesis testing, clustering, optional supervised learning, and evaluation.

> 💡 You can analyze a different index/stock by changing the `TICKER` variable in the data download cell (e.g., `"^NSEI"` for NIFTY 50, `"^NSEBANK"` for BANK NIFTY, or `"RELIANCE.NS"` for an individual stock).

---

## 📊 Methodology Overview

1. **Project & Concept Framing** — what market microstructure means, and why daily OHLCV data is used as a simplified proxy
2. **Data Acquisition** — historical data via Yahoo Finance (`yfinance`)
3. **Data Inspection & Cleaning** — shape, types, missing values, duplicates, logical-consistency checks
4. **Exploratory Data Analysis** — time series, univariate/bivariate analysis, correlation, volume/return/volatility/moving-average analysis
5. **Feature Engineering** — returns, rolling statistics, volatility, price range, lag features, RSI
6. **Hypothesis Testing** — T-test, ANOVA, and correlation significance testing on the volume–volatility relationship
7. **Clustering** — KMeans (Elbow Method + Silhouette Score) and Hierarchical Clustering (dendrogram) to identify market regimes
8. **(Optional) Supervised Learning** — validating cluster structure with a classifier (not future forecasting)
9. **Model Evaluation** — confusion matrix, precision/recall/F1
10. **Insights & Conclusions** — synthesized, appropriately-qualified findings and explicit limitations

---

## 🔍 Key Findings

> *(Fill this in after running the notebook on your real data — see the "Insights and Conclusions" section of the notebook for the full write-up template.)*

- Optimal number of market regimes identified: **[K]**
- Volume–volatility relationship: **[significant / not significant]**, r = **[value]**
- Regime classifier validation accuracy: **[X]%**

---

## ⚠️ Limitations

- Uses daily OHLCV data as a simplified proxy for true tick-level market microstructure.
- Clustering reflects **historical** patterns only; regime structure can shift over time.
- The supervised learning step validates same-day feature-to-regime mapping, not future prediction.
- Findings are specific to the analyzed ticker and date range.

---

## 🔮 Future Scope

See Step 19 in the notebook for a full discussion. Briefly: extending to intraday/tick-level data where available, testing regime *persistence* (does today's regime predict tomorrow's?), adding more Indian stocks/sectors for comparison, and exploring more advanced models (e.g., Hidden Markov Models for regime switching).

---

## 📖 Data Dictionary

| Column | Type | Description |
|---|---|---|
| `Date` | datetime (index) | Trading day. Markets are closed on weekends and NSE holidays, so dates are non-contiguous — this is expected. |
| `Open` | float | Price of the first trade of the day. |
| `High` | float | Highest price traded during the day. |
| `Low` | float | Lowest price traded during the day. |
| `Close` | float | Price of the last trade of the day. |
| `Adj Close` | float | Close price adjusted for dividends/splits. **Always used for return calculations** to avoid fake jumps around corporate actions. |
| `Volume` | int | Total shares/contracts traded during the day. |
| `Daily_Return` | float | `Adj Close` percentage change from the previous day. |
| `Log_Return` | float | Logarithmic return; additive across periods, unlike simple returns. |
| `Price_Range` / `Price_Range_Pct` | float | Intraday `High − Low`, in absolute ₹ and as a % of price. |
| `Rolling_Mean_20` / `Rolling_Std_20` | float | 20-day rolling mean/standard deviation of `Adj Close`. |
| `Volatility_21d` | float | 21-day rolling standard deviation of `Daily_Return` (≈ 1 trading month). |
| `MA20` / `MA50` | float | 20-day / 50-day moving averages of `Adj Close`. |
| `Return_Lag1/2/3` | float | Daily return from 1, 2, 3 days prior. |
| `Volume_Lag1` | float | Volume from 1 day prior. |
| `RSI_14` | float | 14-day Relative Strength Index (0–100 momentum indicator). |
| `Cluster_KMeans` / `Cluster_Hierarchical` | int | Assigned market regime from each clustering method. |

---

## 🛠️ Troubleshooting / FAQ

**Q: All my columns show `dtype: object` after loading the CSV, and I get a `KeyError` on `Adj Close`.**
A: This is a known `yfinance` gotcha. Recent versions (1) default to `auto_adjust=True`, which removes the separate `Adj Close` column, and (2) return MultiIndex columns for a single ticker, which corrupts dtypes when round-tripped through CSV. Fix: download with `yf.download(TICKER, start=..., end=..., auto_adjust=False)` and flatten columns with `df.columns = df.columns.get_level_values(0)` before saving — see the Step 3 cell in the notebook, which already includes this fix.

**Q: Can I analyze a different stock/index?**
A: Yes — change the `TICKER` variable in the Step 3 cell (e.g., `"RELIANCE.NS"`, `"TCS.NS"`, `"^NSEBANK"`) and re-run the notebook from that cell onward.

**Q: Why are there `NaN` values after feature engineering?**
A: Rolling/lag calculations (e.g., a 20-day moving average) genuinely can't produce a value until enough history exists. These rows are dropped once, right before clustering/modeling — this is expected, not a data error.

**Q: My clustering results look different from a previous run.**
A: Check that `random_state` is set on `KMeans` — without it, cluster labels (though not necessarily their meaning) can vary slightly between runs due to random initialization.

**Q: The notebook needs internet access — can I run it offline after the first time?**
A: Yes. Step 3 saves the downloaded data to `nifty50_data.csv`. Once that file exists, you can skip re-running the download cell and just load the CSV directly in Step 5.

---

## 🤝 Contributing

This is primarily an individual/team capstone project, but if extending it:
1. Create a feature branch off `main` (e.g., `feature/hmm-regime-switching`).
2. Keep notebook edits scoped to one section at a time to avoid `.ipynb` merge conflicts (see `Team_Workflow_and_Task_Division.md` for the recommended multi-person workflow).
3. Re-run the full notebook top-to-bottom before opening a pull request, to confirm reproducibility.

---

## 📚 Related Documents

This repository is accompanied by three additional documents (provided alongside this README):
- **Research Report** — background, literature context, hypotheses, and methodology in academic report form.
- **Final Report** — the complete project write-up: methodology, results, insights, limitations, and future scope.
- **Insights Document** — a condensed, findings-only summary for quick reading or presentation.
- **Team_Workflow_and_Task_Division.md** — a 7-day sprint plan for a 3-person team.



---

## 👤 Author

*(Your name here)* — Data Science / ML Internship Capstone Project

## 📄 License

*(Add a license of your choice, e.g., MIT — see [choosealicense.com](https://choosealicense.com/))*
