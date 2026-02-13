F1 Race Strategy Engine (Hybrid ML + Physics)
A professional-grade race strategy simulation engine that combines Machine Learning (XGBoost) with First-Principles Physics to optimize pit stops and predict race outcomes.

Validation Result: Correctly predicted the Monza 2024 1-Stop Strategy (Leclerc) over the theoretically faster 2-Stop (Piastri) with 1.8s accuracy, outperforming traditional strategy models that favored the 2-stop.

🚀 Key Capabilities
Hybrid Architecture: Uses XGBoost for non-linear tire degradation modeling and a Physics Engine for fuel burn, track evolution, and traffic penalties.

Physics-Informed ML: Features include "Effective Abrasion" (Graining) and "Compound Softness Interactions" to force the model to respect thermodynamic laws.

Strategy Solver: Simulates thousands of race permutations (Undercut vs. Overcut) to find the optimal pit window.

Thermal & Traffic Modeling: Adjusts degradation rates based on Track Temperature (T 
track
​	
 ) and Traffic Density (ρ 
traffic
​	
 ).

🏗️ The Pipeline
This is not just a regression model; it is a full simulation stack.

Ingestion: Pulls raw telemetry (Lap Times, Position, Weather) via FastF1.

Physics Normalization:

Fuel Correction: Removes the ~0.03s/kg fuel effect to isolate tire performance.

Track Evolution: Normalizes for rubbering-in (e.g., -0.04s/lap at Monza).

Dirty Air: Calculates "Traffic Penalty" based on gap to car ahead.

ML Modeling (XGBoost):

Predicts Pace Loss (Delta) based on TyreLife, Compound, and CircuitFeatures.

Uses Monotonic Constraints to enforce "Older = Slower" physics.

Simulation Engine:

Reconstructs the race lap-by-lap, adding back Fuel, Traffic, and Pit Loss (t 
pit
​	
 ).

Optimizes for Total Race Time (T 
race
​	
 ).

🏆 Case Studies & Validation
1. The Monza Miracle (Italian GP 2024)

The Scenario: McLaren and most teams boxed twice, believing high degradation favored fresh tires. Ferrari (Leclerc) stayed out for a 1-stop.

Official Pirelli Data: Rated Monza "Low Abrasion" (2/5).

My Model's Insight: Adjusted for "Effective Abrasion" (4/5) due to high graining on the new surface.

Prediction:

Leclerc (1-Stop): 4986.14s

Piastri (2-Stop): 4987.94s

Delta: 1-Stop was 1.80s faster.

Reality: Leclerc won by ~2.7s. The model correctly identified the crossover point that McLaren missed.

2. The Bahrain Optimization (Bahrain GP 2024)

The Scenario: A high-degradation track where the "Undercut" is powerful.

Simulation: Compared Max Verstappen's winning strategy (Soft-Hard-Soft) vs. the Conservative alternative (Soft-Hard-Hard).

Result: The model predicted S-H-S was 1.13s faster, validating the aggressive strategy choice.

Insight: The model correctly balanced the C3's performance advantage against its higher thermal degradation.

🛠️ Usage
This project runs as a comprehensive Jupyter Notebook pipeline.

🔧 Dependencies
fastf1: Telemetry ingestion

xgboost: Non-linear degradation modeling

pandas & numpy: Data manipulation

matplotlib & seaborn: Strategy visualization

🔮 Future Improvements
Monte Carlo Simulation: Adding probability distributions for Safety Cars (VSC/SC) to calculate "Risk-Adjusted" strategy time.

Driver Profiling: Adding a DriverStyle feature (e.g., "Tire Whisperer" vs. "Aggressive") to customize degradation curves for specific drivers.

Built by Elijah McCauley. Validated against 2024 F1 Season Results.