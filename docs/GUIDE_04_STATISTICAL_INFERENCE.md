# Guide — `04_statistical_inference.ipynb` (DA application add-on)

You write the code; use this as the checklist. Run from repo root with kernel **weather-assessment**. Reuse the same load/dedupe/Kyiv filter pattern as notebooks **02** and **03**.

**Suggested order:** setup → §1 winter test → §2 CI → §3 OLS → §4 time-series diagnostics → short markdown conclusions.

**Completion checklist (notebook saved with outputs):**

- [x] §1 — DJF winters, hour overlap, Welch + `temperature_celsius ~ C(winter) + C(obs_hour)`
- [x] §2 — Option B: bootstrap CI for Ridge holdout MAE (B≈5000)
- [x] §3 — Weather covariate OLS (`statsmodels`)
- [x] §4 — ADF, ACF/PACF, Ljung–Box on `y_test - pred_ridge`
- [x] `requirements.txt` — `scipy`, `statsmodels` pinned
- [x] `README.md` / `reports/REPORT.md` — short nb04 blurb

---

## Setup cell (copy pattern from nb02)

- `DATA_PATH = Path("../data/raw/GlobalWeatherRepository.csv")`
- Parse `last_updated`, dedupe on `["location_name", "last_updated"]`
- `kyiv` = sorted Kyiv rows; add `obs_hour = kyiv["last_updated"].dt.hour`
- For AQ sections, keep `weather_cols` / `air_quality_*` columns like nb03

---

## 1. Hypothesis test — winter 2024–25 vs 2025–26 (control observation hour)

**Question:** Is mean temperature different between the two winters, after accounting for **hour-of-day** drift?

**Define winters (meteorological DJF):**

| Label | Months included |
|--------|------------------|
| `2024-25` | Dec **2024**, Jan–Feb **2025** |
| `2025-26` | Dec **2025**, Jan–Feb **2026** |

Filter `kyiv` to those months; add column `winter` (string label).

**Important:** Your EDA already noted 2025–26 looks colder partly because obs hour shifted earlier. Do **not** only run a raw two-sample test without comment.

**Recommended approach (pick one primary; show the other as sensitivity):**

### A. Hour-stratified comparison (simple, easy to explain)

- Restrict to **hours that appear in both winters** (e.g. `obs_hour` in 8–12, or intersect of unique hours per winter).
- On that subset: **Welch t-test** (`scipy.stats.ttest_ind(..., equal_var=False)`) and **Mann–Whitney U** (`mannwhitneyu`, two-sided).
- Report n per winter, means, test statistic, p-value.
- One markdown cell: if unadjusted test differs from adjusted, say why (hour drift).

### B. Regression adjustment (stronger “control for hour” story)

```text
temperature_celsius ~ C(winter) + C(obs_hour)
```

- `statsmodels.formula.api.ols` on the DJF subset.
- Test `winter` coefficient (F-test or t-test on `C(winter)[T.2025-26]` or similar).
- Interpret: difference **after** fixing hour effects.

**Deliverable in notebook:** table of group sizes, means (raw and/or adjusted), test names, p-values, 2–3 sentence interpretation (significant or not; link to hour drift caveat).

---

## 2. Confidence interval — temp ↔ ozone **or** bootstrap MAE

Pick **one** (temp–ozone fits nb03; bootstrap MAE ties to nb02).

### Option A — CI for Pearson r (temp vs `air_quality_Ozone`)

- Kyiv rows with both columns non-null.
- Point estimate: `r` (same ~0.54 as nb03).
- **Bootstrap** (recommended): resample rows with replacement B=2000–5000; each replicate compute `r`; report 2.5% and 97.5% percentiles.
- Or **Fisher z** CI if you prefer closed form (`numpy`/`scipy` — document formula in markdown).

### Option B — Bootstrap CI for holdout **MAE** (Ridge)

- Reuse nb02: same `split_idx`, lag matrix, `Ridge` fit on train, predict test.
- Bootstrap **test rows** (or residuals): B replicates of MAE; percentile CI.
- State clearly: CI is for **one-step-ahead MAE on this single holdout split**, not full uncertainty of the data-generating process.

---

## 3. OLS regression (`statsmodels`)

**Model (example — adjust if multicollinearity is bad):**

```text
temperature_celsius ~ humidity + pressure_mb + wind_kph + precip_mm
```

- Kyiv only; drop rows with missing predictors.
- `sm.OLS(y, X)` with constant (`sm.add_constant`) **or** `ols("...", data=df).fit()`.
- Print `summary()` or table: coef, std err, **p-values**, **R²**, adj. R².
- Short interpretation: which predictors significant at α=0.05; sign direction; weak/strong fit.

---

## 4. Time-series diagnostics

On **Kyiv `temperature_celsius`** (sorted series):

1. **ADF** — `statsmodels.tsa.stattools.adf` on the level series; report test stat, p-value, conclusion (stationary or not at usual α=0.05).
2. **ACF / PACF** — `plot_acf`, `plot_pacf` (e.g. lags 0–40); save optional figure to `reports/` if you want.
3. **Ljung–Box on Ridge residuals** — fit Ridge on train like nb02; residuals = `y_test - y_pred` on holdout (or in-sample one-step errors if you document it). `acf.ljungbox(residuals, lags=[...])` — state whether residual autocorrelation remains.

**Narrative:** Non-stationary level + significant ACF at low lags motivates lag features; Ljung–Box on residuals shows whether Ridge left structure on the test window.

---

## Dependencies

Pinned in `requirements.txt`:

```text
scipy==1.18.1
statsmodels==0.15.0
```

Fresh install:

```powershell
pip install -r requirements.txt
```

---

## README / report blurb

Documented in `README.md` (results table, layout, run order, Methods) and **`reports/REPORT.md`** § Statistical inference (notebook 04). Keep numbers in sync if you re-run the notebook.

---

## Git (you run)

1. Work on a branch, e.g. `feature/nb04-statistical-inference`.
2. Commit: notebook, `requirements.txt`, README/report edits, any new figures under `reports/`.
3. Push, open PR or merge to `master`, push GitHub.
4. Re-run **Restart & Run All** on 04 before commit so outputs are saved.

---

## When stuck

Paste: section number, error traceback, and what you expected. Reference this guide’s section so we can debug fast.
