# GradientDescent_195_Project

## Everything that contains our codes and interpretations
*Files are split up for ease of documentation*\
### Preprocessing.ipynb - Erika
This file deals with all of the datasets and merges them together for the EDA. Datasets are contained in files named DatasetXData where X is a letter A-C.

Justifications and summary for our process is contained in labeled mds.

The results of preprocessing.ipynb are all put instead of the preprocessing_results folder.

### EDA.ipynb - Erika
This file performs all of the statistical computations before making the prediction models. It let us know what variable to put inside of the prediction models, and gave a gist of the outcome.

All exploratory Data Analysis is explained in the markdowns before running each function.

### Phase_B_Modeling.ipynb
This file contains all 3 models. It also has each models RMSE scores, correlation coefficients, and adjusted R^2 calculations, in order to assess their quality.

# How to run our code.

In order to run our codes you have to clone our github repository. All of the files are where they need to be. 

To run them start in this order:
* preprocessing.ipynb
* EDA.ipynb
* Phase_B_Modeling.ipynb

# Citations

1. Alberta Biodiversity Monitoring Institute. (2021). Terrestrial Field Data Collection Protocols (Abridged Version).\
https://abmi.ca/publication/601.html

2. Alberta Biodiversity Monitoring Institute. Raw Bryophytes Dataset (Rotation 1 and Rotation 2).

   Files used:
    * T01A_Site_Physical_Characteristics_2692088856810991747
    * T19B_Moss_Identification_since_2009_11290156903414781363
    
    Spatial coverage: North Saskatchewan and Red Deer Land-use Framework regions.\
    Retrieved April, 2026 from:\
    https://abmi.ca/data-portal

4. Environment and Climate Change Canada. Historical Climate Data: Daily Weather Observations (2010–2019).

    Stations used:
    * Red Deer A (Station ID 2134, 2010–2013)
    * Red Deer Regional A (Station ID 51440, 2014–2019)
      
    Parameters: daily observations (timeframe = 2), CSV format.\
    Derived variables: mean summer temperature, growing season precipitation, frost-free days, and spring temperature anomaly.\
    Retrieved April 2026 from:\
    https://climate.weather.gc.ca/climate_data/bulk_data_e.html



4. Statistics Canada. Table 32-10-0359-01  Estimated areas, yield, production, average farm price and total farm value of principal field crops, in metric and imperial units"\
    Retrieved April, 2026 from:\
    https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210035901
   
6. Wikipedia contributors. (2024). Cook's distance. Wikipedia.\
   https://en.wikipedia.org/wiki/Cook%27s_distance

7. Wikipedia contributors. (2024). Variance inflation factor. Wikipedia.\
   https://en.wikipedia.org/wiki/Variance_inflation_factor

8. Wikipedia contributors. (2024). Coefficient of determination. Wikipedia.\
   https://en.wikipedia.org/wiki/Coefficient_of_determination



