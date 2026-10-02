# Predicting Gun Violence in NYC by Census Tract - Poisson & Negative Binomial Regression

**Evan Maurer & Robert Arteaga** | ISYE 6414 - Regression Analysis | Georgia Institute of Technology

## Overview

For our final project in ISYE 6414, Robert and I built the count regression model for a larger team project studying gun violence in New York City. Our goal was to see how well census & economic indicators could predict the number of shootings in each NYC census tract, and which neighborhood characteristics were most associated with higher shooting rates.

We combined 24,127 NYPD shooting records with 2024 American Community Survey data, the 2022 CDC Social Vulnerability Index, and 2024 Census tract boundaries. After cleaning, we were left with 2,185 census tracts and 17 candidate predictors.

## What We Did

- Spatially joined every shooting incident to its census tract. The NYPD data had its latitude & longitude columns swapped, which we had to fix before any tract counts would show up.
- Split the data 60/20/20 into train, validation, & test sets, and standardized all predictors using only the training data.
- Started with a Poisson regression, using tract population as an offset so we were modeling shooting rates instead of raw counts.
- Found heavy overdispersion (a dispersion statistic of 7.52, where 1 is expected), so we switched to a Negative Binomial model.
- Removed influential tracts using Cook's distance, then picked predictors with stepwise selection based on validation performance. We used LASSO as a second check.
- Evaluated the final model once on the held-out test set.

## Results

- **Overdispersion changed the conclusions.** The plain Poisson model made all 8 selected predictors look significant. Once we corrected for overdispersion, only 6 held up. Its standard errors were 1.65 to 2.56 times too small.
- **Strongest risk factors:** percentage of Black & Hispanic residents and households without a vehicle. The race coefficients reflect NYC's history of segregation & disinvestment, not an effect of race itself.
- **Protective factors:** higher median household income, more residents with a bachelor's degree, and a higher share of limited-English households.
- **Poverty** on its own was tied to an 86% higher shooting rate, but it dropped out of the final model because other disadvantage measures already captured most of the same information.
- **The model missed some neighborhoods.** The biggest prediction errors were concentrated in Queens & Brooklyn, which points to local factors that census data doesn't capture.
- On the test set, the Negative Binomial model had an RMSE of 11.71 shootings per tract. The plain Poisson model actually predicted slightly better (10.92), but we kept the Negative Binomial for its more reliable statistical conclusions.

## Files

- `model1_count_poisson_nb.ipynb` - our full analysis, with all outputs and charts saved
- `ISYE6414_Final_Report.pdf` - the full team report

The notebook relies on helper files (`src/` and `data/`) that aren't included here, so it's meant to be read rather than re-run.

**Tools:** Python, pandas, statsmodels, scikit-learn, geopandas, matplotlib, seaborn

## Team

This was a five-person project. Robert & I built the Poisson / Negative Binomial model. Gulmira Zhavgasheva, Henok Yohannes, & Leroy Bagley built the linear & logistic regression models, and all five of us worked together on data collection, cleaning, the literature review, and the final report.

## References

**Data**

- Agency for Toxic Substances and Disease Registry. (2022). *CDC/ATSDR Social Vulnerability Index (SVI)*. https://www.atsdr.cdc.gov/placeandhealth/svi
- New York City Police Department. (2025). *NYPD Shooting Incident Data (Historic)* [NYC Open Data]. https://data.cityofnewyork.us/
- U.S. Census Bureau. (2024). *American Community Survey 5-Year Estimates*. https://www.census.gov/programs-surveys/acs
- U.S. Census Bureau. (2024). *TIGER/Line Shapefiles*. https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.html
