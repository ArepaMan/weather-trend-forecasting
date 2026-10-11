# Portfolio site integration

The case study lives in the [portfolio](https://github.com/ArepaMan/portfolio) repo:

- `content/projects/weather-trend-forecasting.mdx`
- `content/projects/weather-databricks-pipeline.mdx` (companion pipeline case study)
- Thumbnails and gallery PNGs under `public/images/projects/weather-*.png` and `weather-databricks-*.png`

## Refresh figures after re-running notebooks

From your local portfolio clone:

```powershell
cd C:\Users\manol\Projects\portfolio
npm run sync:weather
```

Default source path is `%USERPROFILE%\Projects\weather-trend-forecasting`. Override with:

```powershell
$env:WEATHER_REPO="C:\path\to\weather-trend-forecasting"
npm run sync:weather
```

## What to commit where

| Change | Repo |
|--------|------|
| Notebooks, `reports/*.png`, PDF, slides | **weather-trend-forecasting** |
| Case study copy, synced gallery PNGs | **portfolio** |

Portfolio front matter includes `demoUrl: https://youtu.be/1ZwhvgLowmE` (update slide 13 in the deck if it still shows a placeholder).
