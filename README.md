# Day-Ahead Electricity Price Forecasting (Bergen)

This repository contains an exploratory data analysis and modeling workflow for forecasting **day-ahead electricity prices** for Bergen, Norway (bidding zone NO5).

The work is intentionally practical rather than purely academic. The long-term goal is to build a **live forecasting dashboard** where users can see next-day price projections alongside realized prices, including an honest view of model accuracy over time.  
At this stage, the notebook represents a **static modeling and evaluation report**. A live pipeline using Azure Databricks and a lightweight Power BI dashboard is planned as the next step.

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

- Weron, R. (2014). *Electricity price forecasting: A review of the state-of-the-art with a look into the future.*
- Ziel, F., Steinert, R., & Husmann, S. (2015). *Efficient modeling and forecasting of electricity spot prices.*
- Lago, J., De Ridder, F., & De Schutter, B. (2018). *Forecasting day-ahead electricity prices: A review of state-of-the-art algorithms, best practices, and an open-access benchmark.*
- Uniejewski, B., Marcjasz, G., & Weron, R. (2016). *Understanding intraday electricity price forecasts.*

These works highlight the importance of autoregressive effects, calendar structure, and external drivers such as weather, while also emphasizing the trade-offs between accuracy, stability, and interpretability.

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
