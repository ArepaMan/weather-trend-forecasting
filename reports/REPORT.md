# Weather Trend Forecasting on the Global Weather Repository

**Author:** Manuel Vargas Alvarez  
**Assessment:** PM Accelerator — Data Scientist / Analyst (Advanced track)  
**Date:** September 2026

---

## Executive summary

This project analyzes the Kaggle Global Weather Repository—a panel of daily weather observations for 268 cities worldwide—and builds a reproducible pipeline from data cleaning through exploratory analysis, anomaly detection, multi-model forecasting, and supplementary environmental and geographical studies. The primary forecasting task uses **Kyiv** daily **temperature (°C)** indexed by **`last_updated`**, with a strict chronological train/test split. On a 20% holdout, a **Ridge regression model on lagged temperatures** is the **best single model** (MAE **2.474°C**, RMSE **3.599°C**; ensemble ties MAE), slightly outperforming a persistence baseline and substantially outperforming a seasonal naive forecast. Advanced requirements are addressed through robust and multivariate anomaly methods, lag-based feature importance, air-quality correlations, and country-level temperature aggregates.

---

## PM Accelerator mission

> *“We are committed to offering free Product Management education to teenagers from underserved families. Our mission is to break down financial barriers and achieve educational fairness… empower more kids for a better future… fostering a diverse landscape in the tech industry.”*  
> — **PMA KIDS** ([pmaccelerator.io](https://www.pmaccelerator.io))

Product Manager Accelerator serves a global community of aspiring and current product managers with training and career programs in product and AI.

---

## Data and cleaning

The dataset contains **167,813** rows and **41** columns, including location metadata, current conditions, air-quality fields, and astronomical times. Each city appears many times over time (~**863** observations for Kyiv), forming a panel suitable for trend and forecast analysis rather than a single cross-section.

Cleaning steps were deliberate and minimal because the file showed **no missing values** in the columns used:

1. **`last_updated`** was parsed with `pd.to_datetime(..., errors="coerce")`, yielding **zero** parse failures.
2. **Full-row duplicates:** none.
3. **Key duplicates:** one pair of rows shared the same `(location_name, last_updated)` for Nan, Thailand (a valid place name, not a null)—one row was dropped, leaving **167,812** rows.
4. Forecasting and EDA use **`last_updated`** as the time index per assessment instructions (not `last_updated_epoch` alone).

No imputation was applied. **Normalization:** weather variables were **not** min–max or z-score scaled; temperatures remain in °C for interpretability, and lag features share that scale. Global IQR outlier counts were documented in notebook 01 for context; extreme days are analyzed with robust z-scores and Isolation Forest in notebook 03 rather than global clipping.

### Data caveats

- **Evaluation horizon:** holdout metrics are **one-step-ahead** (persistence / lags predict the next observed day using known history).
- **Timestamp spacing:** typical spacing **~24 h** (mean gap **24 h 03 m** on Kyiv); observation **hour drifted** from afternoon (**≈15–17 h**, 2024) to morning (**≈9 h**, 2026), so morning readings run colder and year-over-year comparisons are partly time-of-day biased.
- **Snapshot example:** Kyiv **2026-08-17 09:00** records **1.1°C** (feels-like −4.3) between neighbors at 21–24°C—likely a scrape error; **in the holdout window**, inflating naive RMSE (≈3.75 → ≈2.97 if excluded).

---

## Exploratory data analysis

**City selection:** Kyiv was chosen for modeling because it has a long, dense series (863 points from **2024-05-16** to **2026-09-27**) with clear seasonal structure.

**Sampling:** Successive observations are mostly spaced by about one day (~**24 h**; mean gap **24 h 03 m** on Kyiv), with occasional shorter or longer gaps. **Because the observation hour drifts, readings aren’t fixed to a single time of day.**

**Temperature:** The Kyiv series shows pronounced seasonality, with summer peaks near **37°C** and winter lows near **−21°C** in the sample; the winter of 2025–26 appears colder than the prior winter in this slice **(partly reflecting earlier observation hours)**.

**Precipitation:** The `precip_mm` field is a **reading at observation time** in the scrape (not a daily total)—mostly near zero with intermittent spikes—so it was used for **EDA and anomaly features**, not as a forecast target here.

Visualizations are saved as `kyiv_temperature.png` and `kyiv_precipitation.png`.

---

## Forecasting methodology and results

**Target:** `temperature_celsius` for Kyiv.  
**Split:** First **80%** of timestamps for training, last **20%** for testing—**no random shuffle**, to prevent temporal leakage.

Four approaches were evaluated on the holdout set:

| Model | MAE (°C) | RMSE (°C) |
|-------|----------|-----------|
| Naive (last value) | 2.509 | 3.753 |
| Seasonal naive (365-row lag) | 5.993 | 7.528 |
| Ridge (lags 1, 2, 3, 7, 14, 28) | **2.474** | **3.599** |
| Ensemble (mean of Ridge + naive) | 2.474 | 3.653 |

The **naive** forecast is strong because daily temperature is highly autocorrelated. **Ridge** improves MAE and RMSE modestly by combining several lags with L2 regularization. The **seasonal naive** model uses the value from **365 rows earlier**. On the holdout, that lag spans **~365.8–365.9 days**, so calendar date alignment is within about a day. It loses because **last year’s weather on that date differs from today** by ordinary variability, while persistence uses yesterday. Kyiv’s **observation hour drift** (afternoon in 2024 → morning in 2026) adds **time-of-day bias** to year-over-year comparisons (MAE **5.993°C**).

An **ensemble** is the unweighted mean of **Ridge and naive**, the two lowest holdout-MAE models. MAE **rounds** to 2.474°C for both Ridge and the ensemble; **Ridge has lower RMSE** (3.599 vs 3.653°C).

Holdout predictions are visualized in `kyiv_forecast_holdout.png`. **MAE** is emphasized in interpretation because it expresses average error in degrees Celsius; **RMSE** highlights days with larger misses.

---

## Advanced analyses

### Anomaly and outlier detection

Two complementary methods were applied to Kyiv:

1. **Robust z-scores** from a 30-day rolling median and median absolute deviation (MAD), flagging **9** days with |z| > 3.5. **Example:** **2026-08-17** at **1.1°C** in the **forecast test window** (likely scrape error). The **2024-05-16** flags are **edge effects** of the centered rolling window at the series start.
2. **Isolation Forest** on temperature and precipitation (expected contamination 2%), labeling **18** points—the **top 2% by construction**.

Disagreement in counts is expected: the first method focuses on univariate extremes relative to recent local climate; the second flags joint unusual combinations of rain and temperature.

Figure: `kyiv_anomalies.png`.

### Feature importance

Ridge coefficients on lag features show **lag_1 ≈ 0.796**, **lag_7 ≈ 0.093**, and smaller contributions from lags 3 and 14. Near-zero coefficients for **lag_2** and **lag_28** reflect **high correlation among lags** (lag_2 vs lag_1 **r ≈ 0.96**), not a separate “L2 shrinkage” story; coefficients are from a **full-series** fit and would shift if refit on training data only.

Figure: `kyiv_lag_importance.png`.

### Environmental impact (air quality)

For Kyiv, Pearson correlations between weather variables and air-quality fields show notable associations—for example, **temperature and ozone (r ≈ 0.54)** and **humidity and ozone (r ≈ −0.49)**, with moderate links between pressure and sulphur dioxide / nitrogen dioxide. These patterns are reported as **associations in this dataset**, not established causal mechanisms.

Figure: `kyiv_air_quality_corr.png`.

### Geographical patterns

Mean `temperature_celsius` by `country` uses only countries with **≥100 rows** (186 included; 25 sparse excluded). **Highest:** Qatar, United Arab Emirates, Cambodia (~32–33°C). **Lowest:** Iceland, Mongolia, Canada (~5.7–6.6°C). Country strings mix languages/scripts; aggregates are over scrape timestamps, not WMO climate normals.

Figure: `country_mean_temperature.png`.

---

## Limitations and future work

- **Single-city forecast** depth trades off against global multi-series modeling (hierarchical models, per-city ensembles).
- **Observation-hour drift** biases year-over-year baselines; calendar and hour-of-day features (month, day-of-year, hour) would help.
- **Precipitation** was analyzed in EDA but not forecast here; zero-inflated or classification approaches may fit better.
- **Production extensions:** normalized geography labels, proper backtesting, and external validation against official station data.

---

## Reproducibility

Code is organized in Jupyter notebooks (`01_cleaning_and_eda`, `02_forecasting`, `03_advanced_analyses`), with dependencies in `requirements.txt` and instructions in the repository `README.md`. The Kaggle CSV is excluded from version control; reviewers download `GlobalWeatherRepository.csv` into `data/raw/`.

---

## Conclusion

The project delivers an advanced assessment workflow: **time-ordered holdout evaluation**, transparent cleaning, multiple forecast benchmarks with an ensemble, and extended analyses on anomalies, feature importance, air quality, and geography. The main quantitative result is **day-ahead forecasting at ~2.5°C MAE**, marginally better than persistence, using lag-based Ridge regression on holdout data, with clear documentation of what worked, what failed (seasonal naive), and what should improve next.
