# Project Summary: LSTM-Based Prediction of Mars Planetary Boundary Layer Height

This file summarizes the research paper associated with this repository, for use as context when writing or verifying the README and other repo documentation. It is not part of the paper itself.

## Paper

**Title:** LSTM-Based Prediction of Mars Planetary Boundary Layer Height from the Mars Climate Database with Calibrated Uncertainty

**Authors:** James T. Bible, MD Kamruzzaman Sarker — Department of Computer Science, Bowie State University, Bowie, Maryland, USA


## Problem

Predicts Mars planetary boundary layer height (PBLH) — a scalar regression target reported in meters — from short temporal sequences of modeled atmospheric state variables, using the Mars Climate Database (MCD) as the data source. To the best of the authors' knowledge, this is the first published study to apply LSTM-based deep learning directly to Mars PBLH prediction; prior Mars PBL work relies on physics-based modeling and large-eddy simulation, while existing PBLH machine-learning studies are Earth-focused.

## Data source

- **Mars Climate Database (MCD) version 6.1**, sampled over:
  - Latitude: −60° to 60° in 10° increments
  - Longitude: −60° to 60° in 10° increments
  - Solar longitude (Ls, seasonal coverage): 0° to 345° in 15° increments
  - Local time (diurnal coverage): 0 to 21 h in 3 h increments
  - Altitude: 26 levels, 0 to 25,000 m in 1,000 m increments
- A single MCD scenario and perturbation setting was used consistently across all runs.
- Total supervised samples: **6,354**, from an approximately 20% sampling fraction of 192 raw Ls/local-time slices (188 usable target-time indices after sequence construction).

## Inputs and target

- **Target:** PBLH (meters), standardized during training and converted back to physical units for reporting.
- **Input features ("intersection-minimal" configuration):** 3 vertical-profile variables — temperature (`temp`), zonal wind (`u`), meridional wind (`v`) — sampled at 26 altitude levels (3 × 26 = 78 features), plus 2 surface variables — surface temperature (`Tsurf`), surface pressure (`ps`) — for **80 input features per time step**.
- **Sequence formulation:** sequence-to-one regression. Sequence length 4, stride 1 → each supervised example has shape `[4, 80]`, predicting a single PBLH value at the following time step.

## Evaluation design

- **Blocked-time split**, not random partitioning — held-out test region defined by solar longitude (Ls), not by lat/lon, to prevent leakage from nearby seasonal/diurnal states.
- Protected holdout region around **Ls = 90° and 105°**, with a safety gap around the block.
- **10 random seeds**: 0, 1, 2, 3, 4, 5, 7, 13, 41, 42. All paper-level results are 10-seed means.

## Model

- **Architecture:** unidirectional single-layer LSTM (Hochreiter & Schmidhuber, 1997) + regression head.
- **Baseline config:** sequence length 4, hidden size 64, batch size 64, learning rate 0.001, up to 60 epochs, Adam optimizer, MSE loss, dropout 0.10 (applied after the temporal encoder), early stopping (patience 10), learning-rate reduction on plateau. No recurrent dropout, no bidirectionality, no attention, no physics-derived auxiliary features, no nonlinear target transform beyond standardization.
- **Hidden-unit capacity sweep:** 14 sizes — 4, 8, 16, 32, 64, 96, 128, 256, 384, 512, 768, 1024, 1536, 2048 — all other settings held fixed.

## Baselines compared against

1. **Global-average** — training-set mean PBLH for every test sample.
2. **Similar-case average** — training-set mean PBLH from samples with similar (lat, lon, Ls-bin [30°], local-time-bin [2 h]); falls back to location average, then global average, if no match.
3. **Previous-value** — predicts the last available PBLH at the same (lat, lon).

## Key results

| Model | RMSE (m) | R² | nRMSE (%) | Train time (s) |
|---|---|---|---|---|
| 64-unit baseline | 1335.1 | 0.9342 | 13.7 | 17.4 |
| **1536-unit (best)** | **876.1** | **0.9716** | **9.0** | 33.7 |
| 2048-unit | 881.4 | 0.9713 | 9.0 | 39.0 |
| 4-unit (smallest) | 1679.7 | 0.8942 | 17.2 | 22.2 |

- **34.4% RMSE reduction** at 1536 units relative to the 64-unit baseline.
- Diminishing returns beyond ~1536 hidden units (2048-unit model does not improve further and costs more compute).
- The LSTM baseline substantially outperformed all three simple baselines (global-average RMSE 5612.5 m, similar-case 5843.6 m, previous-value 6867.2 m, vs. 1335.1 m for the 64-unit LSTM).

### Temporal-regime consistency
- **Day/night:** RMSE nearly identical (e.g., 1536-unit model: 735.6 m day vs. 728.7 m night).
- **Mars-month / seasonal:** capacity-scaling trend holds consistently across all evaluated Ls bins and broad seasonal groups (northern summer/southern winter vs. northern winter/southern summer).

### Feature-influence concentration
Evaluated via one-feature-at-a-time scrambling (80 features per time step; top 20/40/60/80% = 16/32/48/64 features). Larger models concentrate influence more: top-20% feature share captures 51.89% (64-unit) → 73.63% (1536-unit) → 75.63% (2048-unit) of measured influence.

### Uncertainty (conformal prediction)
Post-hoc split-conformal calibration on validation residuals, evaluated for 64-, 96-, and 1536-unit models at 90% and 95% nominal coverage. Interval width narrows substantially with capacity (e.g., 90% interval: 5352.34 m at 64-unit → 3247.89 m at 1536-unit) while empirical coverage stays close to nominal throughout.

## Metrics used

Primary: **RMSE** (meters). Supporting: **R²**, **nRMSE** (normalized by target IQR), **MAPE** (computed only for |y| > 200 m). Secondary diagnostics: MAE, bias, sMAPE.

## Repository contents (source code provided by student — results not yet regenerated/verified in this repo)

*(Fill in / adjust this section to match the actual code layout once uploaded — e.g., data preprocessing scripts, model training scripts, hidden-unit sweep runner, conformal calibration code, postprocessing/plotting scripts for day/night, Mars-month, seasonal, and feature-influence analyses.)*

## Notes for README / documentation work

- The paper's dataset access point is the Mars Climate Database web service: https://www-mars.lmd.jussieu.fr/
- Reproducibility relies on: fixed seed list (see above), fixed blocked-time split protocol, and the intersection-minimal 80-feature input signature 

