# Drifting or Pivoting? The Algorithmic and Social Penalties of Category Spanning in the Creator Economy

> **Work in progress:** This project is actively being developed. Results, code, and documentation are not finalized and may change.

This repository contains a machine learning pipeline for studying category spanning behavior in the creator economy and its association with engagement outcomes.

## Background

This project is motivated by the categorical imperative and illegitimacy discount in organizational sociology: actors that do not fit cleanly into recognized categories are often harder for audiences and intermediaries to evaluate and may be penalized. In traditional markets, this pattern has been documented for category-spanning firms and cultural products.

The creator economy introduces a different setting. On platforms like YouTube, creators are evaluated by both social audiences (viewers/subscribers) and algorithmic recommenders (ranking and distribution systems). This creates a strategic tension: creators are often encouraged to maintain a consistent niche identity, but may also benefit from exploring adjacent or new categories.

## Research Focus

The core question is whether category spanning is associated with video underperformance relative to a creator's own recent baseline, that is, whether cross-category movement looks more like drifting (penalty) or pivoting (adaptation).

Following the Stage 2 design, the empirical setup uses:

- Unit of analysis: individual video upload
- Target: `underperform_flag` (1 if a video's log-engagement falls below the channel's rolling historical baseline; 0 otherwise)
- Key explanatory behavior: `category_switch`, defined with a stricter rule where the current category differs from the previous category after a stable prior run

This framing treats the project as a time-aware supervised classification problem rather than a static cross-sectional correlation.

## Data Context

The analysis is built on the YouNiverse dataset (Ribeiro and West, 2021), using English-language YouTube video and channel metadata in longitudinal format. The staged pipeline constructs cleaned and feature-engineered datasets from large raw JSONL/TSV sources into panel-ready and ML-ready tables for modeling.

## Project Layout

```text
category-spanning-penalty/
├── README.md
├── 0_data/
│   ├── yt_metadata_en.jsonl.gz
│   ├── df_channels_en.tsv.gz
│   ├── random_sampled_10000.csv
│   ├── research_ready_videos.csv
│   ├── stage2_panel_ready.csv
│   └── stage2_ml_ready.csv
├── 1_preprocessing/
│   ├── 1_data_preparation.ipynb
│   └── 2_preprocessing.ipynb
├── 2_modeling/
│   └── 1_modeling.ipynb
└── 3_outputs/
```

## Data Files (Current Workspace)

| File | Approx. Size | Rows | Purpose |
|---|---:|---:|---|
| `0_data/yt_metadata_en.jsonl.gz` | 13G | raw compressed JSONL | Base YouTube metadata source |
| `0_data/df_channels_en.tsv.gz` | 5.7M | compressed TSV | Channel-level metadata |
| `0_data/random_sampled_10000.csv` | 1.8G | 5,466,744 | Intermediate sampled/flattened video data |
| `0_data/research_ready_videos.csv` | 1.5G | 4,146,993 | Cleaned research-ready dataset |
| `0_data/stage2_panel_ready.csv` | 1.2G | 4,133,481 | Panel-format feature set |
| `0_data/stage2_ml_ready.csv` | 1.4G | 4,094,101 | ML-ready feature set with split labels |

## Workflow Overview

Run notebooks in this order:

1. `1_preprocessing/1_data_preparation.ipynb`
	- Reads raw sources such as `0_data/yt_metadata_en.jsonl.gz` and `0_data/df_channels_en.tsv.gz`.
	- Produces:
	- `0_data/random_sampled_10000.csv`
	- `0_data/research_ready_videos.csv`

2. `1_preprocessing/2_preprocessing.ipynb`
	- Reads `0_data/research_ready_videos.csv` (+ channel metadata).
	- Engineers panel and time-aware features (examples: `category_switch`, lag features, rolling statistics, upload cadence features, `underperform_flag`, `split`).
	- Saves `0_data/stage2_ml_ready.csv`.
	- The repository also includes `0_data/stage2_panel_ready.csv` as a panel artifact.

3. `2_modeling/1_modeling.ipynb`
	- Currently reads `0_data/research_ready_videos.csv`.
	- Builds baseline models using scikit-learn (Linear Regression, Random Forest Regressor, Linear SVR) and reports metrics (R2, RMSE).

## Environment

Recommended setup:

1. Python 3.10+
2. JupyterLab or VS Code notebooks
3. Core packages:
	- `pandas`
	- `numpy`
	- `matplotlib`
	- `scikit-learn`
	- `plotnine`

Example installation:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -U pip
pip install pandas numpy matplotlib scikit-learn plotnine jupyter
```

## How To Run

From the repository root:

```bash
cd /Users/norazhan/Desktop/Y1T2/ppol5204_ds/project/category-spanning-penalty
jupyter lab
```

Then execute notebooks sequentially:

1. `1_preprocessing/1_data_preparation.ipynb`
2. `1_preprocessing/2_preprocessing.ipynb`
3. `2_modeling/1_modeling.ipynb`

## Outputs

- `3_outputs/` is currently empty and can be used for exported figures, model summaries, and final tables.
- Main generated tabular outputs are stored in `0_data/`.

## Notes

- Data files are large; expect high RAM and storage usage.
- If you rerun the preprocessing notebooks, generated CSV row counts may differ depending on filtering and missingness handling.
- The modeling notebook currently uses `research_ready_videos.csv`; you may adapt it to consume `stage2_ml_ready.csv` if you want to use engineered features and time-aware splits directly.
