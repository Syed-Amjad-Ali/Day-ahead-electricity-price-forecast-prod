# Day-Ahead Electricity Price Forecasting (Bergen)

I built this project to test myself on **data engineering fundamentals combined with a small, realistic forecasting problem**.

The goal was not to build a highly complex machine learning model, but to understand how a **real-world pipeline** would look end to end:
API ingestion → cleaning and structuring data → running a forecasting model → preparing outputs for a simple dashboard.

Electricity prices turned out to be a good fit for this purpose. The data is real, time-dependent, publicly available, and naturally suited for incremental ingestion. Combined with weather data, it allowed me to explore both engineering and modeling decisions without artificial assumptions.

---

## Data source

Hourly electricity spot prices are retrieved from the publicly available and generously maintained API provided by  
[https://www.hvakosterstrommen.no](https://www.hvakosterstrommen.no)

The data ultimately originates from the ENTSO-E Transparency Platform:  
[https://transparency.entsoe.eu](https://transparency.entsoe.eu)

The openness and reliability of these sources make them well suited for experimentation, validation, and future deployment in a production-like setting.

Weather data (air temperature) is included as an additional explanatory variable to capture demand-side effects.

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



---

## Project evolution

I started this project in a **purely analytical way**.

Initially, I worked with a **static dataset** covering electricity prices and weather data from January 2023 to January 2026. Using Jupyter Notebook, I focused on:
- Exploratory data analysis  
- Understanding seasonality and intraday patterns  
- Testing different feature choices  
- Comparing simple statistical models  

After experimenting with several specifications, I intentionally settled on a **simple but stable OLS-based model**. The objective at this stage was not to optimize performance aggressively, but to establish a **baseline that behaves sensibly** and is easy to reason about.

Once the modeling side felt solid enough, I moved on to the main learning goal of the project: **turning an analysis notebook into a production-style pipeline**.

---

## Pipeline overview

I migrated the project to **Azure Databricks** and broke the large notebook into smaller, purpose-specific notebooks aligned with a **bronze–silver–gold (medallion) architecture**.

What started as a linear notebook gradually became a **non-linear DAG of dependent tasks**, closer to what I would expect in a real data platform.

### Bronze
- One-time historical backfill from CSV  
- Incremental ingestion from electricity price and weather APIs  
- Append-only storage using Delta tables  
- Ingestion timestamps preserved for auditability  

### Silver
- Incrementally maintained, cleaned datasets  
- Deduplication and late-arriving data handling  
- Clear separation between price data and weather data  
- Joins performed only when required for modeling  

### Gold
- Model-ready training datasets  
- Stored and versioned model parameters  
- Day-ahead forecasts written as reproducible outputs  

At first, I focused mainly on **getting the jobs to run correctly**. Once the pipeline was stable, I went back to improve:
- Table partitioning  
- Incremental logic  
- Proper use of Delta Lake `MERGE` patterns  
- Avoiding unnecessary full rewrites  

This iterative approach mirrors how I would expect a real system to evolve over time.

---

## Architecture and design principles

Some of the principles I consciously tried to apply while building the pipeline:

- Incremental processing instead of recomputing full history  
- Idempotent transformations using Delta Lake  
- One notebook per logical responsibility  
- Clear separation between ingestion, transformation, and modeling  
- Ability to inspect table history and understand how data evolved  

The pipeline is intentionally kept readable rather than overly abstract.

---

## Data sources

**Electricity prices**  
Hourly spot prices retrieved from:  
https://www.hvakosterstrommen.no  

Underlying source:  
ENTSO-E Transparency Platform  
https://transparency.entsoe.eu  

**Weather data**  
Hourly air temperature data retrieved from the Norwegian Meteorological Institute (Frost API).

Both data sources are free, reliable, and well suited for incremental ingestion, which made them ideal for this project.

---

## Modeling objective

The modeling task is deliberately constrained:

- Forecast the **next 24 hours** of electricity prices  
- Hourly resolution  
- Use only information that would realistically be available at prediction time  
- Keep the model transparent and explainable  

This constraint influenced both feature design and pipeline structure.

---

## Modeling approach

The current model is intentionally simple:

- Ordinary Least Squares (OLS)  
- Hour-of-day and weekday effects  
- Autoregressive structure using lagged prices  
- Weather effects via air temperature  

Model parameters are:
- Trained on gold-layer datasets  
- Versioned monthly  
- Stored explicitly in Delta tables  

The model is not treated as the “final answer”. Instead, it acts as a **baseline that the pipeline is built around**.

---

## Operational workflow

A typical execution flow looks like this:

1. Bronze ingestion jobs append new hourly price and weather data  
2. Silver tables incrementally update trusted datasets  
3. Silver joins produce feature tables for modeling  
4. Gold training datasets are refreshed  
5. Model parameters are retrained on a monthly cadence  
6. Day-ahead forecasts are generated and stored  

This structure is reflected in the non-linear Databricks Job graph.

---

## Results and evaluation

The model captures intraday price patterns reasonably well under normal conditions.  
Errors are largest during sudden price spikes, which is expected given the limited information set and the linear model structure.

Predicted and realized prices are stored separately, making it possible to evaluate performance transparently and prepare the data for future dashboarding.




---

## Project status and next steps


Planned improvements include:
- Improving the autoregressive structure (e.g. testing `t-168` instead of weekday dummies)  
- Using 24h Weather forecast data instead of imputation 
- The above two changes would simultaneously require adjustments in the data pipeline inside Databricks





---

## Notes  again :smiley: 

This project is intentionally iterative. The current model is not presented as a final or optimal solution, but as a well-reasoned and reproducible baseline that can be extended as more data sources and operational constraints are introduced.
