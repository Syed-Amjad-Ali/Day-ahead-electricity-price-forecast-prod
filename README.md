# Day-Ahead Electricity Price Forecasting (Bergen)

This repository contains the main exploratory data analysis and modeling workflow for forecasting **day-ahead electricity prices** for Bergen, Norway (bidding zone NO5).

The project currently centers around a single, end-to-end notebook that documents the full thought process: from initial data exploration and time series decomposition to feature design, model testing, and evaluation. This notebook serves as the analytical foundation of the project.

As the project evolves, this workflow will gradually be broken down into smaller, purpose-specific notebooks aligned with a **bronze–silver–gold** structure. These will be used as part of scheduled Azure Databricks jobs and pipelines, separating data ingestion, transformation, forecasting, and evaluation more clearly.

The work is intentionally practical rather than purely academic. The long-term goal is to build a **live forecasting dashboard** where users can see next-day price projections alongside realized prices, including an honest view of model accuracy over time.

At this stage, the repository represents a **static modeling and evaluation report** backed by real data. A live pipeline using Azure Databricks and a lightweight Power BI dashboard is planned as the next step.

---

## Data source

Hourly electricity spot prices are retrieved from the publicly available and generously maintained API provided by  
[https://www.hvakosterstrommen.no](https://www.hvakosterstrommen.no)

The data ultimately originates from the ENTSO-E Transparency Platform:  
[https://transparency.entsoe.eu](https://transparency.entsoe.eu)

The openness and reliability of these sources make them well suited for experimentation, validation, and future deployment in a production-like setting.

Weather data (air temperature) is included as an additional explanatory variable to capture demand-side effects.

---

## Project objective

The objective is to forecast **the next 24 hours of electricity prices** using only information that would realistically be available at the time of prediction.

The focus is deliberately narrow:
- Short-term (day-ahead) forecasting  
- Hourly resolution  
- Transparent and explainable models  
- Continuous comparison between predicted and actual prices  

Rather than maximizing complexity, the emphasis is on understanding model behavior, limitations, and real-world usability.

---

## Background and related literature

For a more academic perspective on electricity price forecasting, the following papers provide useful context and motivation:

1. Jaehnert, S., Farahmand, H., & Doorman, G. L. (2009).  
   *Modelling of Prices Using the Volume in the Norwegian Regulating Power Market.*  
   NTNU, IEEE-style conference paper.

2. Jensen, M. W., Ren, H., & Shalaginov, A. (2024).  
   *Day-ahead Electricity Price Forecasting of Elspot Markets in Norway.*  
   ENERGY 2024 (IARIA), Kristiania University College.

3. Mabilangan, G. (2025).  
   *Statistical Analysis of Electricity Prices in Southern Norway.*  
   Noroff School of Technology and Digital Media, March 2025.

4. Scott, O. H., & Mellander, W. (2025).  
   *Scenario-Based Long-Term Electricity Price Forecasting in NO4 (Norway) Using a Hybrid Machine Learning Model.*  
   Bachelor Thesis, KTH Royal Institute of Technology.

These works highlight the importance of autoregressive effects, calendar structure, and external drivers such as weather, hydropower availability, global fuel prices, and exchange rates while also emphasizing the trade-offs between accuracy, stability, and interpretability.

---
## Notes
This project is intentionally iterative. The current model is not presented as a final or optimal solution, but as a well-reasoned and reproducible baseline that can be extended as more data sources and operational constraints are introduced.

## Modeling approach

The modeling process followed a gradual and exploratory path:

1. Initial exploratory analysis and time series decomposition to understand trend and seasonality.
2. Baseline linear models using calendar features such as hour of day and weekday.
3. Introduction of lagged prices, with a focus on a 24-hour lag to preserve a realistic day-ahead forecasting setup.
4. Inclusion of air temperature as a weather-related explanatory variable.
5. Experiments with XGBoost to test whether nonlinear relationships improve performance.
6. Final selection of an OLS model based on performance, stability, and interpretability.

While more complex models can capture nonlinearities, the final OLS specification provides a strong balance between predictive power and transparency, which is important for deployment in a public-facing dashboard.

---

## Results and evaluation

The final model captures the intraday structure of prices well and produces realistic day-ahead forecasts.  
Errors are most pronounced during sudden evening price spikes, which is expected given the limited information set and the linear nature of the model.

Rather than hiding these limitations, the project explicitly compares predicted and actual prices hour by hour. This makes model behavior and uncertainty visible, which is a core design principle for the planned dashboard.

---

## Next steps

This notebook is the analytical foundation for a live system. Planned extensions include:

- A daily automated data pipeline using Azure Databricks
- Monthly model retraining with expanding historical data
- A Power BI dashboard showing:
  - Day-ahead price forecasts
  - Actual realized prices
  - Model error over time
  - Cheapest and most expensive forecasted hours

The emphasis will remain on realism, transparency, and usability rather than overfitting or excessive model complexity.

---

## Repository contents

- `EDA and Time Series Decomp.ipynb`  
  Exploratory analysis, feature engineering, model development, and evaluation.

- `Results.txt`  
  Summary outputs and evaluation results.

---

## Notes ( again :) )

This project is intentionally iterative. The current model is not presented as a final or optimal solution, but as a well-reasoned and reproducible baseline that can be extended as more data sources and operational constraints are introduced.
