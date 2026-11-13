# Synthetic Oracle: Stock Signal Research Project

> Live demo: https://stock-advisor-here.netlify.app

An educational research project that turns daily price data into stock signals, and tests honestly whether those signals work. It is a final-year MCA project, not a trading product and not financial advice.

**Headline result:** after correcting a volatility bias in the original target, the ML ensemble showed **no measurable predictive edge** (mean yearly AUC 0.5156 vs 0.5178 for a simple low-volatility rule; rank IC 0.0023, t = 0.27). Details are in `EXPERIMENT_RESULTS.md`. The dashboard is a working demo of the pipeline, not evidence of a tradable strategy.

---

## What it does

You type a stock symbol. The system returns:

- **Signal**: BUY / HOLD / SELL from a rule-based decision engine that uses the ML ensemble as one of its inputs
- **ML score**: the ensemble's output, shown as **uncalibrated** (it is not a probability)
- **Trade plan**: entry, stop, target and R:R, produced by fixed rules (not validated for profit)
- **Technical score and setup score**: rule-based (the technical score is known to saturate; see `KNOWN_LIMITATIONS.md`)
- **SHAP explanation**: which features pushed the ML score
- **AI narrative**: a text summary written by an LLM (Groq, LLaMA 3.3 70B) from the numbers above; it explains, it does not predict
- **TradingView chart**: embedded for visual context
- **Paper-trading journal**: logs signals to a Google Sheet so outcomes can be tracked honestly over time `[VERIFY: optional integration]`

---

## How it works

1. **Data**: daily prices for a project-selected universe of 147 symbols (NSE and US) from Yahoo Finance via `yfinance`.
2. **Features**: 22 price-based features (moving-average distance, volatility as ATR/price, momentum, RSI, ADX, Bollinger, MFI, stochastic and more).
3. **Target**: will the stock rank in the **top 30% of stocks by 10-day forward return** on that date? (A relative rank, not "up 30%".)
4. **Models**: five classifiers (Decision Tree, Random Forest, Logistic Regression, XGBoost, Gradient Boosting) combined into an ensemble.
5. **Validation**: walk-forward by year with purging of overlapping labels.
6. **Decision engine**: layered rules turn the ML output plus technical conditions into a signal and trade plan.
7. **Composite score**: 50% ML, 30% technical, 20% keyword sentiment. The weights are fixed and were not tuned for performance.

```
User -> Netlify frontend -> Render FastAPI backend
        -> yfinance data -> 22 features -> 5-model ensemble
        -> rule-based decision engine -> trade plan
        -> optional LLM narrative -> JSON -> dashboard
```

---

## Research findings so far

- The original target favoured volatile stocks (40.5% of winners came from the most volatile third, vs 33.3% expected by chance).
- A volatility-adjusted, market-separated target removed that bias. On it, the ensemble did **not** beat a simple low-volatility rule.
- Accuracy numbers from the original target are not evidence of skill, and are not reported as such.
- The 147-symbol universe was chosen by hand in hindsight, so any result carries selection bias.

## Planned work (not implemented)

A pre-registered test of whether news sentiment (FinBERT) adds ranking information on US stocks. The design, success rule and the possibility of a null result are written down before any run. Until it is run and validated, sentiment stays informational and out of the model.

---

## Tech stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vanilla HTML, CSS, JavaScript |
| Charts | TradingView widget |
| Backend | FastAPI, Python |
| ML | scikit-learn, XGBoost, SHAP |
| Data | yfinance (unofficial source, rate-limited) |
| Narrative | Groq API (LLaMA 3.3 70B), optional |
| Deployment | Netlify (frontend), Render (backend) |

---

## API endpoints `[VERIFY each route against backend/main.py]`

| Endpoint | Description |
|----------|-------------|
| `GET /predict?symbol=AAPL` | Signal analysis |
| `GET /morning-scan` | Scan of the symbol list |
| `GET /scan` | Full scan |
| `GET /health` | System status |
| `GET /backtest?symbol=AAPL` | Simple trade backtest ("Win rate (trades)") |
| `GET /expectancy?symbol=AAPL` | Expectancy stats |

---

## Running locally

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
```
Secrets (Groq key, Google credentials) belong in environment variables or files **outside the repository**. Never commit them. `[VERIFY: commands match your setup]`

---

## Documentation

- `PROJECT_ARCHITECTURE.md`: how the system is built
- `RESEARCH_METHODOLOGY.md`: labels, validation, leakage controls
- `EXPERIMENT_RESULTS.md`: the volatility-adjusted experiment
- `MODEL_CARD.md`: what the models are and are not
- `KNOWN_LIMITATIONS.md`: honest list of weaknesses
- `DEMO_GUIDE.md`: how to run the demo

---

## Author

Built by **Harshvardhan Kasliwal**, final-year MCA, NIT Jamshedpur.
[LinkedIn](https://www.linkedin.com/in/harshvardhan-kasliwal-675207229/) · [GitHub](https://github.com/unfitcoder101) · [X](https://x.com/unfitcoder)

## Disclaimer

Educational and research use only. Not financial advice. The research found no demonstrated predictive edge. Do not trade real money based on this system.
