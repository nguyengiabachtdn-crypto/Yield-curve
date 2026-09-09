# Yield-curve-fitting

# Overview
This repo is my attempt at fitting different yield curve models, like the NSS or the Diebold-Li dynamic parameterization. These factors are dynamically modeled over time to generate 1-day ($h=1$) and 21-day ($h=21$) out-of-sample yield curve predictions, evaluated against a Naive Persistence (Random Walk) benchmark.

# Features
This notebook features
* **Decay factor calibration**: Instead of fixing decay rates, I calibrated them to the data via grid search for the NSS curve or via minimizing the Sum of Squared Errors (SSR) for the D-L model.
* **Dynamic Time-Series Modeling**: Fitting an AR(1) model to the parameters
* **Out-of-Sample testing**: Predictions tested against real data

# Models
I attempt to model US Treasuries Yield using the Nelson-Siegel-Svensson (NSS) static curve and its successor, the dynamic Diebold-Li (D-L) model

NSS model

$$y_t(\tau) = \beta_{1t} + \beta_{2t} \left( \frac{1 - e^{-\lambda_1 \tau}}{\lambda_1 \tau} \right) + \beta_{3t} \left( \frac{1 - e^{-\lambda_1 \tau}}{\lambda_1 \tau} - e^{-\lambda_1 \tau} \right) + \beta_{4t} \left( \frac{1 - e^{-\lambda_2 \tau}}{\lambda_2 \tau} - e^{-\lambda_2 \tau} \right)$$

Diebold-Li model 

$$y_t(\tau) = \beta_{1t} + \beta_{2t} \left( \frac{1 - e^{-\lambda_1 \tau}}{\lambda_1 \tau} \right) + \beta_{3t} \left( \frac{1 - e^{-\lambda_1 \tau}}{\lambda_1 \tau}\right)$$

where

* **$\beta_{1t}$ (Level Factor):** Long-term asymptotic yield level 
* **$\beta_{2t}$ (Slope Factor):** Short-term factor component whose loading starts at $1.0$ at zero maturity ($\tau = 0$) and monotonically decays to $0$.
* **$\beta_{3t}$ (First Curvature Factor):** Primary medium-term hump or trough factor 
* **$\beta_{4t}$ (Second Curvature Factor):** Secondary medium-term factor 
* **$\lambda_1$ (First Decay Parameter):** Governs the exponential decay rate of the slope and determines the exact maturity location of the primary curvature peak.
* **$\lambda_2$ (Second Decay Parameter):** Governs the exponential decay rate and determines the exact maturity location of the secondary curvature peak.
* **$\tau$ (Time to Maturity):** Residual life of the financial instrument expressed in years ($\tau > 0$).

# Methodologies
The parameters are estimated using the Ordinary Least Square method. Predictions are made with a univariate AR(1) model

$$\hat{\beta}_{i,t} = c_i + \gamma_i\hat{\beta}_{i,t-1} + \epsilon_i$$

and horizon reconstruction is done by

$$\hat{y}_{t+h} = \hat{\boldsymbol{\beta}}^\top_{t+h} \textbf{X}_\tau$$
