# 🗺️ Morocco Labour Market — Spatial Data Pipeline

> **How unequal is labour force participation between men and women across Morocco's 1,500+ communes — and can we map it?**

This repository builds the analysis-ready spatial dataset for a commune-level study of gender gaps in labour market activity in Morocco, using the 2024 HCP Census. It handles raw boundary files, multi-strategy name matching, gap filling via GADM, and exports a clean GeoJSON ready for spatial econometric modelling.

---

## 📸 Preview

| Commune Coverage | Activity Gap Distribution | Urban vs Rural Split |
|:---:|:---:|:---:|
| *(figures/01_coverage_map.png)* | *(figures/02_activity_gap_hist.png)* | *(figures/03_urban_rural.png)* |

> Figures are generated automatically when you run the script.

---

## 🎯 Objective

Morocco's 2024 Census records labour market indicators at the commune level for the first time at this granularity. The raw data comes in two separate files — a shapefile of commune boundaries (HCP) and a tabular census extract — which do not share a clean common identifier. This project solves that matching problem using a three-stage strategy and produces a single, analysis-ready spatial file covering ~1,500 communes.

---

## 📦 Dataset

| File | Source | Unit | Coverage | Key Variables |
|---|---|---|---|---|
| `2024_Census.xlsx` | HCP Morocco (2024 Census) | Commune | ~1,500 communes | Activity rates, unemployment, illiteracy, education, infrastructure by sex |
| `populaion_commune.zip` | HCP Morocco | Commune polygon | Full Morocco | Commune boundaries (shapefile) |
| `gadm41_MAR_4.shp` | GADM 4.1 (auto-downloaded) | Commune | Full Morocco | Used for spatial gap-filling only |

> Raw data files are not included in this repository. Place them in `data/raw/` before running.

---

## 🔬 Methodology

The pipeline runs as a single numbered script with clearly labelled steps:

**Script 01 — `01_data_cleaning.py`**

| Step | Description |
|---|---|
| 1 | Load Census Excel and HCP shapefile |
| 2 | Clean HCP commune names (strip prefixes, fix encoding) |
| 3 | Direct name merge: HCP ↔ Census (~90%+ match rate) |
| 4 | Download GADM Level-4 boundaries for Morocco |
| 5 | Centroid-based spatial join for unmatched polygons |
| 6 | Intersects fallback for remaining gaps |
| 7 | Province-median imputation for last residuals |
| 8 | Remove Western Sahara communes (out of scope) |
| 9 | Compute gender gap variables (activity, unemployment, illiteracy) |
| 10 | Rename all variables to English short names |
| 11 | Build Queen contiguity spatial weights matrix |
| 12 | Export GeoJSON, CSV, weights pickle, and codebook |

---

## 📊 Key Output Variables

| Variable | Definition |
|---|---|
| `activity_gap` | Male − Female labour force participation rate (percentage points) |
| `unemployment_gap` | Male − Female unemployment rate (pp) |
| `illiteracy_gap` | Male − Female illiteracy rate (pp) |
| `activity_rate` | Overall activity rate (both sexes) |
| `urban` | Urban (1) / Rural (0) binary |
| `pop_total` | Total municipal population |
| `log_pop` | Log of total population |
| `electricity` | % households with electricity access |
| `water` | % households with running water |
| `distance_to_road` | Average distance to paved road (km) |
| `higher_edu` | % population with higher education |
| `married_pct` | % population aged 15+ who are married |

Full variable definitions in `outputs/CODEBOOK.csv`.

---

## 📁 Folder Structure

```
morocco-labour-cleaning/
├── scripts/
│   └── 01_data_cleaning.py       ← Main pipeline
├── data/
│   ├── raw/                      ← Input files (not tracked by git)
│   │   ├── 2024_Census.xlsx
│   │   └── populaion_commune.zip
│   └── processed/                ← Generated outputs
│       ├── FINAL_MOROCCO_2024.geojson
│       ├── FINAL_MOROCCO_2024.csv
│       └── spatial_weights_queen.pkl
├── figures/                      ← Auto-generated maps & plots
├── outputs/
│   └── CODEBOOK.csv
├── README.md
└── .gitignore
```

---

## ▶️ How to Reproduce

### Requirements

```bash
pip install geopandas libpysal esda spreg pandas numpy matplotlib openpyxl requests
```

### Steps

1. Place `2024_Census.xlsx` and `populaion_commune.zip` in `data/raw/`
2. Run the script:

```bash
cd morocco-labour-cleaning
python scripts/01_data_cleaning.py
```

The script auto-detects whether you are running locally or in Google Colab and sets paths accordingly. GADM boundaries are downloaded automatically on first run and cached locally.

---

## 🛠️ Tools & Packages

| Tool | Version | Purpose |
|---|---|---|
| Python | 3.10+ | Core language |
| GeoPandas | ≥0.13 | Spatial data handling |
| libpysal | ≥4.7 | Spatial weights matrix |
| pandas | ≥2.0 | Data wrangling |
| NumPy | ≥1.24 | Numerical operations |
| requests | — | GADM auto-download |
| matplotlib | ≥3.7 | Diagnostic plots |

---

## 🔮 Future Work

- Add Script 02 for descriptive spatial analysis (choropleth maps, Moran's I)
- Link to modelling repository (`morocco-labour-spatial`) for OLS, Fractional Logit, SAR, and SDM estimation
- Explore panel extension if sub-national time series become available from HCP

---

## 👩‍💻 Author

**Majda El Oumami**
GitHub: [github.com/majda-eloumami](https://github.com/majda-eloumami)
Thesis project — Spatial Econometrics, 2024–2025
