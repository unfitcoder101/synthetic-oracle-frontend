# Synthetic Oracle — AI Quantitative Trading Dashboard

> Live: https://stock-advisor-here.netlify.app

A production-grade quantitative trading dashboard that surfaces AI-generated stock signals across 500+ symbols from NSE, NYSE, and NASDAQ. Built for swing traders who want institutional-grade analysis without the institutional price tag.

---

## What it does

You type a stock symbol. The system returns:

- **Signal** — BUY / HOLD / SELL with confidence score
- **Trade Plan** — exact entry price, stop loss, target, R:R ratio, position size
- **A+ Setup Score** — ranks setup quality 0–10 based on multi-factor confluence
- **Decision Engine Output** — 13 layered filters that explain why the signal fired
- **SHAP Explainability** — which features drove the ML prediction
- **AI Analysis** — natural language trade thesis powered by Groq LLaMA 3.3 70B
- **TradingView Chart** — live chart embedded for visual confirmation

---

## Features

### Signal Engine
- Ensemble ML model — XGBoost, Random Forest, Gradient Boosting, Decision Tree, Logistic Regression
- 74% validation accuracy on swing trade labels (5% target / 3% stop / 10-day window)
- 22 engineered features — RSI, ADX, Bollinger Bands, momentum, volatility, MFI, Stochastic, Williams %R, Darvas Box, Livermore pivots, 52W breakout, gap patterns

### Morning Scan
- Scans 500+ symbols in parallel (20 workers, ThreadPoolExecutor)
- Returns ranked candidates sorted by setup quality score
- Runs in under 3 minutes on free-tier cloud

### Trade Plan Calculator
- Van Tharp R-multiple position sizing
- Dynamic trailing stop levels
- Pyramid entry signals at +10% and +20%
- Risk per trade: configurable (default 2-3% of capital)

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vanilla HTML, CSS, JavaScript |
| Charts | TradingView Widget |
| AI Analysis | Groq API (LLaMA 3.3 70B) |
| Backend | FastAPI, Python |
| ML | scikit-learn, XGBoost, SHAP |
| Data | yfinance |
| Deployment | Netlify (frontend), Render (backend) |

---

## Architecture

User → Netlify Frontend → Render FastAPI Backend
↓
yfinance data fetch
↓
Feature engineering (22 features)
↓
Ensemble ML prediction
↓
Decision engine (13 filters)
↓
Trade plan calculator
↓
Groq AI analysis
↓
JSON response → Dashboard render

---

## API Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /predict?symbol=AAPL` | Signal analysis |
| `GET /morning-scan` | Market scan |
| `GET /scan` | Full scan |
| `GET /health` | System status |
| `GET /backtest?symbol=AAPL` | Backtest |
| `GET /expectancy?symbol=AAPL` | Expectancy stats |

---

## Sample Output

```json
{
  "symbol": "HINDALCO.NS",
  "prediction": "BUY",
  "confidence": 91.3,
  "a_plus_score": 6,
  "trade_plan": {
    "entry": 1149,
    "stop_loss": 1100,
    "target": 1250,
    "risk_reward": "2.1:1",
    "position_size": 40
  }
}
```

---


## Disclaimer

This system is for educational and research purposes only. Not financial advice. Past signal accuracy does not guarantee future performance. Always do your own research before making any investment decisions.

