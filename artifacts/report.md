# F1 Tire Degradation - Data-Driven Model Report

## Overview

General Model Metrics (test set): R2=0.283, RMSE=1.093s, MAE=0.635s

## Reliability Ranges

| compound   |   max_reliable_tyre_life |   available_samples |
|:-----------|-------------------------:|--------------------:|
| C1         |                       32 |                2742 |
| C2         |                       38 |                3761 |
| C3         |                       47 |               10295 |
| C4         |                       30 |                4506 |
| C5         |                        5 |                  87 |

## Degradation Slopes (s per lap)

| compound   |   linear_slope_s_per_lap |   delta_at_max |   max_observed_tyre_life |
|:-----------|-------------------------:|---------------:|-------------------------:|
| C1         |               0.0636168  |      1.33247   |                       32 |
| C2         |              -0.0190935  |     -1.49652   |                       38 |
| C3         |              -0.00492761 |     -1.27778   |                       47 |
| C4         |               0.0159959  |      0.0344153 |                       30 |
| C5         |               0.151879   |      0.439386  |                        5 |

## Per-Compound Metrics

| compound   |   n_test_laps |        r2 |     rmse |      mae |
|:-----------|--------------:|----------:|---------:|---------:|
| C1         |           630 |  0.159067 | 0.823204 | 0.608863 |
| C2         |           653 | -0.913519 | 1.68058  | 0.864616 |
| C3         |          2145 |  0.246867 | 0.951037 | 0.597756 |
| C4         |           948 |  0.67175  | 1.04324  | 0.582172 |
| C5         |            18 |  0.141131 | 0.547746 | 0.387204 |

## Top Feature Importances

|                  |   importance |
|:-----------------|-------------:|
| Lap Length       |   0.227133   |
| Tyre Stress      |   0.146152   |
| TyreLife         |   0.14435    |
| TrackTemp        |   0.12898    |
| AirTemp          |   0.099066   |
| dirty_air        |   0.0555063  |
| Asphalt Abrasion |   0.0328119  |
| Breaking         |   0.0325804  |
| Traction         |   0.0324768  |
| compound_C2      |   0.0294752  |
| Lateral          |   0.0263178  |
| Asphalt Grip     |   0.0125917  |
| Downforce        |   0.0124098  |
| compound_C1      |   0.00802386 |
| Track Evolution  |   0.00716445 |

## Grouped Importances

| group             |   features |   importance_sum |   importance_mean |
|:------------------|-----------:|-----------------:|------------------:|
| Environment       |          3 |        0.283552  |        0.0945173  |
| Circuit_Load      |          5 |        0.249937  |        0.0499873  |
| Lap_Length        |          1 |        0.227133  |        0.227133   |
| TyreLife          |          1 |        0.14435   |        0.14435    |
| Circuit_Grip_Wear |          3 |        0.052568  |        0.0175227  |
| Compound One-Hots |          5 |        0.0424603 |        0.00849205 |

## Interpretation

- Longer observed stints inflate total delta for harder compounds.

- TyreLife dominates; circuit load features have secondary influence.

- dirty_air contributes but less than intrinsic tyre wear + circuit stress.

## Limitations

- Low signal-to-noise; modest R2 expected.

- No extrapolation beyond reliable ranges (dashed in plots).

- Feature importances can shift with additional races.

- Random Forest variance used as proxy; not full predictive uncertainty.

## Artifacts

- Model: general_random_forest.joblib

- Features: feature_list.json

- Reliability: reliable_ranges.json

- Engineered data: engineered_data.csv

- Predictions with CI: test_predictions_with_ci.csv