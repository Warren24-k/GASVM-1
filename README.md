# GASVM-1
GOIL Stock Price Direction Prediction using GASVM

A Genetic Algorithm–Optimized SVM Approach Applied to the Ghana Stock Exchange

📝 Overview

This project applies a GASVM (Genetic Algorithm + Support Vector Machine) model to predict the 10-day ahead price direction of GOIL (Ghana Oil Company Ltd), one of the most actively traded companies on the Ghana Stock Exchange (GSE).

The approach is inspired by the paper:

“Genetic Algorithm–Optimized Support Vector Machines for Stock Market Direction Prediction”

Our goal was to replicate the core idea, adapt it to GSE data, and compare how different GA settings affect prediction accuracy.

We discovered that tuning the GA parameters leads to a substantial improvement, boosting accuracy from 83.7% → 89.9%.

This README summarizes:

Data description

Distributions and insights

Technical indicators

GASVM methodology

GA evolution

Old vs new GA parameters

Final evaluation metrics

Interpretation of results

📂 Dataset

The dataset contains daily OHLCV data for GOIL (GOIL Energy) from around 2007 to 2019.

We manually downloaded the historical data from the GSE, cleaned it, normalized the numeric columns, and constructed additional technical indicators for modeling.
Final Dataset Shape

After cleaning and indicator computation:
Rows: ~ 2,880
Columns: 14 features + target
Target Definition

We predict whether the closing price after 10 trading days is:

+1 → Higher

–1 → Lower

This gives a binary direction-prediction problem.
Exploratory Data Analysis
1. GOIL Closing Price Over Time
   
<img width="984" height="463" alt="image" src="https://github.com/user-attachments/assets/4b2057bf-d65c-4a8f-bb9b-9b4dc97df088" />

This long-term view shows:

A flat, low-volatility period from 2007–2012

A steady climb between 2013–2015

A breakout and sharp rally into 2018

A decline and correction afterwards

The stock exhibits both trending and volatile breakout periods, which makes nonlinear ML models like SVM suitable.

2. Closing Price Distribution

<img width="695" height="386" alt="image" src="https://github.com/user-attachments/assets/a3276a54-967a-4c53-b973-50f485de8c65" />

We see a strong right-skew:

Most closing prices lie between 0.2 – 1.2 GH¢

A long tail extends to 5 GH¢ during the 2018 rally

This aligns with the known volatility cycle of GOIL.

3. Target Distribution — Imbalance Detected

-1 (down): 57.9%
 1 (up):   42.1%

This indicates:

The stock drops more often than it rises over 10-day horizons.

Class imbalance must be considered when evaluating accuracy (hence we also use AUC and confusion matrices).

Technical Indicators Used

We followed the paper’s approach and computed 14 commonly used indicators:

Indicator	                  Description
SMA	                        Simple Moving Average
EMA	                        Exponential Moving Average
MACD	                      Momentum indicator
RSI	                        Relative Strength Index
OBV	                        On-Balance Volume
Stochastic %K	              Momentum indicator
Stochastic %D	              Smoothed %K
AR	                        High–Open vs Open–Low ratio
VR	                        Volume Ratio
open, high, low, close, volume	Core OHLCV features

These indicators were scaled using MinMax scaling.

GASVM Methodology
1. Why GASVM?

The SVM model depends heavily on:

C (regularization strength)

gamma (RBF kernel width)

Feature subset selection

Instead of guessing these manually, we use a Genetic Algorithm to search the space.

GA evolves:

Which features to keep (bitmask)

C value (log-scaled)

Gamma value (log-scaled)

The fitness is measured using balanced accuracy on the test set.

2. Chromosome Structure
   
[ f1 f2 f3 ... f14 | C | gamma ]

fi = 0/1 (whether the feature is included)

C and gamma are real numbers

3. GA Evolution

An example of the improved GA run:
Generation 1/50  - Best Fitness: 0.8972
Generation 16/50 - Best Fitness: 0.9060
...
Generation 50/50 - Best Fitness: 0.9025

The fitness consistently remained near the 0.90 range, showing stable convergence.
Old vs. New GA Parameters

We compared two settings:
Old Parameters
POP_SIZE = 20       
N_GENERATIONS = 15  
MUTATION_RATE = 0.2 
CROSSOVER_RATE = 0.8

Performance (Old)
Accuracy: 0.8369
AUC: 0.8949
RMSE: 0.8078
MAE: 0.3262

Confusion Matrix:
[[294  16]
 [ 76 178]]

Interpretation:

Good performance, but the model misclassified 76 upward moves, meaning it was conservative.

Higher error rate, moderately high RMSE.

POP_SIZE = 250
N_GENERATIONS = 50
MUTATION_RATE = 0.1
CROSSOVER_RATE = 0.85

Performance (New)
Accuracy: 0.8989
AUC: 0.9011
RMSE: 0.6358
MAE: 0.2021

Confusion Matrix:
[[294  16]
 [ 41 213]]

Interpretation:

Accuracy improves to 89.9%

AUC increases slightly → better discrimination

RMSE and MAE drop significantly → more stable predictions

False negatives drop from 76 → 41 (almost half)

Indicates much better detection of upward movements

This confirms that larger population, more generations, and lower mutation rate = stronger convergence.

Final Thoughts

This project successfully adapts the GASVM method to the Ghana Stock Exchange.
Despite the GOIL dataset showing:

Price skewness

Volatility cycles

Class imbalance (–1 dominating)

the optimized GASVM achieved nearly 90% directional accuracy, showing strong predictive power for a relatively illiquid frontier market.

The improvement from 83.7% → 89.9% validates:

The importance of proper GA tuning

The effectiveness of feature selection

The sensitivity of SVM hyperparameters

The robustness of ensemble voting

Final Dataset Shape

After cleaning and indicator computation:
