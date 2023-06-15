# US-Power-Outage-Modeling
Project for DSC 80

### Framing the Problem

Cleaned dataset with relevant columns:

|   OBS |   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | CLIMATE.REGION     | SEASON   |   RES.PRICE | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   OUTAGE.DURATION |
|------:|-------:|--------:|:-------------|:--------------|:-------------------|:---------|------------:|:-------------------|:-------------------|-----------------:|---------------------:|------------------:|
|     1 |   2011 |       7 | Minnesota    | MN            | East North Central | Summer   |       11.6  | normal             | severe weather     |              300 |                70000 |        51         |
|     2 |   2014 |       5 | Minnesota    | MN            | East North Central | Spring   |       12.12 | normal             | intentional attack |              675 |                65000 |         0.0166667 |
|     3 |   2010 |      10 | Minnesota    | MN            | East North Central | Fall     |       10.87 | cold               | severe weather     |             1024 |                70000 |        50         |
|     4 |   2012 |       6 | Minnesota    | MN            | East North Central | Summer   |       11.79 | normal             | severe weather     |               56 |                68200 |        42.5       |
|     5 |   2015 |       7 | Minnesota    | MN            | East North Central | Summer   |       13.07 | warm               | severe weather     |              250 |               250000 |        29         |

Our exploratory data analysis on this dataset can be found [here].(https://ancientabacus.github.io/US-Power-Outage-Trends/)

### Baseline Model

### Final Model

### Fairness Analysis

Group X is California
Group Y is not California

Permutation Test:
Null Hypothesis: Our model is fair. Its precision for outage duration in California and not in California are roughly the same, and any differences are due to random chance.
Alternative Hypothesis: Our model is unfair. Its precision for outage duration not in California lower than its precision for outage duration in Louisiana.

Evaluation metric: RMSE
Test statistic: difference in RMSE
Significance level: 0.05

<iframe src="assets/FAIR_EDA.html" width=800 height=600 frameBorder=0></iframe>

We noticed that states besides California were mostly variable, so we decided to compare California to rest of US states. We chose California as Group X, not California as Group Y.

<iframe src="assets/FAIR.html" width=800 height=600 frameBorder=0></iframe>

Resulting p-value: 0.03

Conclusion: We reject the null hypothesis at the significance level of 0.05. We conclude that there is evidence to support the claim the model is unfair in predicting the severity of power outages outside of California.