# Data

Do not commit large CSV files to git.

1. Create folder: `data/raw/`
2. Download from Kaggle: [Global Weather Repository](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository)
3. Expected file name (adjust in notebooks if different): `Global Weather Repository.csv`

### Kaggle CLI (optional)

```powershell
pip install kaggle
# Place kaggle.json in %USERPROFILE%\.kaggle\
kaggle datasets download -d nelgiriyewithana/global-weather-repository -p data/raw --unzip
```
