**Predicting the Crowd: A Regression Analysis of Alberta Wheat Yields using Eco-Climate Data**

## **I. Problem Formulation (7 Marks)**

Alberta's wheat sector is economically critical, yet yield prediction traditionally relies strictly on direct meteorological measurements. These measurements often miss ground-level environmental signals such as soil health and localized microclimate moisture.

**Specific Question:** *Does a multiple linear regression model combining bryophyte biodiversity indicators with climate variables outperform a baseline climate-only model in predicting annual wheat yield (bushels/acre) in Alberta?*

Because the target variable (wheat yield) is continuous, this is formulated as a **Regression** problem.

## **II. Data Acquisition (12 Marks)**

We acquired data from three distinct public sources:

1. **Dataset A (Bryophyte Observations):** Sourced from the Alberta Biodiversity Monitoring Institute (ABMI). Contains systematic grid-based species observations. We aggregated this to derive annual biodiversity features (e.g., species richness).  
2. **Dataset B (Alberta Crop Yield):** Sourced from open.alberta.ca, containing historical provincial crop yields measured in bushels per acre.  
3. **Dataset C (Climate Data):** Sourced from Environment and Climate Change Canada (ECCC). Contains daily meteorological data (temperature, precipitation) which was aggregated into annual growing-season features.

## **III. Exploratory Data Analysis (11 Marks)**

During our EDA, we visualized the distributions of our continuous variables to check for normality and plotted scatterplots to observe linear relationships between climate features (like summer temperature) and crop yield.

**Hypothesis Testing:** We observed negative trends between extreme temperatures and yield. A formal statistical test (e.g., Pearson correlation coefficient or a t-test on extreme vs normal years) was conducted to evaluate the significance of these relationships before modeling.

## **IV. Data Preprocessing (12 Marks)**

To prepare the disparate datasets for linear regression, we executed the following cleaning pipeline:

* **Aggregation:** Daily weather data from ECCC and individual site observations from ABMI were mathematically aggregated by year to align with the annual crop yield target variable.  
* **Handling Missing Data:** Years with insufficient bryophyte sampling were dropped to prevent artificially skewed biodiversity metrics.  
* **Normalization:** Biological indicators (like identifications per site) were effort-normalized by dividing total counts by the number of sites sampled that year to control for sampling bias.  
* **Outliers:** Data was checked for extreme anomalies using IQR fences, ensuring no single corrupted sensor reading distorted our linear models.

## **V. Modeling (11 Marks)**

We selected **Multiple Linear Regression** because it provides explicit mathematical coefficients, allowing us to strictly quantify the "highest impact" variables on crop yield, unlike black-box models (e.g., Random Forests).

**Independent Variables (Features):** Mean summer temperature, total precipitation, spring temperature anomaly, and bryophyte richness.

**Dependent Variable (Target):** Wheat Yield (bushels/acre).

Because our aggregated dataset resulted in a highly limited sample size (n=10 years), conducting a standard 80/20 train-test split would leave only 2 data points for testing, which is mathematically unstable. Therefore, the models were evaluated on the full dataset, acknowledging the inherent risk of overfitting.

**Mathematical Equations (to 3 decimal places):**

* **Model A (Baseline Climate):** Yield \= β₀ \- 2.838(Temp) \- 0.032(Precip) \+ 2.155(SpringAnomaly)  
* **Model B (Augmented Eco-Climate):** Yield \= β₀ \- 3.393(Temp) \- 0.028(Precip) \+ 1.746(SpringAnomaly) \- 0.440(Bryophyte)  
* **Model C (Bryophyte Only):** Yield \= β₀ \- 0.575(Bryophyte)

## **VI. Model Evaluation (12 Marks)**

We utilized two distinct metrics to evaluate model performance: **Root Mean Square Error (RMSE)** to measure raw predictive error in actual units (bushels/acre), and **Adjusted R-Squared (R2)** to measure the proportion of variance explained while mathematically penalizing for the addition of unnecessary variables.

| Model | RMSE (bushels/acre) | Adjusted R²  |
| :---- | :---- | :---- |
| **Model A (Baseline)** | 3.03 | 0.231 |
| **Model B (Augmented)** | 2.95 | 0.147 |
| **Model C (Bryophyte Only)** | 3.95 | \-0.015 |

**Interpretation of Metrics:**

While Model B achieved the lowest absolute error (RMSE \= 2.95), the Adjusted R2 actually *decreased* from 0.231 (Model A) to 0.147 (Model B). This indicates that while adding the bryophyte data slightly reduced raw training error, it did not provide enough novel explanatory power to justify the increased complexity of the model given our small sample size. Model C's negative Adjusted R2 confirms that bryophyte data on its own is entirely insufficient for predicting crop yields.

## **VII. Interpretation of Results & Recommendations (11 Marks)**

**Comprehensive Summary & Parameter Impact:**

Translating our mathematical coefficients into real-world insights, summer temperature is the dominant driver of wheat yield. In Model B, the coefficient of \-3.393 means that, holding all other variables constant, every 1°C increase in average summer temperature results in a massive loss of roughly 3.4 bushels of wheat per acre. Conversely, warmer spring anomalies provided a boost (+1.746).

Interestingly, the bryophyte coefficient was negative (-0.440). This contradicts our initial hypothesis that biodiversity would strictly correlate with healthy, high-yield soil. It is possible that environments fostering heavy bryophyte growth are too damp or acidic for optimal wheat production.

**Strengths & Shortcomings:**

* **Strength:** The models are highly interpretable. By utilizing linear regression, we successfully isolated the specific weight of temperature versus precipitation, providing clear, quantifiable directives.  
* **Shortcoming:** The primary weakness is the severe sample size limitation. By aggregating site-level data into annual provincial averages, our dataset shrank to n=10. This limits statistical power and makes the coefficients highly sensitive to single-year anomalies (outliers).

## **VIII. Advice for Future Researchers (5% Bonus)**

If future researchers were to repeat this procedure, we strongly recommend abandoning provincial-level aggregation. Instead, the data should be joined spatially (e.g., using GIS coordinates to map specific ABMI survey sites to localized county-level crop yields). This would increase the sample size from n=10 to potentially n \> 500, allowing for a robust Train-Test split and ensuring the regression model does not overfit to the noise of global annual averages. Furthermore, agricultural researchers should prioritize funding for heat-resistant wheat strains, as temperature proved to be a vastly more impactful predictor of crop failure than precipitation.

## **IX. References & Citations (5 Marks)**

1. Alberta Biodiversity Monitoring Institute. (2021). Terrestrial Field Data Collection Protocols (Abridged Version).  
   [https://abmi.ca/publication/601.html](https://abmi.ca/publication/601.html)  
2. Alberta Biodiversity Monitoring Institute. Raw Bryophytes Dataset (Rotation 1 and Rotation 2).  
   Files used:  
   * T01A\_Site\_Physical\_Characteristics\_2692088856810991747  
   * T19B\_Moss\_Identification\_since\_2009\_11290156903414781363  
3. Spatial coverage: North Saskatchewan and Red Deer Land-use Framework regions.  
   Retrieved April, 2026 from:  
   [https://abmi.ca/data-portal](https://abmi.ca/data-portal)  
4. Environment and Climate Change Canada. Historical Climate Data: Daily Weather Observations (2010–2019).  
   Stations used:  
   * Red Deer A (Station ID 2134, 2010–2013)  
   * Red Deer Regional A (Station ID 51440, 2014–2019)  
5. Parameters: daily observations (timeframe \= 2), CSV format.  
   Derived variables: mean summer temperature, growing season precipitation, frost-free days, and spring temperature anomaly.  
   Retrieved April 2026 from:  
   [https://climate.weather.gc.ca/climate\_data/bulk\_data\_e.html](https://climate.weather.gc.ca/climate_data/bulk_data_e.html)  
6. Statistics Canada. Table 32-10-0359-01  Estimated areas, yield, production, average farm price and total farm value of principal field crops, in metric and imperial units"  
   Retrieved April, 2026 from:  
   [https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210035901](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210035901)  
7. Wikipedia contributors. (2024). Cook's distance. Wikipedia.  
   [https://en.wikipedia.org/wiki/Cook%27s\_distance](https://en.wikipedia.org/wiki/Cook%27s_distance)  
8. Wikipedia contributors. (2024). Variance inflation factor. Wikipedia.  
   [https://en.wikipedia.org/wiki/Variance\_inflation\_factor](https://en.wikipedia.org/wiki/Variance_inflation_factor)  
9. Wikipedia contributors. (2024). Coefficient of determination. Wikipedia.  
   [https://en.wikipedia.org/wiki/Coefficient\_of\_determination](https://en.wikipedia.org/wiki/Coefficient_of_determination)

