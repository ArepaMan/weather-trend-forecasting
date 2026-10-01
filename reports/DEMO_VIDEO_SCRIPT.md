# Demo video script (~90 seconds)

Read while screen-recording: notebook overview → one plot → metrics cell → GitHub README.

---

**[0:00–0:15] Intro**

“Hi, I’m Manuel Vargas Alvarez. This is my PM Accelerator advanced data science assessment on the Kaggle Global Weather Repository—167 thousand rows, 268 cities, and 41 weather and air-quality features.”

**[0:15–0:35] Cleaning & EDA**

“In notebook one I parsed `last_updated`, removed one duplicate timestamp for Nan Thailand, and focused EDA on Kyiv—863 daily observations with clear seasonal temperature and spiky precipitation. Here are the time-series plots in `reports/`.”

*Show `kyiv_temperature.png` or live notebook plot.*

**[0:35–0:55] Forecasting**

“In notebook two I used an eighty-twenty chronological split—no shuffle. Ridge on lag features beat naive and seasonal baselines with about two point five degrees MAE on holdout; seasonal naive failed because last year’s weather on the same calendar date still differs from today, and Kyiv’s observation hour drifted from afternoon to morning—not because the three-hundred-sixty-five-row lag misses the calendar. I also averaged Ridge and naive for a simple ensemble.”

*Show metrics output or `kyiv_forecast_holdout.png`.*

**[0:55–1:15] Advanced**

“Notebook three adds robust-z anomalies, Isolation Forest outliers, lag importance, air-quality correlations—temperature and ozone around point five four—and country mean temperature bars. Code and README are on GitHub; data comes from Kaggle.”

*Flash `kyiv_air_quality_corr.png` or anomaly plot.*

**[1:15–1:30] Close**

“This project supports PM Accelerator’s mission of building analytical rigor for product and AI careers. Thanks for watching—repo link in the submission form.”

*Optional: show PM Accelerator mission slide from `SLIDES.md`.*
