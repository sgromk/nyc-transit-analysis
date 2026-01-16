# MTA Ridership Forecasting: A Tournament of Models
### Team Members: Rizvee Ahmed, Brendan Hendricks, Gabriel Kinshuk, Vyshnavi Maringanti

This repository contains the analytical framework and implementation code used to forecast daily ridership across the Metropolitan Transportation Authority (MTA) network. The project addresses the operational challenges of the post-pandemic fiscal cliff, where projected operating deficits are expected to reach $400 million by 2027.

The analysis treats public transit as a Service Supply Chain where the "product" (seat-miles) is instantly perishable. This requires precise demand matching to optimize capacity planning and resource allocation.

## Project Overview

We employ a Tournament of Models approach to evaluate the trade-offs between the interpretability of classical Frequentist methods and the flexibility of Bayesian probabilistic programming. The study segments the New York City transit landscape into three modes: the Subway System, the Bus Network, and Bridges and Tunnels.

### Analytical Framework
The analysis identifies three primary structural characteristics in the transit time series:
* **Complex Seasonality**: A dominant 7-day weekly cycle, specifically influenced by the Return to Office shift where demand is lower on Mondays and Fridays.
* **Stochastic Trends**: A post-pandemic ridership plateau at approximately 60% to 70% recovery.
* **Structural Breaks**: Behavioral shifts that render historical data prior to 2020 largely obsolete.



---

## Models Evaluated

The repository includes implementations for the following architectures:

### 1. Classical Frequentist Models (R `fable`)
* **Naïve and Seasonal Naïve (SNaive)**: Established as performance baselines to test the persistence of the 7-day weekly cycle.
* **ETS (Error, Trend, Seasonality)**: Exponential smoothing state-space models used to capture local level shifts.
* **ARIMA**: Selected for its ability to model complex autocorrelation structures inherent in commuter habits.

### 2. Bayesian Probabilistic Modeling (`Stan`)
A custom $AR(7)$ model was developed to explicitly encode the weekly dependence structure. The data generation process is defined as:

$$y_{t} \sim \text{Normal}(\mu_{t}, \sigma)$$
$$\mu_{t} = \alpha + \sum_{k=1}^{7} \beta_{k} y_{t-k}$$

Where $y_{t}$ represents the log-transformed ridership and $\beta_{k}$ represents the autoregressive coefficients for the preceding seven days.



---

## Dataset and Scope

* **Training Period**: January 1, 2024, to December 31, 2024, covering a 366-day window.
* **Forecast Horizon**: January 1, 2025, to January 9, 2025 (9-day out-of-sample validation).
* **Data Source**: MTA Open Data portal.
* **Scope Boundaries**: The models focus on system-wide aggregate demand and do not account for station-level granularity or exogenous weather variables.

---

## Key Performance Results

The models were evaluated using Mean Absolute Error (MAE) as the primary metric for operational capacity risk.

| Mode | Champion Model | MAE | Lift vs. Naïve Baseline |
| :--- | :--- | :--- | :--- |
| **Subway** | ARIMA | 258,740 | +67.0% |
| **Bus** | ARIMA | 160,174 | +43.2% |
| **Bridges** | Naïve | 48,691 | 0.0% |

### Key Findings
* **ARIMA Dominance**: ARIMA was the most effective for public transit; it successfully isolated signals from noise caused by hybrid work volatility.
* **Private Transport Stability**: Vehicular traffic on bridges and tunnels is highly stable; complex models failed to outperform a simple Naïve random walk.
* **The Holiday Blind Spot**: The Bayesian $AR(7)$ model exhibited a significant downward bias because it lacks a Holiday Regressor, leading to failures in recognizing that holidays do not follow typical weekday patterns.
