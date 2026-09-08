# Market-Implied Inflation Forecasts from Kalshi Prediction Markets

**Can prediction-market prices provide useful real-time forecasts of U.S. inflation?**

This repository contains my end-to-end research implementation of a UCLA Master of Quantitative Economics (MQE) QuantLab project evaluating Kalshi CPI year-over-year prediction markets. I carried out the data acquisition, API troubleshooting, data processing, forecast-construction methodology, validation, statistical evaluation, benchmark comparison, and written analysis presented here.

## Key findings

- **Probabilistic forecasts are informative and improve as the release approaches.** Mean Brier scores are approximately **0.08 three weeks before release** and **0.06–0.07 in the final days**, versus 0.25 for an uninformative 50/50 forecast.
- **Directional performance is high.** The market identifies the direction of CPI YoY changes correctly about **97% of the time**, well above naive momentum and majority-class benchmarks near 60%.
- **Point forecasts are competitive with a professional benchmark.** At the one-day horizon, the market's mean absolute error is **0.075 percentage points**, compared with **0.122** for the Federal Reserve Bank of Cleveland inflation nowcast.
- The written study evaluates **44 monthly CPI releases from November 2022 through June 2026**, with one unpublished realization excluded from scoring.

## Research workflow

```text
Kalshi REST API
      ↓
Live / historical schema reconciliation
      ↓
Contract metadata + daily candlesticks
      ↓
Fixed horizons: 21d / 14d / 7d / 3d / 1d / final
      ↓
Threshold prices → survival curve S(t) = P(CPI > t)
      ↓
Isotonic regression for monotonicity
      ↓
Outcome-bin probability distribution
      ↓
Median / mode / dispersion summaries
      ↓
BLS scoring + Cleveland Fed / naive benchmarks
```

## Methodology

### 1. Data acquisition and normalization
The pipeline retrieves CPI threshold-market data from Kalshi and handles structurally different live and historical endpoint schemas. Contract metadata and candlestick observations are normalized into a consistent long-format representation.

### 2. Fixed-horizon forecasts
Each monthly release is standardized to six pre-release evaluation horizons: 21, 14, 7, 3, and 1 calendar day before release, plus the final pre-release observation.

### 3. Probability-distribution construction
Threshold contracts imply a survival curve, $S(t)=P(CPI>t)$. Because independently traded contracts can create local monotonicity violations, I use isotonic regression to enforce a weakly decreasing survival curve before differencing adjacent thresholds into outcome-bin probabilities.

### 4. Validation
The primary threshold-based series is cross-checked against Kalshi's independently priced mutually exclusive CPI outcome-bin series.

### 5. Forecast evaluation
Forecasts are evaluated using Brier scores, directional hit rates, confidence-weighted directional accuracy, and point-forecast error. Professional benchmarking uses contemporaneous Cleveland Fed inflation nowcasts on matched release months.

## Repository structure

```text
notebooks/
  01_data_and_forecast_pipeline.ipynb
  02_cross_series_validation.ipynb
  03_forecast_evaluation.ipynb

data/processed/
  fixed_horizon_snapshots.csv
  forecast_panel_adjusted.csv
  forecast_panel_summary.csv

results/
  brier_by_release_horizon.csv
  mean_brier_by_horizon.csv
  benchmark_directional.csv

paper/
  kalshi_cpi_prediction_markets.pdf
```

## Project context and contribution

This research was completed within **UCLA MQE QuantLab**. I independently executed the project end-to-end, including research implementation, data acquisition and cleaning, API troubleshooting, forecast construction, validation, statistical evaluation, benchmark comparison, and written analysis. The PDF in `paper/` is the portfolio version of the written deliverable.

## Data sources

- **Kalshi public REST API** — CPI year-over-year prediction-market data
- **U.S. Bureau of Labor Statistics** — realized CPI-U index values
- **Federal Reserve Bank of Cleveland** — inflation nowcast benchmark

Raw API responses and local benchmark source files are not included here. Processed research outputs used for the portfolio analysis are included for transparency.

## Reproducing the analysis

Install dependencies:

```bash
pip install -r requirements.txt
```

Then run the notebooks in order. The BLS registration key is optional; if you use one, set it as an environment variable rather than placing it in code:

```bash
export BLS_API_KEY="your_key_here"
```

The Cleveland Fed benchmark section expects local vintage files in `cleveland_nowcast/`; these source files are not redistributed in this repository.

## Limitations

The sample covers a relatively short and largely disinflationary macroeconomic regime, so results should not be interpreted as proof that prediction markets will dominate professional forecasts in other regimes. Year-over-year CPI observations are serially dependent, market prices may embed spreads and risk premia, and some thinly traded contract-days rely on quote midpoints rather than executed trades.

## Version note

The written portfolio paper reports the project sample through June 2026. Some processed files in this repository contain a subsequently collected July 2026 market event; the headline findings above follow the written study's stated sample and interpretation.

---
**Python · pandas · NumPy · scikit-learn · REST APIs · probabilistic forecasting · forecast evaluation**
