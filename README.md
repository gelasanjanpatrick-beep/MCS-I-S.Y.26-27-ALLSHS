# Year-Ahead GHI Forecasting and Mapping for the Philippines: Code and Data

Code and data for the study *"Development and Evaluation of a Physics-Informed Hybrid SARIMAX-LSTM Model Using Clear-Sky Index-Informed Learning for Year-Ahead Global Horizontal Irradiance Forecasting and Spatial Mapping in the Philippines"*

The study forecasts monthly GHI one year ahead for 93 grid cells (1° × 1°) covering the Philippines. It uses NASA POWER monthly data from 2005–2025 and produces a forecast for 2026.

## Contents

| Location | Contents |
|---|---|
| `*.ipynb` | Jupyter notebooks, one or more per procedure (see the run order below) |
| `data/raw/` | NASA POWER monthly data and SRTM elevation as downloaded |
| `data/processed/` | spatial grid, validated datasets, Ineichen-Perez clear-sky table, data-quality results |
| `outputs/` | forecasts, metrics, trained models, significance tests and the 2026 forecast |
| `outputs/figures/` | all figures, as PNG (300 dpi), SVG and PDF |
| `outputs/maps/` | maps of the spatial grid, as PNG (300 dpi) |
| `requirements.txt` | Python libraries with the exact versions used |

## Reproducing the study

1. Install Python 3.14 and the libraries:
   ```
   pip install -r requirements.txt
   ```
2. Open this folder in Jupyter or VS Code. Run each notebook from top to bottom (**Run All**) in this order:

| Order | Procedure | Notebook | Main outputs |
|---|---|---|---|
| 1 | Data acquisition | `Philippine Map Create_Grid.ipynb` | `data/processed/grid_metadata.csv`, `data/processed/philippines_grid.geojson` |
| 2 | Data acquisition | `Data_Extraction_NASAPOWER.ipynb` | `data/raw/nasa_power/nasa_power_monthly.csv` |
| 3 | Data acquisition | `Elevation_Extraction.ipynb` | `data/raw/elevation/grid_elevation.csv` |
| 4 | Data pre-processing | `Data_Preprocessing_Validation.ipynb` | `data/processed/nasa_power_validated.csv`, `data/processed/quality/` |
| 5 | Standalone models and rolling validation | `Standalone_Models.ipynb` | `outputs/standalone_models/` |
| 6 | Final model testing (standalone) | `Final_Testing_Standalone.ipynb` | `outputs/final_test/` |
| 7 | Hybrid models (+ clear-sky index, + penalized loss) and rolling validation | `Hybrid_Models.ipynb` | `outputs/hybrid_models/`, `data/processed/clearsky_ineichen_monthly.csv` |
| 8 | Final model testing (hybrid) | `Final_Testing_Hybrid.ipynb` | `outputs/final_test_hybrid/` |
| 9 | Spatial analysis and mapping | `Final_Test_Visualizations.ipynb`, `Philippine Map_Grid.ipynb` | `outputs/figures/final_test_*`, `outputs/maps/` |
| 10 | Comparative and ablation analysis | `Comparative_Ablation_Analysis.ipynb` | `outputs/significance_tests/`, `outputs/figures/rmse_difference_maps_full_record.*` |
| 11 | Final model training and 2026 forecast | `Final_Product_2026.ipynb` | `outputs/final_product_2026/`, 2026 maps in `outputs/figures/` |
| – | Workflow charts | `Workflow_Charts.ipynb` | `outputs/figures/*_workflow.*` |
| – | List of links to every file | `Reproducibility_Links.ipynb` | `reproducibility_links.html` |

**To reproduce the reported numbers exactly**, start at step 4 and use the data files included here. Steps 2 and 3 download the data again from NASA POWER and OpenTopoData, and the providers occasionally revise their data. Step 11 uses the included `data/raw/nasa_power/nasa_power_monthly_2005_2025.csv` and downloads again only if that file is missing or `REFRESH_EXTRACTION = True`. `outputs/final_product_2026/nasa_power_revisions_vs_development.csv` records whether NASA POWER revised any 2005–2024 value between the two downloads (none had at the time of the study).

## Data sources

- **NASA POWER** (monthly GHI `ALLSKY_SFC_SW_DWN`, cloud amount `CLOUD_AMT`, relative humidity `RH2M`): *"The data was obtained from the National Aeronautics and Space Administration (NASA) Langley Research Center (LaRC) Prediction of Worldwide Energy Resource (POWER) Project funded through the NASA Earth Science/Applied Science Program."* https://power.larc.nasa.gov
- **Elevation:** NASA SRTM GL1 v003 (30 m), accessed through OpenTopoData (https://www.opentopodata.org). One SRTM void (grid 2) was filled with NASA/METI ASTER GDEM v3 (30 m). The `elevation_source` column records the dataset of each value.
- **Philippine boundary shapefile (`PH_Adm0_Country`)**, not included because of its size (198 MB, above GitHub's 100 MB file limit): *[add the source and link]*. Download it and save it as `data/boundaries/Philippines.shp/PH_Adm0_Country.shp.shp`. It is needed only for step 1 and for the map cells of steps 9, 10 and 11. Every other step runs without it, because the grid it produced is included (`data/processed/grid_metadata.csv`, `data/processed/philippines_grid.geojson`).

## Reproducibility notes

- Random seed 42 for Python, NumPy and PyTorch, with PyTorch's deterministic algorithms enabled.
- SARIMAX, climatology, seasonal naive and the statistical tests are exactly repeatable. LSTM and XGBoost results may differ in the last decimals on different hardware or library versions.
- Each model output folder (`standalone_models`, `final_test`, `hybrid_models`, `final_test_hybrid`, `final_product_2026`) contains an `environment.txt` with the library versions used for that run.
- Run on Windows 11 with Python 3.14.7, an Intel Core i5-13420H CPU, 7.7 GB of RAM and no GPU.
- On a computer with 8 GB of RAM or less, a notebook may stop with a memory error. Set `N_JOBS = 4` in its settings cell. This only changes how many grids are fitted at the same time, not the results.

## Citation

*[Authors]. ([Year]). Development and Evaluation of a Physics-Informed Hybrid SARIMAX-LSTM Model Using Clear-Sky Index-Informed Learning for Year-Ahead Global Horizontal Irradiance Forecasting and Spatial Mapping in the Philippines. [Institution].*

## License

*[Choose a license, e.g. MIT for the code and CC BY 4.0 for the data outputs.]*
