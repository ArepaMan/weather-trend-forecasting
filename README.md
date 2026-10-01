# Weather Trend Forecasting (PM Accelerator — Advanced DS Assessment)

Analysis and forecasting on the Kaggle [**Global Weather Repository**](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository): daily weather for cities worldwide (41 features, 167k+ rows). This repo implements the **advanced** track: cleaning, EDA, anomaly detection, multi-model forecasting with ensemble, feature importance, air-quality correlations, and geographical aggregates.

**Forecast focus:** `temperature_celsius` for **Kyiv**, indexed by **`last_updated`**.

---

## Results summary

| Area | Highlights |
|------|------------|
| **Data** | 167,813 rows → 167,812 after 1 duplicate `(location_name, last_updated)`; 268 locations; ~863 obs/city for Kyiv |
| **EDA** | Seasonal Kyiv temp (~−21°C to ~37°C); spiky precipitation; ~daily sampling with small timing gaps |
| **Models (80/20 time split)** | Naive MAE 2.509°C · Seasonal-365 MAE 5.993°C · Ridge (lags) MAE **2.474°C** · Ensemble (Ridge+naive) MAE 2.474°C |
| **Advanced** | 9 robust-z anomalies; 18 Isolation Forest outliers; lag_1 dominates Ridge coefs; temp↔ozone r≈0.54 (Kyiv) |

Full narrative, metrics, and report bullets: [`reports/REPORT_NOTES.md`](reports/REPORT_NOTES.md).

Assessment requirements: [`docs/ASSESSMENT.md`](docs/ASSESSMENT.md).

---

## Repository layout

```
weather-trend-forecasting/
├── data/raw/              # Kaggle CSV (not in git) — see data/README.md
├── docs/                  # Assessment doc + ASSESSMENT.md
├── notebooks/
│   01_cleaning_and_eda.ipynb
│   02_forecasting.ipynb
│   03_advanced_analyses.ipynb
├── reports/               # Figures + REPORT_NOTES.md (+ your slide export)
├── requirements.txt
└── README.md
```

### Figures (under `reports/`)

| File | Content |
|------|---------|
| `kyiv_temperature.png` | Kyiv temperature time series |
| `kyiv_precipitation.png` | Kyiv precipitation time series |
| `kyiv_forecast_holdout.png` | Actual vs ensemble on test period |
| `kyiv_anomalies.png` | Robust-z anomalies on temperature |
| `kyiv_lag_importance.png` | Ridge lag coefficients |
| `kyiv_air_quality_corr.png` | Weather vs air-quality correlation heatmap |
| `country_mean_temperature.png` | Mean temp by country (top/bottom 10) |

---

## Setup

**Requirements:** Python **3.14** (project venv; not tested on 3.10–3.13).

```powershell
cd weather-trend-forecasting
python -m venv .venv

# Windows
.\.venv\Scripts\Activate.ps1

# macOS/Linux
# source .venv/bin/activate

python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name weather-assessment --display-name "Python (weather-assessment)"
```

### Dataset

1. Download from [Kaggle](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository).
2. Place the CSV in `data/raw/` as **`GlobalWeatherRepository.csv`** (see [`data/README.md`](data/README.md) for CLI option).

The CSV is **gitignored** (large file). Reviewers must download their own copy.

---

## Running the analysis

Open Jupyter with the project root as the working directory:

```powershell
jupyter notebook
```

Run in order; select kernel **Python (weather-assessment)**:

1. **`notebooks/01_cleaning_and_eda.ipynb`** — load, clean, Kyiv EDA, temp/precip plots  
2. **`notebooks/02_forecasting.ipynb`** — train/test split, naive / seasonal / Ridge, ensemble, metrics  
3. **`notebooks/03_advanced_analyses.ipynb`** — anomalies, feature importance, air quality, geo charts  

Figures are written to `reports/` when you run the savefig cells.

---

## Methods (short)

- **Cleaning:** `pd.to_datetime` on `last_updated`; drop duplicate keys; no imputation; **no z-score/min–max scaling** (documented IQR scan in notebook 01).
- **Forecasting:** Chronological 80/20 split; **one-step-ahead** holdout; naive; seasonal naive = value **365 rows back** (calendar date within ~1 day on holdout—still loses to weather variability + observation-hour drift); Ridge on lags `[1,2,3,7,14,28]`; ensemble = unweighted mean of Ridge + naive (lowest MAE pair).
- **Metrics:** MAE and RMSE on holdout `temperature_celsius` (°C).
- **Anomalies:** Rolling 30-day median/MAD robust z (threshold 3.5); Isolation Forest on temp + precip (2% contamination).
- **Limitations:** Kyiv observation-hour drift; Kyiv 2026-08-17 test-window snapshot; country means use **≥100 rows/country**; mixed-language labels; correlations ≠ causation.

---

## Submission (PM Accelerator)

- Public GitHub repo (or private with access for `community@pmaccelerator.io`, `hr@pmaccelerator.io`)
- This README + `requirements.txt`
- Report or slides including **PM Accelerator mission** ([pmaccelerator.io](https://pmaccelerator.io))
- 1–2 minute demo video (code + outputs)

---

## Author

Manuel Vargas Alvarez — PM Accelerator Data Scientist technical assessment (2026).
