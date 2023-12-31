# US Power Outage Severity Modeling
By Gino Angelici (gangelici@ucsd.edu) and Ifunanya Okoroma (iokoroma@ucsd.edu) <br />
A project for DSC 80: Practice and Application of Data Science taught at UC San Diego during the Spring 2023 quarter <br />
Our exploratory data analysis from Project 3 can be found [here](https://ancientabacus.github.io/US-Power-Outage-Trends/) <br />

## Framing the Problem
For the fifth and final project in the DSC 80 course, we were posed to identify a prediction problem to solve. This proposed problem came from the same dataset we analyzed in Project 3. As with Project 3, we downloaded research data on power outages in the United States from Purdue University. The data was found at this [link](https://engineering.purdue.edu/LASCI/research-data/outages/outagerisks). The data ranged from January 2000 to July 2016. The dataset contains 1534 rows (observations) and 56 columns (variables).

Prediction Problem: Predict the average duration of power outages in 2022 in hours.

Prediction Type: We decided to perform regression.

Response Variable: 'OUTAGE.DURATION' <br />
Reason: Longer power outages lead to more damage such as food spoilage, inability to work, and loss of access to utilities.

Performance Metric: Root Mean Squared Error (RMSE) <br />
Reason: RMSE is a popular choice for measuring the performance of a regression model. It represents how far the response variable unit is off during a prediction, making it easy to understand the model's accuracy.

### Cleaning
We commenced the same thorough data cleaning process that we conducted when completing Project 3. We used the same code to obtain the clean dataset once again. Here is a review of the steps we took. <br />

1. Before converting to a .csv file from a Microsoft Excel (.xlsx) file, we manually removed the first five rows and the two columns as these were not actually data entries. <br />
2. Taking a row of units and concatenating those to the column names so they would not be recognized as actual data entries. Dropping that row from the dataframe afterward. <br />
3. Combining the 'OUTAGE.START.DATE' and 'OUTAGE.START.TIME' columns to make them one column ('OUTAGE.START') to represent the time the power went out as a pandas datetime type. Then, dropping the two columns since they are not needed anymore. <br />
4. Combining the 'OUTAGE.RESTORATION.DATE' and 'OUTAGE.RESTORATION.TIME' columns to make them one column ('OUTAGE.RESTORATION') to represent the time the power was restored as a pandas datetime type. Then, dropping the two columns since they are not needed anymore. <br />
5. Manually checking each column to assess the type of the values in that column. We changed them accordingly to have them make more sense (ex: changing the type of the 'YEAR' column to an 'Int64' type instead of 'float'). <br /> 

Cleaned dataset with relevant columns:

|   OBS |   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | CLIMATE.REGION     | SEASON   |   RES.PRICE | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   OUTAGE.DURATION |
|------:|-------:|--------:|:-------------|:--------------|:-------------------|:---------|------------:|:-------------------|:-------------------|-----------------:|---------------------:|------------------:|
|     1 |   2011 |       7 | Minnesota    | MN            | East North Central | Summer   |       11.6  | normal             | severe weather     |              300 |                70000 |        51         |
|     2 |   2014 |       5 | Minnesota    | MN            | East North Central | Spring   |       12.12 | normal             | intentional attack |              675 |                65000 |         0.0166667 |
|     3 |   2010 |      10 | Minnesota    | MN            | East North Central | Fall     |       10.87 | cold               | severe weather     |             1024 |                70000 |        50         |
|     4 |   2012 |       6 | Minnesota    | MN            | East North Central | Summer   |       11.79 | normal             | severe weather     |               56 |                68200 |        42.5       |
|     5 |   2015 |       7 | Minnesota    | MN            | East North Central | Summer   |       13.07 | warm               | severe weather     |              250 |               250000 |        29         |

## Baseline Model
We both had thoughts on potential features we could use for our baseline model. With dozens of columns, we had many options at our disposal. Since we had many columns that we felt would have a great fit for our baseline model, we performed k-fold cross-validation on four different groups of two features. These features were ['SEASON', 'CUSTOMERS.AFFECTED'], ['SEASON', 'CLIMATE.CATEGORY'], ['DEMAND.LOSS.MW', 'CUSTOMERS.AFFECTED'], and ['CLIMATE.REGION', 'RES.PRICE']. Building the Pipelines for these features involved the transformation of categorical features 'SEASON', 'CLIMATE.CATEGORY', and 'CLIMATE.REGION' through the use of One-Hot Encoding. The only other individual Pipeline used was written to keep the non-categorical columns as is before running them through a regression. We needed to run a for loop to provide the outcomes of each cross-validation score. The features that would end up minimizing the RSME with a value of 99.643822 would be DEMAND.LOSS.MW and CUSTOMERS.AFFECTED. Both features are ordinal quantitative features. Setting aside the performance of the features, we feel that these features did well enough to sustain themselves to be a part of the best model because it is a greater burden on power companies to make sure the power is restored when there are more customers and therefore, a higher demand.

## Final Model
When it came to our final model, we were asked to improve upon our baseline model by using our original features along with engineering new ones. However, obtaining these features was not completed without another round of cross-validation. This time, a dataframe was created to store all of these values of the final Pipeline that stored all the preprocessing steps for 13 different features. One-Hot Encoding was applied to ['CLIMATE.REGION', 'SEASON', 'CAUSE.CATEGORY', 'U.S._STATE']. Standardization was applied to ['RES.PRICE', 'TOTAL.PRICE', 'COM.PRICE', 'IND.PRICE']. A preprocessing step to keep the feature the same was applied to 'DEMAND.LOSS.MW'. Quantile division was applied to 'RES.CUSTOMERS', 'COM.CUSTOMERS', 'TOTAL.CUSTOMERS', 'IND.CUSTOMERS'. Running this all together through one Pipeline yielded ['CLIMATE.REGION', 'RES.PRICE', 'DEMAND.LOSS.MW', 'SEASON', 'RES.CUSTOMERS'] as the best features for this model.

When it comes to the original 'CLIMATE.REGION' and 'RES.PRICE' features, those have been transformed by One-Hot Encoding and Standardization, respectfully. Some additional features include using the original 'DEMAND.LOSS.MW' column and transforming two new features, which are 'SEASON' and 'RES.CUSTOMERS'. One-Hot Encoding was applied to 'SEASON' while the 'RES.CUSTOMERS' features were to be broken up into quantiles. 'CLIMATE.REGION' and 'SEASON' are categorical features while 'RES.PRICE', 'DEMAND.LOSS.MW', and 'RES.CUSTOMERS' are ordinal quantitative features. The algorithm we applied to our model was Lasso Regression, which yielded the best results when compared to other methods such as KNeighborsRegressor and DecisionTreeRegressor.

When computing the minimum RSME, this number came out to be better than our baseline with a fold of 1 and an RMSE of 74.756655. Understanding the context of the addition of the new variables also makes sense for the model's improvement. Having a model that accounts for differences in America's weather landscape can be very beneficial. With 'CLIMATE.REGION' and 'SEASON', this implies the reality that different weather conditions can drastically affect when power can get fixed in an area. For 'RES.PRICE' and 'RES.CUSTOMERS', this also helps improve our model due to their nature of being localized to one area, just in the way the weather is. We then fit our final model to compute the fairness analysis.

## Fairness Analysis

Group X: is California <br />
Group Y: is not California

Permutation Test:

Null Hypothesis: Our model is fair. Its precision for outage duration in California and not in California is roughly the same, and any differences are due to random chance. <br />
Alternative Hypothesis: Our model is unfair. Its precision for outage duration not in California is lower than its precision for outage duration in Louisiana.

Evaluation metric: RMSE <br />
Test statistic: Difference in RMSE <br />
Significance level: 0.05

<iframe src="assets/FAIR_EDA.html" width=800 height=600 frameBorder=0></iframe>

We noticed that states besides California were mostly variable, so we decided to compare California to the rest of the US states. We chose California as Group X, not California as Group Y.

<iframe src="assets/FAIR.html" width=800 height=600 frameBorder=0></iframe>

Resulting p-value: 0.03 <br />

Conclusion: We reject the null hypothesis at the significance level of 0.05. We conclude that there is evidence to support the claim the model is unfair in predicting the severity of power outages outside of California.
