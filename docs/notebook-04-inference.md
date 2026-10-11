# Notebook 04 — statistical inference

Reference for `notebooks/04_statistical_inference.ipynb`. Uses the same Kyiv load/dedupe pattern as notebooks 02–03. Kernel: **weather-assessment**.

## 1. Winter comparison (DJF 2024–25 vs 2025–26)

- Filter meteorological winter months; label `winter` and `obs_hour`.
- Account for observation-hour drift between winters (overlap of hours may be sparse).
- Primary tools: Welch / Mann–Whitney on comparable subsets; OLS `temperature_celsius ~ C(winter) + C(obs_hour)` on the DJF panel.

## 2. Uncertainty on holdout MAE

Bootstrap (B ≈ 5000) on absolute errors from the Ridge one-step holdout in notebook 02. Report percentile CI for MAE on the fixed 80/20 split.

## 3. Weather OLS (`statsmodels`)

Example formula:

```text
temperature_celsius ~ humidity + pressure_mb + wind_kph + precip_mm
```

Kyiv rows with complete predictors. Report coefficients, p-values, R². Note serial correlation (Durbin–Watson) on time-ordered rows.

## 4. Time-series diagnostics

- ADF on temperature levels (`statsmodels.tsa.stattools.adfuller`).
- ACF/PACF plots.
- Ljung–Box on Ridge holdout residuals (`y_test - pred_ridge`).

## Dependencies

`scipy` and `statsmodels` are pinned in `requirements.txt`. Results are summarized in `reports/REPORT.md` § Statistical inference.
