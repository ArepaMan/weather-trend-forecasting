# Presenter script — aligned to `Weather_Trend_Forecasting.pptx`

**For the submission video (OBS):** use **§ 2-minute demo** below — **7 slides, ~120 seconds**.  
**For a full deck read-through:** use **§ Full deck (slides 1–14)** further down (~8–10 min).

**Repo URL (slide 13):** `https://github.com/ArepaMan/weather-trend-forecasting`  
**Video URL:** add after YouTube upload, then update slide 13.

---

## 2-minute demo (submission video)

**Setup:** PowerPoint **Slide Show** (full screen). OBS records the slideshow only; **mic on**, no camera. Advance with **→** when each block ends.

**Slides you SKIP in this recording:** 2, 3, 6, 9, 10, 12, 14 *(mission, objectives, methodology, feature importance, air quality, limitations, thank-you — they stay in the deck and repo).*

| Time | Go to slide | On screen (quick check) |
|------|-------------|-------------------------|
| 0:00 | **1** | Title, your name, assessment |
| 0:12 | **4** | 167,812 rows, caveats |
| 0:28 | **5** | Kyiv charts (temp + precip) |
| 0:45 | **7** | Metrics table + holdout plot |
| 1:05 | **8** | Anomalies + chart |
| 1:22 | **11** | Country mean temp bars |
| 1:40 | **13** | GitHub + how to reproduce |

---

### 0:00–0:12 — Slide 1 (Title)

**SAY:**  
“Hi, I’m Manuel Vargas Alvarez. This is my PM Accelerator advanced data science assessment on the Kaggle Global Weather Repository—cleaning, forecasting, and advanced analyses on a global city panel.”

**→ Advance to slide 4** *(skip slides 2–3).*

---

### 0:12–0:28 — Slide 4 (Data overview)

**SAY:**  
“After cleaning: 167,812 rows, 41 features, 268 cities. I used Kyiv—863 observations—indexed by `last_updated`. Evaluation is one-step-ahead; observation time drifts from afternoon in twenty-twenty-four toward morning in twenty-twenty-six.”

**→ Advance to slide 5.**

---

### 0:28–0:45 — Slide 5 (EDA — Kyiv)

**SAY:**  
“Kyiv shows clear seasonal temperature swings and spiky precipitation. These plots are from notebook one; precipitation is a reading at observation time, not a daily total.”

*Point at the temperature chart, then precip.*

**→ Advance to slide 7** *(skip slide 6).*

---

### 0:45–1:05 — Slide 7 (Forecasting results)

**SAY:**  
“Eighty-twenty chronological split, no shuffle. Ridge is the best single model—about two point four seven degrees MAE and three point six RMSE on holdout. Naive persistence is close. Seasonal naive is near six degrees MAE because last year’s weather and shifting observation hours don’t match today—not because the three-hundred-sixty-five-row lag misses the calendar. Here’s the holdout forecast plot.”

*Point at Ridge row, then the chart.*

**→ Advance to slide 8.**

---

### 1:05–1:22 — Slide 8 (Anomalies)

**SAY:**  
“Notebook three: nine days flagged with robust z-scores, including a suspicious one-point-one degree snapshot in the test window. Isolation Forest marks eighteen points—the top two percent by design. This chart marks those days on the Kyiv series.”

**→ Advance to slide 11** *(skip slides 9–10).*

---

### 1:22–1:40 — Slide 11 (Geography)

**SAY:**  
“Country means use at least a hundred rows per country—186 countries. Hottest aggregates: Qatar, UAE, Cambodia; coldest include Iceland, Mongolia, and Canada. Mixed-language labels; scrape means, not official climate normals.”

*Point at the bar chart.*

**→ Advance to slide 13** *(skip slide 12).*

---

### 1:40–2:00 — Slide 13 (Repository)

**SAY:**  
“Code and notebooks one through three are on GitHub at ArepaMan slash weather-trend-forecasting. Install requirements, download the Kaggle CSV locally, run top to bottom. Report, slides, and figures are in the repo. Thanks for watching.”

*Optional:* show repo URL on slide if typed; end recording.

---

**End.** Target length **1:50–2:10**. Trim slide 11 or 8 by five seconds if you run long; do not add skipped slides in this take.

---

## Full deck (slides 1–14) — live presentation or long recording

Use **PowerPoint → Presenter View**. Each block lists **what’s on the slide** and **what to say**.

**Recording tip:** do **not** use this section for the 1–2 minute submission; use **§ 2-minute demo** above.

---

## Slide 1 — Title

**On slide:** Weather Trend Forecasting — Global Weather Repository · Manuel Vargas Alvarez · PM Accelerator Advanced DS · Kaggle dataset link.

**SAY:**  
“Hi, I’m Manuel Vargas Alvarez. This is my PM Accelerator advanced data science assessment. I used the Kaggle Global Weather Repository to clean data, explore trends, forecast Kyiv temperature, and run the advanced analyses the track requires.”

---

## Slide 2 — PM Accelerator mission

**On slide:** PMA KIDS quote (free PM education for teenagers from underserved families; educational fairness; diverse tech industry) · link to pmaccelerator.io · line about PM Accelerator community.

**SAY:**  
“The assessment asks us to reflect PM Accelerator’s mission. This PMA KIDS quote is on the slide verbatim—breaking barriers to product education for underserved teens. My analysis is separate technically, but I’m submitting in that spirit of rigorous, reproducible work for product and AI careers.”

---

## Slide 3 — Objective & approach

**On slide:** Goal: forecast weather trends on a real panel dataset. **Advanced track:** cleaning & EDA, anomalies, multiple models + ensemble, feature importance, air quality, geography. **Primary series:** Kyiv · `temperature_celsius` · time index **`last_updated`**.

**SAY:**  
“My goal was end-to-end data science on a global weather panel—not a one-off chart. I hit the advanced checklist: EDA, anomalies, several forecast models plus an ensemble, lag importance, Kyiv air-quality correlations, and country-level temperature patterns. Everything is anchored on Kyiv daily temperature indexed by `last_updated`, as the brief specifies.”

---

## Slide 4 — Data overview

**On slide:** Table: 167,813 → **167,812** rows; 41 columns; 268 cities; ~863 obs/city. Cleaning bullets: datetime parse, no imputation, no scaling, one duplicate key dropped (Nan, Thailand). **Caveats:** one-step-ahead eval; mean gap ~24 h 03 m + **observation-hour drift** (afternoon 2024 → morning 2026); **2026-08-17** 1.1°C snapshot in test window.

**SAY:**  
“After cleaning we have 167,812 rows and 41 features across 268 cities. I parsed `last_updated`, didn’t impute or scale temperatures, and removed one duplicate location-timestamp pair for Nan, Thailand. Important caveats on this slide: evaluation is **one-step-ahead**, Kyiv’s **observation hour drifts** over the series, and there’s a suspicious **1.1 degree** reading on August 17, 2026 that sits in the forecast test period.”

---

## Slide 5 — EDA — Kyiv time series

**On slide:** 863 points, 2024-05-16 to 2026-09-27; median spacing ~24 h; seasonality about −21°C to 37°C; precip at observation time not daily total. **Images:** `kyiv_temperature.png`, `kyiv_precipitation.png`.

**SAY:**  
“Kyiv has 863 daily-ish observations with clear seasonal temperature swings and spiky precipitation. Precipitation here is a scrape reading at observation time—not a daily total—so I used it for exploration and anomalies, not as a forecast target. These two charts are the Kyiv temperature and precipitation series from notebook one.”

*Gesture at the charts on slide.*

---

## Slide 6 — Forecasting methodology

**On slide:** 80/20 **chronological** split; models: naive, seasonal naive (365-row lag), Ridge lags [1,2,3,7,14,28], ensemble = mean of Ridge + naive (lowest MAE pair). Metrics MAE & RMSE in °C.

**SAY:**  
“Forecasting uses a strict time-ordered split—eighty percent train, twenty percent test, no shuffle. I compared persistence, a three-hundred-sixty-five-row seasonal naive, Ridge on multiple lags, and a simple ensemble averaging Ridge and naive because they had the lowest holdout MAE. All metrics are MAE and RMSE in degrees Celsius.”

---

## Slide 7 — Forecasting results

**On slide:** Results table — Naive 2.509 / 3.753; Seasonal 5.993 / 7.528; **Ridge 2.474 / 3.599**; Ensemble 2.474 / 3.653. Takeaways: Ridge best **single** model on RMSE; seasonal naive loses to weather variability + hour drift; ensemble ties Ridge on MAE. **Image:** `kyiv_forecast_holdout.png`.

**SAY:**  
“On holdout, Ridge is the best single model at about **2.47 degrees MAE** and **3.60 RMSE**—only a small edge over naive persistence. Seasonal naive is much worse at about six degrees MAE—not because the three-hundred-sixty-five-row lag misses the calendar, but because last year’s weather differs from today and observation hours drift. The ensemble ties Ridge on MAE; I still treat Ridge as primary because its RMSE is lower. This plot shows actual versus forecast on the test window.”

*Point at table row for Ridge, then the chart.*

---

## Slide 8 — Anomaly detection

**On slide:** Robust z (30-day median/MAD, threshold 3.5): **9** days. Example **2026-08-17** in test window; 2024-05-16 flags = window edge at series start. Isolation Forest: **18** points, top 2% by construction. **Image:** `kyiv_anomalies.png`.

**SAY:**  
“Notebook three flags nine days with robust z-scores above three and a half, including that August 2026 snapshot I mentioned. The early May 2024 flags are mostly edge effects of the rolling window. Isolation Forest marks eighteen multivariate points—the top two percent by design—so the counts differ by method. Here’s the temperature series with anomalies marked.”

---

## Slide 9 — Feature importance

**On slide:** Ridge lag coefficients — **lag_1 ~0.796**, **lag_7 ~0.093**, smaller 3 and 14; lag_2 / lag_28 ≈ 0 because lag_2 correlates with lag_1 (~0.96). **Image:** `kyiv_lag_importance.png`.

**SAY:**  
“Lag one dominates—yesterday’s temperature predicts tomorrow. Lag seven adds a modest weekly signal. Near-zero weight on lag two is mostly collinearity with lag one, not a magic regularization story. That matches why naive persistence almost matches Ridge in the holdout.”

---

## Slide 10 — Environmental analysis (Kyiv)

**On slide:** Correlations (not causation): temp–ozone **+0.54**, humidity–ozone **−0.49**, pressure–SO₂ / NO₂ ~+0.36–0.37. **Image:** `kyiv_air_quality_corr.png`.

**SAY:**  
“For Kyiv I correlated weather fields with air-quality columns. The strongest patterns are temperature with ozone and humidity with ozone—association in this scrape, not proven causation. The heatmap on the slide summarizes those relationships.”

---

## Slide 11 — Geographical patterns

**On slide:** Mean temp by country, **≥100 rows** (186 countries). **Highest:** Qatar, UAE, Cambodia ~32–33°C. **Lowest:** Iceland, Mongolia, Canada ~5.7–6.6°C. Mixed-language labels; scrape aggregates not climate normals. **Image:** `country_mean_temperature.png`.

**SAY:**  
“Country means filter out sparse countries with fewer than a hundred rows—186 countries remain. Hottest aggregates are Qatar, UAE, and Cambodia; coldest include Iceland, Mongolia, and Canada. Labels mix languages; these are means over the repository snapshots, not official climate normals.”

---

## Slide 12 — Limitations & next steps

**On slide:** Single-city depth vs global model; **observation-hour drift** biases year-over-year baselines; correlations not causation; next steps: calendar/hour features, country-name normalization, precip modeling.

**SAY:**  
“Limitations are on the slide: I went deep on one city, hour-of-day drift affects comparisons, and correlation isn’t causation. Next steps would be proper calendar features, cleaner geography labels, and precipitation models suited to zero inflation.”

---

## Slide 13 — Repository & reproduction

**On slide:** GitHub URL · pip install + notebooks 01→03 · Kaggle CSV path · demo video URL placeholder.

**SAY:**  
“Everything reproduces from GitHub—notebooks one through three, pinned requirements, README instructions. Reviewers download the Kaggle CSV locally; it’s not in the repo. My demo video link is on this slide once uploaded; the code is at github.com/ArepaMan/weather-trend-forecasting.”

*If video URL is still blank, say: “Video link will be in the submission form.”*

---

## Slide 14 — Thank you

**On slide:** Questions? · contact / LinkedIn placeholders.

**SAY:**  
“That’s the project—clean panel data, honest baselines, Ridge near persistence, and advanced anomaly, environmental, and geo views. Thanks for watching; happy to take questions.”

---

## If your deck differs slightly

Compare slide **titles** in PowerPoint to the headings above. If a title moved, keep the **SAY** text with the slide that shows the same **bullets or figure**.
