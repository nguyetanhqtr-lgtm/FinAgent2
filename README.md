# FinAgent: AI-Powered Financial Data Agent

> **Course:** IT Application in Banking and Finance  
> **Assessment:** Midterm Hackathon Project  
> **Stack:** Python · yfinance · Groq LLaMA 3.3 · Plotly · Pandas

---

## 1. Project Overview

FinAgent is an end-to-end AI-powered financial data agent that autonomously collects, cleans, visualizes, and analyzes financial market data. The system mirrors real-world fintech and data engineering workflows, integrating a Large Language Model (LLM) to generate grounded, data-referenced natural language analysis.

The agent tracks **11 assets** (8 stocks + 3 macro indicators) over a 2-year period (2023–2024) and features an interactive **ReAct Agent loop** that answers natural language questions about the data by autonomously selecting and calling the appropriate analysis tool.

---

## 2. Assets Tracked

| Category | Assets |
|---|---|
| **Tech Stocks** | AAPL, AMZN, META, MSFT, NVDA, TSLA |
| **Defensive Stocks** | JNJ, JPM |
| **Macro Indicators** | S&P 500, Gold, Oil |

---

## 3. Project Structure

```text
FinAgent/
│
├── Data_Collection.ipynb      # Module 1 — Data collection via yfinance
├── data_cleaning.ipynb        # Module 2 — Cleaning, feature engineering
├── visualization.ipynb        # Module 3 — Charts and dashboards
├── mainn.ipynb                # Module 4 — Full pipeline + AI Agent
│
├── data/
│   ├── raw/
│   │   ├── stock2_prices.csv
│   │   └── macro2_prices.csv
│   └── processed/
│       └── cleaned_data.xlsx
│
├── outputs/
│   ├── ai_analysis_output.json
│   └── agent_output.json
│
├── .env.example               # API key template (never commit real keys)
├── requirements.txt           # All dependencies
└── README.md
```

---

## 4. Modules

### Module 1 — Data Collection (`Data_Collection.ipynb`)
- Fetches stock and macro price data using **yfinance**
- Covers 2 source types: stock prices and macro/commodity indicators
- Exports raw CSV files for downstream processing

### Module 2 — Data Cleaning (`data_cleaning.ipynb`)
Handles all common financial data quality issues:
- **Missing values** — forward-fill + backward-fill (standard for time series)
- **Duplicate records** — detected and removed with logging
- **Data type normalization** — dates, numerics, currency formatting
- **Feature engineering** — daily returns, 7-day MA, 30-day MA, 30-day rolling volatility
- **Outlier detection** — flags days where returns exceed 3 standard deviations from mean

### Module 3 — Visualization (`visualization.ipynb`)
Produces 4 distinct chart types:
1. **Price Trend + Volume Overlay** — AAPL close price with MA7, MA30, and volume bar chart
2. **Correlation Heatmap** — cross-asset daily return correlations
3. **KDE Distribution** — daily return distributions for NVDA, TSLA, JNJ, JPM
4. **Bollinger Bands** — NVDA rolling statistics with upper/lower bands

### Module 4 — AI Analysis + Agent (`mainn.ipynb`)
Runs the full pipeline end-to-end and includes:

**AI Analysis (Section 2.4):**
- Trend summary for each of the 11 assets
- Anomaly commentary (returns ≥ 3σ flagged across all assets)
- Risk ranking based on average volatility
- Pairwise asset comparisons (NVDA/TSLA, AAPL/MSFT, Gold/SP500)

**AI Agent — ReAct Loop:**
- User asks a natural language question
- Agent **reasons** which tool to call
- Agent **acts** by executing the tool on real data
- Agent **observes** the result and generates a data-backed answer

Available tools:

| Tool | Description |
|---|---|
| `get_snapshot` | 30-day performance snapshot for any asset |
| `get_anomalies` | Detect extreme return days (≥3σ) |
| `compare_assets` | Side-by-side comparison of 2 assets |
| `get_risk_ranking` | Rank all assets by volatility |

---

## 5. Setup & Installation

### Prerequisites
- Python 3.9+
- A free [Groq API key](https://console.groq.com)

### Install dependencies
```bash
pip install -r requirements.txt
```

### Configure API key
```bash
cp .env.example .env
# Open .env and paste your Groq API key
```

### Run the full pipeline
Open `mainn.ipynb` in VS Code or Jupyter and run all cells top to bottom.

---

## 6. API Keys & Security

- **Never commit real API keys** to GitHub
- Use `.env` file locally (already in `.gitignore`)
- See `.env.example` for the required format
- Groq API is free — get your key at [console.groq.com](https://console.groq.com)

---

## 7. LLM Integration

**Model used:** `llama-3.3-70b-versatile` via Groq API  
**Why Groq:** Free tier, no rate limit issues, extremely fast inference (LPU hardware)  
**Prompt strategy:**
- Structured JSON data passed as context in every prompt
- Model explicitly instructed to reference specific numbers only
- Temperature set to 0.3 for consistent, factual output
- System prompt enforces 150-word limit per section to prevent verbosity

**Hallucination prevention:**
- All prompts include real computed data (not descriptions)
- System instruction: *"Do NOT invent figures not in the provided data"*
- Output is grounded in actual computed snapshots, not general knowledge

---

## 8. Design Decisions

| Decision | Rationale |
|---|---|
| Forward-fill for missing values | Standard practice for financial time series (weekends/holidays) |
| 30-day volatility window | Industry standard for monthly volatility estimation |
| 3σ outlier threshold | Statistically robust; flags ~0.3% of observations |
| Simple daily returns over log returns | Better interpretability for visualization and LLM context |
| Groq LLaMA over OpenAI | Free tier sufficient; avoids paid API dependency |
| 4 API calls for 11 assets | Batching all assets in one prompt reduces quota usage by 8× |

---

## 9. Output Files

| File | Description |
|---|---|
| `cleaned_data.xlsx` | Fully processed dataset with all features |
| `ai_analysis_output.json` | LLM-generated trend, anomaly, risk, and comparison analysis |
| `agent_output.json` | AI Agent responses to demo questions |

---

## 10. Requirements

```
pandas
numpy
matplotlib
seaborn
plotly
groq
python-dotenv
openpyxl
yfinance
requests
```

---

## 11. Limitations & Future Improvements

- **Rate limits:** Groq free tier has daily quotas — production use would require a paid plan
- **Static data:** Currently uses historical data (2023–2024); real-time streaming not implemented
- **Single asset per snapshot:** Agent tools analyze one asset at a time; portfolio-level analysis not yet supported
- **No persistent memory:** Agent context resets between sessions

---

*Built for the FinAgent Hackathon-Like Midterm Project — IT Application in Banking and Finance*
