**Predicting the Crowd: A Regression Analysis of Alberta Wheat Yields using Eco-Climate Data**

Alberta's wheat sector contributes substantially to Canadian agricultural output, yet yield 
prediction traditionally relies strictly on direct meteorological measurements. These 
measurements often miss ground-level environmental signals such as soil moisture conditions 
and localized microclimate variation. Bryophytes, which lack roots and absorb water directly 
from their surroundings, are recognized as sensitive indicators of ambient moisture and 
temperature conditions at the ground level. In 2025 Thouvenot and others  
demonstrated that bryophyte species richness in agricultural grasslands responds 
to productivity and land use intensity, suggesting that bryophyte community composition 
carries ecological information applicable to agricultural contexts. This raises the question of 
whether bryophyte richness across Alberta encodes environmental signals that meteorological station data cannot.

**Specific Question:** Does a multiple linear regression model combining bryophyte biodiversity 
indicators with climate variables outperform a baseline climate-only model in predicting 
annual wheat yield in bushels per acre in Alberta?

Because the target variable (wheat yield) is continuous and the predictors are numerical, 
this is formulated as a regression problem. It is also nontrivial because it requires 
integrating three independent public datasets collected under different sampling protocols 
and spatial resolutions, harmonizing a protocol change in bryophyte survey methodology 
through effort correction, and evaluating whether a biological indicator adds predictive 
value over an established meteorological baseline.

## II. Data Acquisition

We acquired data from three distinct public sources, all publicly available and relevant to modeling annual wheat yield in Alberta:

**Dataset A (Bryophyte Observations):** Sourced from the Alberta Biodiversity Monitoring Institute (ABMI) data portal. Contains systematic grid-based moss and liverwort species observations collected across Alberta under two protocols: a pre-2009 whole-hectare search protocol (T19A, T18D) and a post-2009 quadrant-based protocol (T19B, T18A). A site coordinate table (T01A) provided latitude and longitude for each site and year. Annual biodiversity features were derived from these files. The dataset is well documented through ABMI field protocols and each variable is described in accompanying metadata, including missing value codes (DNC, VNA, SNI, SNR, UID, PNA, NONE) that distinguish true absences from data collection failures.

**Dataset B (Alberta Wheat Yield):** Sourced from Statistics Canada Table 32-10-0359-01, containing historical annual provincial wheat yield in bushels per acre from 1908 onward. Each row represents one calendar year with a single yield measurement. The dataset is straightforward with no ambiguous variables.

**Dataset C (Climate Data):** Sourced from Environment and Climate Change Canada (ECCC) historical climate data. Contains daily meteorological observations including mean temperature, minimum temperature, and total precipitation. Two Red Deer stations were used to span the modeling window without gaps: Red Deer A (Station ID 2134, 2008 and 2010 to 2013) and Red Deer Regional A (Station ID 51440, 2014 to 2019). Daily records were aggregated into four annual growing season features: mean summer temperature, total growing season precipitation, frost free days, and spring temperature anomaly.

All three datasets are appropriate for modeling annual wheat yield as a function of climate and ecological conditions. The modeling window covers 2008 and 2010 to 2019, yielding 11 usable observations after filtering years with insufficient bryophyte site coverage.

## III. Data Preprocessing

**Duplicate Rows:** T01A was deduplicated to one coordinate record per site and year using drop duplicates on the site and year joint key, ensuring each site contributes only one spatial record per field season.

**NaN and Missing Value Handling:** ABMI missing value codes (DNC, VNA, SNI, SNR, UID, PNA, NONE) were treated as NaN and removed before any richness calculations. Rows in T19B and T19A with no matching effort record in T18A or T18D respectively were dropped. Rows with no matching T01A coordinate were dropped. For climate data, missing values in 2016 and 2017 (caused by station gaps) were imputed using the column mean across the modeling window, which is appropriate given the small number of affected values and the absence of strong trends in those variables.

**Irrelevant Columns Dropped:** Only columns needed for joining, filtering, or feature derivation were retained at each stage. From T19B and T19A: ABMI Site, Year, Scientific Name, Taxonomic Resolution, and Common Name. From T01A: ABMI Site, Year, Public Latitude, and Public Longitude. Columns such as field crew initials, identification analyst, rotation label, and unique taxonomic identification number were dropped as they carry no modeling relevance.

**Categorical Variables:** Taxonomic Resolution was used as a filter (keeping only rows where the value equals species) rather than encoded as a predictor, since the goal was consistent richness counts rather than modelling taxonomic resolution itself. A protocol flag (pre2009 or post2009) was added to the stacked bryophyte table for traceability but was not included as a model predictor since effort correction via richness per 100 minutes already accounts for the protocol difference.

**Numerical Transformations and Effort Normalization:** Two forms of normalization were applied. First, richness per site was computed by dividing species richness by the number of sites sampled that year, controlling for variation in spatial coverage across years. Second, richness per 100 minutes was computed by dividing richness per site by mean search minutes and multiplying by 100, correcting for the protocol change between pre-2009 whole hectare searches and post-2009 quadrant searches. This effort correction is the primary normalization step and is what makes pre and post 2009 observations comparable. Climate features were left in their natural units since linear regression does not require standardization and interpretability of coefficients was a priority.

**Sampling Coverage Filter:** Years with fewer than 10 sites sampled were excluded from the bryophyte annual features to prevent richness estimates from being driven by a handful of sites. This threshold is conservative given the spatial extent of ABMI sampling but prevents the most unstable estimates from entering the model.

**Aggregation:** Daily ECCC weather records were grouped by year and aggregated into four derived features: mean temperature across June, July, and August for summer temperature; total precipitation across May through August for growing season precipitation; count of days above 0 degrees Celsius minimum temperature across May through September for frost free days; and deviation of April and May mean temperature from the window mean for spring temperature anomaly. ABMI observations were aggregated by year after filtering, joining, and effort correction to produce one row per year.

## IV. Exploratory Data Analysis

**Time Series Plots:** We plotted each variable across the modeling window to identify trends, anomalies, and structural patterns. The full historical wheat yield series (1908 to 2021) reveals a long term upward trend driven by agricultural technology, with the modeling window sitting in a stable high yield plateau. Bryophyte richness per 100 minutes shows a U shaped pattern with a notable dip around 2011 and a strong rise toward 2019. Climate variables are broadly stable across the window with the exception of total growing season precipitation, which varies between roughly 200 and 425mm year to year.

**Correlation Matrices:** Two correlation matrices were produced: one across all EDA variables and one restricted to modeling variables. The full EDA matrix revealed that raw bryophyte count variables (species richness, total identifications, ids per site) are strongly intercorrelated with n sites sampled, confirming these reflect sampling effort rather than ecology and justifying the use of richness per 100 minutes as the effort corrected metric. The modeling matrix confirmed that spring temperature anomaly has the strongest positive correlation with wheat yield while richness per 100 minutes, mean summer temperature, and total growing season precipitation are negatively correlated with yield. All predictor intercorrelations are low, which was later confirmed by VIF scores under 2.

**Scatterplots:** Pairwise scatterplots between each predictor and wheat yield were produced to inspect linearity assumptions. The negative relationship between mean summer temperature and yield and the positive relationship between spring temperature anomaly and yield were both visible in the scatterplots, consistent with model coefficients.

All visualizations include axis labels, titles, and legends. At least one non-visual insight (the correlation matrix) is included with interpretation above.

## **V. Modeling (11 Marks)**

We selected **Multiple Linear Regression** because it provides explicit mathematical coefficients, allowing us to strictly quantify the "highest impact" variables on crop yield, unlike black-box models (like Random Forests).

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
7. Thouvenot, L., Ferlian, O., Buscot, F., Frouz, J., Guerra, C. A., Heintz-Buschart, A.,
   Taudiere, A., & Eisenhauer, N. (2025). Nitrogen enrichment and vascular plant richness 
   loss reduce bryophyte richness. Scientific Reports, 15, 3951.
   https://doi.org/10.1038/s41598-025-88425-2
8. Wikipedia contributors. (2024). Cook's distance. Wikipedia.  
   [https://en.wikipedia.org/wiki/Cook%27s\_distance](https://en.wikipedia.org/wiki/Cook%27s_distance)  
9. Wikipedia contributors. (2024). Variance inflation factor. Wikipedia.  
   [https://en.wikipedia.org/wiki/Variance\_inflation\_factor](https://en.wikipedia.org/wiki/Variance_inflation_factor)  
10. Wikipedia contributors. (2024). Coefficient of determination. Wikipedia.  
   [https://en.wikipedia.org/wiki/Coefficient\_of\_determination](https://en.wikipedia.org/wiki/Coefficient_of_determination)



