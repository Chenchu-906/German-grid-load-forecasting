# ⚡ German Electricity Grid Demand Forecasting (ENTSO-E)

An end-to-end multi-step hourly load forecasting pipeline for the German power grid, progressing from naive heuristics to probabilistic Quantile Gradient Boosted Trees (LightGBM).

![Model Benchmarks](figures/forecast_benchmark_plot.png)

## 📌 Problem Formulation
Transmission System Operators (TSOs) require reliable 7-day-ahead demand projections to schedule unit commitment and maintain spinning reserves. This project forecasts a 168-hour (1-week) holdout horizon strictly avoiding temporal lookahead bias and recursive error accumulation.

## 🛠️ Methodology & Modeling Progression
1. **Flat Baseline (24h Moving Average):**
   - Projects the prior 24-hour mean constantly across the test week.
   - Highlights the pitfall of ignoring diurnal cycles (~16.4% MAPE, massive night over-predictions).
2. **Cyclical Baseline (Hour-of-Week Seasonal Profile):**
   - Historical grouping by `(day_of_week, hour)`.
   - Captures intra-day ramps and weekend drops (~7.4% MAPE), but creates a persistent -4,009 MW negative bias due to changing seasonal consumption baselines.
3. **Quantile LightGBM (P10 / P50 / P90):**
   - **Cyclical Features:** Trigonometric $\sin / \cos$ transformations for hour-of-day and day-of-week.
   - **Autoregressive Lags:** Bounded strictly to $t \ge 168$ (`lag_168`, `lag_336`) to reflect real operational cutoffs without leakage.
   - **Rolling Momentum:** 168-hour rolling mean shifted by 168 hours to capture broad seasonal level shifts.
   - **Probabilistic Risk Corridors:** Trained with Pinball loss ($\alpha=0.10, 0.50, 0.90$) to provide an 80% confidence interval for grid dispatch decisions.

## 📊 Benchmark Results

| Model | Bias (MW) | MAE (MW) | RMSE (MW) | MAPE (%) |
| :--- | :---: | :---: | :---: | :---: |
| **Moving Average (24h)** | +2,150 | 8,119 | 9288.33 | 16.4% |
| **Seasonal Profile** | -4,009 | 4,009 | 4188.57 | 7.38% |
| **LightGBM (P50)** | **-2125.98** | **2125.98** | **2296** | **3.95%** |


## 🚀 Getting Started

1. Clone the repository:
```bash
git clone [https://github.com/](https://github.com/chenchu-906/german-grid-load-forecasting.git
cd Utility_Forecaste