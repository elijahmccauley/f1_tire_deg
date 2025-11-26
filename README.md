# F1 Tire Degradation Analysis

A data-driven approach to modeling Formula 1 tire degradation using FastF1 telemetry data and circuit metadata.

## 🎯 Project Goal

Build a predictive model for F1 tire wear that can:
1. Predict how lap times degrade as tires wear
2. Compare degradation characteristics across tire compounds (C1-C5)
3. Provide pit stop strategy recommendations

## 📊 Data Sources

### FastF1 Lap Data
- 2024 F1 season race data via the FastF1 Python library
- Lap times, sector times, tire compounds, and stint information
- Weather data (track temperature, air temperature)
- Position and gap data for dirty air calculations

### Circuit Metadata (`circuit.csv`)
Circuit-specific characteristics that affect tire wear:
- **Traction**: Grip demand rating (1-5)
- **Tyre Stress**: Overall stress on tires (1-5)
- **Asphalt Grip**: Track surface grip level (1-5)
- **Asphalt Abrasion**: Surface roughness/wear factor (1-5)
- **Track Evolution**: How much the track rubbers in (1-5)
- **Breaking**: Braking intensity required (1-5)
- **Lateral**: Lateral load on tires (1-5)
- **Downforce**: Aerodynamic downforce level (1-5)
- **Lap Length**: Circuit length in kilometers

## 🔧 Features Used in the Model

### Primary Features
| Feature | Description |
|---------|-------------|
| `TyreLife` | Number of laps on current set of tires |
| `compound_speed` | Compound encoding: C5=5 (fastest), C4=4, C3=3, C2=2, C1=1 (slowest) |
| `compound_x_tyrelife` | Interaction term: compound_speed × TyreLife (softer tires degrade faster) |
| `dirty_air` | Proximity to car ahead (0=clean air, 1=bumper-to-bumper) |
| `TrackTemp` | Track surface temperature (°C) |
| `team_tier` | Car performance tier (1=top teams, 4=backmarkers) |
| `Lap Length` | Circuit length in kilometers |

### Circuit Features
All circuit characteristics from the metadata are included as additional features.

### Engineered Features
| Feature | Description |
|---------|-------------|
| `tyrelife_squared` | Non-linear degradation component |
| `compound_x_tyrelife_sq` | Compound × TyreLife² for accelerating late-stint degradation |
| `delta_from_fresh` | Target variable: seconds slower than fresh tire baseline |
| `fuel_time_loss` | Estimated lap time loss due to fuel load |
| `lap_time_fuel_corrected` | Fuel-corrected lap times |

## 🏗️ Model Architecture

### Approach 1: Gradient Boosting Regressor
- **Target**: `delta_from_fresh` (seconds slower than lap 1 on fresh tires)
- **Algorithm**: scikit-learn GradientBoostingRegressor
- **Hyperparameters**: 200 estimators, max_depth=5, learning_rate=0.1

### Approach 2: Physics-Informed Ridge Regression
- Linear model with physics-based feature engineering
- Enforces monotonically increasing degradation
- Post-processing to guarantee no mid-stint "improvements"

## 📈 Current Results

### Model Performance
- Test R²: ~0.11-0.15 (expected due to high noise in racing data)
- Test RMSE: ~1.5-1.7 seconds

### Key Findings

1. **Data Sparsity Issue**: Soft compounds (C5) have limited long-stint data because teams pit before tires degrade significantly. This creates **selection bias** - we don't observe the true degradation curve.

2. **Degradation Rates** (measured from actual stints):
   - C1: ~55 ms/lap degradation rate
   - C2: ~49 ms/lap
   - C3: ~42 ms/lap
   - C4: ~37 ms/lap
   - C5: ~36 ms/lap

3. **Stint Length Distribution**:
   - C5 stints average ~15-18 laps
   - C1 stints can extend 30+ laps
   - This explains why C1 appears to have "worse" degradation in absolute terms

## ⚠️ Known Limitations

1. **Selection Bias**: Teams pit before tires fall off the "cliff", so we rarely see true end-of-life performance for soft compounds.

2. **Extrapolation Risk**: Predictions beyond observed stint lengths are unreliable.

3. **Model Smoothness**: The monotonic curves are artificially smooth - real degradation has more variation.

4. **Track-Specific Effects**: A global model may not capture track-specific degradation patterns.

## 📁 Project Structure

```
f1_tire_deg/
├── F1_TireDeg_Analysis.ipynb    # Main analysis notebook
├── FastF1Test.ipynb             # FastF1 exploration
├── circuit.csv                  # Circuit metadata
├── README.md                    # This file
└── outputs/
    ├── tire_degradation_model_v4.joblib
    ├── tire_degradation_model_v5_monotonic.png
    ├── monotonic_degradation_curves.csv
    ├── data_sparsity_heatmap.png
    └── degradation_rate_analysis.png
```

## 🚀 Usage

```python
import fastf1
import pandas as pd

# Load a race session
session = fastf1.get_session(2024, "Bahrain", "R")
session.load()

# Process for tire analysis
processed_laps = process_race_for_tire_analysis(session)

# Predict degradation
delta = predict_degradation(
    tire_life=15,
    compound='C3',
    track_temp=40,
    dirty_air=0.3,
    team_tier=2
)
```

## 🔮 Future Improvements

1. **Per-Circuit Models**: Train separate models for high/medium/low degradation circuits
2. **Bayesian Approach**: Use priors to constrain predictions within physically realistic bounds
3. **Degradation Rate Focus**: Model rate of change rather than absolute values
4. **Multi-Stint Strategy**: Optimize full race strategy, not just single pit stops
5. **Real-Time Updates**: Incorporate live telemetry for dynamic predictions

## 📚 Dependencies

- `fastf1` - F1 telemetry data
- `pandas` - Data manipulation
- `numpy` - Numerical operations
- `scikit-learn` - Machine learning models
- `matplotlib` / `seaborn` - Visualization
- `scipy` - Statistical analysis

## 📝 License

This project is for educational and research purposes. F1 data is provided by FastF1 under their terms of use.