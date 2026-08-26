# MCD Experimental Results

This directory contains the complete set of experimental results from training and evaluating LSTM models on Mars Climate Database (MCD) 6.1 data. Results include individual run artifacts, aggregated metrics, and capacity-sweep analysis.

## Directory structure

### `runs/`

Individual experiment runs, each in a timestamped directory (e.g., `20260622T011644452184_s013`). Each run contains:
- `logs/`: detailed experiment artifacts
  - `run_*_training_curve.csv`: epoch-by-epoch training/validation metrics
  - `run_*_model_comparison.csv`: performance metrics across model sizes
  - `run_*_regime_metrics.csv`: temporal regime breakdown (day/night, seasonal)
  - `run_*_conformal.json`: conformal prediction calibration results
  - `run_*_paper_table.csv`: publication-ready results table
  - `run_*_split.json`: data split details and random seeds
  - `run_*_acceptance.json`: acceptance test results
  - `run_*_unit_corrections.json`: unit conversion verification

### `aggregate/`

Aggregated results across all runs (10 random seeds):
- `all_mcd_runs_metrics.csv`: full metrics for every run and model size
- `capacity_summary_from_runs.csv`: summary statistics (mean, std) of performance vs. model capacity
- `session_map.csv`: metadata mapping runs to configurations

### `capacity_sweep/`

Analysis of LSTM capacity (hidden unit) scaling:
- `comparison.csv` & `comparison.md`: side-by-side comparison of models (64 to 2048 units)
- `rmse_chart.png`: visualization of RMSE vs. model capacity
- `dataset_details.md` & `dataset_details.json`: experiment configuration and data characteristics
- `session_index.csv`: index of sessions included in the sweep

## Key results

Baseline 64-unit model: **1335.1 m RMSE** (R² = 0.9342)  
Best model (1536 units): **876.1 m RMSE** (R² = 0.9716)  
Improvement: **34.4% RMSE reduction**

See the repository root `README.md` for full research context, methods, baselines, and detailed results.

## Using these results

- **Compare model capacities**: See `capacity_sweep/comparison.csv` for side-by-side metrics
- **Analyze specific runs**: Navigate to `runs/<timestamp>/logs/` for individual run details
- **Reproduce results**: Each run's `split.json` records the exact data split and random seed
- **Publication tables**: `runs/*/logs/run_*_paper_table.csv` provides ready-to-cite results
