---
layout: post
title: "Using Regression Discontinuity to see which debts are worth collecting"
tags: [ Project, python, pandas, Cleaning Data, Statistical learning, Regression Discontinuity, hypothesis, null hypothesis, pvalue, Statistical test, regression modeling, Graphical exploratory data analysis, R-squared, OLS, chi-square test, significance test]
date: 2019-03-24
excerpt: "Playing bank data scientist and using regression discontinuity to see which debts are worth collecting."
comments: True
project: True
---

---
## Data
This project uses a dataset which is available [here](https://github.com/Kau5h1K/password-filtering/tree/master/datasets).

---
## 1. Regression discontinuity: banking recovery
<p>After a debt has been legally declared "uncollectable" by a bank, the account is considered to be "charged-off." But that doesn't mean the bank simply <strong><em>walks away</em></strong> from the debt. They still want to collect some of the money they are owed. The bank will score the account to assess the expected recovery amount, that is, the expected amount that the bank may be able to receive from the customer in the future (for a fixed time period such as one year). This amount is a function of the probability of the customer paying, the total debt, and other factors that impact the ability and willingness to pay.</p>
<p>The bank has implemented different recovery strategies at different thresholds ($1000, $2000, etc.) where the greater the expected recovery amount, the more effort the bank puts into contacting the customer. For low recovery amounts (Level 0), the bank just adds the customer's contact information to their automatic dialer and emailing system. For higher recovery strategies, the bank incurs more costs as they leverage human resources in more efforts to contact the customer and obtain payments. Each additional level of recovery strategy requires an additional $50 per customer so that customers in the Recovery Strategy Level 1 cost the company $50 more than those in Level 0. Customers in Level 2 cost $50 more than those in Level 1, etc. </p>
<p><strong>The big question</strong>: does the extra amount that is recovered at the higher strategy level exceed the extra $50 in costs? In other words, was there a jump (also called a "discontinuity") of more than $50 in the amount recovered at the higher strategy level? We'll find out in this notebook.</p>
<p><img src="https://assets.datacamp.com/production/project_504/img/Regression%20Discontinuity%20graph.png" alt="Regression discontinuity graph" width="600px"></p>
<p>First, we'll load the banking dataset and look at the first few rows of data. This puts us in a good position to understand the dataset itself and begin thinking about how to analyze the data.</p>


```python
# Import modules
import pandas as pd
import numpy as np
# Read in dataset
df = pd.read_csv('datasets/bank_data.csv')

# Print the first few rows of the DataFrame
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>expected_recovery_amount</th>
      <th>actual_recovery_amount</th>
      <th>recovery_strategy</th>
      <th>age</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2030</td>
      <td>194</td>
      <td>263.540</td>
      <td>Level 0 Recovery</td>
      <td>19</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1150</td>
      <td>486</td>
      <td>416.090</td>
      <td>Level 0 Recovery</td>
      <td>25</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>380</td>
      <td>527</td>
      <td>429.350</td>
      <td>Level 0 Recovery</td>
      <td>27</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1838</td>
      <td>536</td>
      <td>296.990</td>
      <td>Level 0 Recovery</td>
      <td>25</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1995</td>
      <td>541</td>
      <td>346.385</td>
      <td>Level 0 Recovery</td>
      <td>34</td>
      <td>Male</td>
    </tr>
  </tbody>
</table>
</div>


---
## 2. Graphical exploratory data analysis
<p>The bank has implemented different recovery strategies at different thresholds ($1000, $2000, $3000 and $5000) where the greater the Expected Recovery Amount, the more effort the bank puts into contacting the customer. Zeroing in on the first transition (between Level 0 and Level 1) means we are focused on the population with Expected Recovery Amounts between $0 and $2000 where the transition between Levels occurred at $1000. We know that the customers in Level 1 (expected recovery amounts between $1001 and $2000) received more attention from the bank and, by definition, they had higher Expected Recovery Amounts than the customers in Level 0 (between $1 and $1000).</p>
<p>Here's a quick summary of the Levels and thresholds again:</p>
<ul>
<li>Level 0: Expected recovery amounts &gt;$0 and &lt;=$1000</li>
<li>Level 1: Expected recovery amounts &gt;$1000 and &lt;=$2000</li>
<li>The threshold of $1000 separates Level 0 from Level 1</li>
</ul>
<p>A key question is whether there are other factors besides Expected Recovery Amount that also varied systematically across the $1000 threshold. For example, does the customer age show a jump (discontinuity) at the $1000 threshold or does that age vary smoothly? We can examine this by first making a scatter plot of the age as a function of Expected Recovery Amount for a small window of Expected Recovery Amount, $0 to $2000. This range covers Levels 0 and 1.</p>


```python
# Scatter plot of Age vs. Expected Recovery Amount
from matplotlib import pyplot as plt
%matplotlib inline
plt.scatter(x=df['expected_recovery_amount'], y=df['age'], c="g", s=2)
plt.xlim(0, 2000)
plt.ylim(0, 60)
plt.xlabel('Expected Recovery Amount')
plt.ylabel('Age')
plt.legend(loc=2)
plt.show()
```


![png](/assets/img/nodefault-debts_files/nodefault-debts_3_0.png)

---
## 3. Statistical test:  age vs. expected recovery amount
<p>We want to convince ourselves that variables such as age and sex are similar above and below the $1000 Expected Recovery Amount threshold. This is important because we want to be able to conclude that differences in the actual recovery amount are due to the higher Recovery Strategy and not due to some other difference like age or sex.</p>
<p>The scatter plot of age versus Expected Recovery Amount did not show an obvious jump around $1000.  We will be more confident in our conclusions if we do statistical analysis examining the average age of the customers just above and just below the threshold. We can start by exploring the range from $900 to $1100.</p>
<p>For determining if there is a difference in the ages just above and just below the threshold, we will use the Kruskal-Wallis test which is a statistical test that makes no distributional assumptions.</p>


```python
# Import stats module
from scipy import stats

# Compute average age just below and above the threshold
era_900_1100 = df.loc[(df['expected_recovery_amount']<1100) &
                      (df['expected_recovery_amount']>=900)]
by_recovery_strategy = era_900_1100.groupby(['recovery_strategy'])
by_recovery_strategy['age'].describe().unstack()

# Perform Kruskal-Wallis test
Level_0_age = era_900_1100.loc[df['recovery_strategy']=="Level 0 Recovery"]['age']
Level_1_age = era_900_1100.loc[df['recovery_strategy']=="Level 1 Recovery"]['age']
stats.kruskal(Level_0_age,Level_1_age)
```




    KruskalResult(statistic=3.4572342749517513, pvalue=0.06297556896097407)


---
## 4. Statistical test:  sex vs. expected recovery amount
<p>We were able to convince ourselves that there is no major jump in the average customer age just above and just
below the $1000 threshold by doing a statistical test as well as exploring it graphically with a scatter plot.  </p>
<p>We want to also test that the percentage of customers that are male does not jump as well across the $1000 threshold. We can start by exploring the range of $900 to $1100 and later adjust this range.</p>
<p>We can examine this question statistically by developing cross-tabs as well as doing chi-square tests of the percentage of customers that are male vs. female.</p>


```python
# Number of customers in each category
crosstab = pd.crosstab(df.loc[(df['expected_recovery_amount']<2000) &
                              (df['expected_recovery_amount']>=0)]['recovery_strategy'],
                       df['sex'])
# Chi-square test
chi2_stat, p_val, dof, ex = stats.chi2_contingency(crosstab)
p_val
```




    0.3941650543686612


---
## 5. Exploratory graphical analysis: recovery amount
<p>We are now reasonably confident that customers just above and just below the $1000 threshold are, on average, similar in terms of their average age and the percentage that are male.  </p>
<p>It is now time to focus on the key outcome of interest, the actual recovery amount.</p>
<p>A first step in examining the relationship between the actual recovery amount and the expected recovery amount is to develop a scatter plot where we want to focus our attention at the range just below and just above the threshold. Specifically, we will develop a scatter plot of Expected Recovery Amount (Y) vs. Actual Recovery Amount (X) for Expected Recovery Amounts between $900 to $1100.  This range covers Levels 0 and 1.  A key question is whether or not we see a discontinuity (jump) around the $1000 threshold.</p>


```python
# Scatter plot of Actual Recovery Amount vs. Expected Recovery Amount
plt.scatter(x=df['expected_recovery_amount'], y=df['actual_recovery_amount'], c="g", s=2)
plt.xlim(900, 1100)
plt.ylim(0, 2000)
plt.xlabel("Expected Recovery Amount")
plt.ylabel("Actual Recovery Amount")
plt.legend(loc=2)
plt.show()
```


![png](/assets/img/nodefault-debts_files/nodefault-debts_9_0.png)

---
## 6. Statistical analysis:  recovery amount
<p>Just as we did with age, we can perform statistical tests to see if the actual recovery amount has a discontinuity above the $1000 threshold. We are going to do this for two different windows of the expected recovery amount $900 to $1100 and for a narrow range of $950 to $1050 to see if our results are consistent.</p>
<p>Again, the statistical test we will use is the Kruskal-Wallis test, a test that makes no assumptions about the distribution of the actual recovery amount.</p>
<p>We will first compute the average actual recovery amount for those customers just below and just above the threshold using a range from $900 to $1100.  Then we will perform a Kruskal-Wallis test to see if the actual recovery amounts are different just above and just below the threshold.  Once we do that, we will repeat these steps for a smaller window of $950 to $1050.</p>


```python
# Compute average actual recovery amount just below and above the threshold
by_recovery_strategy['actual_recovery_amount'].describe().unstack()

# Perform Kruskal-Wallis test
Level_0_actual = era_900_1100.loc[df['recovery_strategy']=='Level 0 Recovery']['actual_recovery_amount']
Level_1_actual = era_900_1100.loc[df['recovery_strategy']=='Level 1 Recovery']['actual_recovery_amount']
print(stats.kruskal(Level_0_actual,Level_1_actual))

# Repeat for a smaller range of $950 to $1050
era_950_1050 = df.loc[(df['expected_recovery_amount']<1050) &
                      (df['expected_recovery_amount']>=950)]
Level_0_actual = era_950_1050.loc[df['recovery_strategy']=='Level 0 Recovery']['actual_recovery_amount']
Level_1_actual = era_950_1050.loc[df['recovery_strategy']=='Level 1 Recovery']['actual_recovery_amount']
print(stats.kruskal(Level_0_actual,Level_1_actual))
```

    KruskalResult(statistic=65.37966302528878, pvalue=6.177308752803109e-16)
    KruskalResult(statistic=30.246000000000038, pvalue=3.80575314300276e-08)

---
## 7. Regression modeling: no threshold
<p>We now want to take a regression-based approach to estimate the impact of the program at the $1000 threshold using the data that is just above and just below the threshold. In order to do that, we will build two models.  The first model does not have a threshold while the second model will include a threshold.</p>
<p>The first model predicts the actual recovery amount (outcome or dependent variable) as a function of the expected recovery amount (input or independent variable). We expect that there will be a strong positive relationship between these two variables.  </p>
<p>We will examine the adjusted R-squared to see the percent of variance that is explained by the model.  In this model, we are not trying to represent the threshold but simply trying to see how the variable used for assigning the customers (expected recovery amount) relates to the outcome variable (actual recovery amount).</p>


```python
# Import statsmodels
import statsmodels.api as sm

# Define X and y
X = era_900_1100['expected_recovery_amount']
y = era_900_1100['actual_recovery_amount']
X = sm.add_constant(X)

# Build linear regression model
model = sm.OLS(y, X).fit()
predictions = model.predict(X)

# Print out the model summary statistics
model.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>actual_recovery_amount</td> <th>  R-squared:         </th> <td>   0.261</td>
</tr>
<tr>
  <th>Model:</th>                      <td>OLS</td>          <th>  Adj. R-squared:    </th> <td>   0.256</td>
</tr>
<tr>
  <th>Method:</th>                <td>Least Squares</td>     <th>  F-statistic:       </th> <td>   63.78</td>
</tr>
<tr>
  <th>Date:</th>                <td>Sun, 24 Mar 2019</td>    <th>  Prob (F-statistic):</th> <td>1.56e-13</td>
</tr>
<tr>
  <th>Time:</th>                    <td>15:25:04</td>        <th>  Log-Likelihood:    </th> <td> -1278.9</td>
</tr>
<tr>
  <th>No. Observations:</th>         <td>   183</td>         <th>  AIC:               </th> <td>   2562.</td>
</tr>
<tr>
  <th>Df Residuals:</th>             <td>   181</td>         <th>  BIC:               </th> <td>   2568.</td>
</tr>
<tr>
  <th>Df Model:</th>                 <td>     1</td>         <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>         <td>nonrobust</td>       <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
              <td></td>                <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th>                    <td>-1978.7597</td> <td>  347.741</td> <td>   -5.690</td> <td> 0.000</td> <td>-2664.907</td> <td>-1292.612</td>
</tr>
<tr>
  <th>expected_recovery_amount</th> <td>    2.7577</td> <td>    0.345</td> <td>    7.986</td> <td> 0.000</td> <td>    2.076</td> <td>    3.439</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>64.493</td> <th>  Durbin-Watson:     </th> <td>   1.777</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td> 185.818</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 1.463</td> <th>  Prob(JB):          </th> <td>4.47e-41</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.977</td> <th>  Cond. No.          </th> <td>1.80e+04</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 1.8e+04. This might indicate that there are<br/>strong multicollinearity or other numerical problems.


---
## 8. Regression modeling: adding true threshold
<p>From the first model, we see that the regression coefficient is statistically significant for the expected recovery amount and the adjusted R-squared value was about 0.26. As we saw from the graph, on average the actual recovery amount increases as the expected recovery amount increases. We could add polynomial terms of expected recovery amount (such as the squared value of expected recovery amount) to the model but, for the purposes of this practice, let's stick with using just the linear term.</p>
<p>The second model adds an indicator of the true threshold to the model. If there was no impact of the higher recovery strategy on the actual recovery amount, then we would expect that the relationship between the expected recovery amount and the actual recovery amount would be continuous.     </p>
<p>In this case, we know the true threshold is at $1000.  </p>
<p>We will create an indicator variable (either a 0 or a 1) that represents whether or not the expected recovery amount was greater than $1000. When we add the true threshold to the model, the regression coefficient for the true threshold represents the additional amount recovered due to the higher recovery strategy.  That is to say, the regression coefficient for the true threshold measures the size of the discontinuity for customers just above and just below the threshold.</p>
<p>If the higher recovery strategy did help recovery more money, then the regression coefficient of the true threshold will be greater than zero.  If the higher recovery strategy did not help recover more money than the regression coefficient will not be statistically significant.</p>


```python
# Create indicator (0 or 1) for expected recovery amount >= $1000
df['indicator_1000'] = np.where(df['expected_recovery_amount']<1000, 0, 1)
era_900_1100 = df.loc[(df['expected_recovery_amount']<1100) &
                      (df['expected_recovery_amount']>=900)]

# Define X and y
X = era_900_1100.loc[:,['expected_recovery_amount','indicator_1000']]
y = era_900_1100['actual_recovery_amount']
X = sm.add_constant(X)

# Build linear regression model
model = sm.OLS(y,X).fit()

# Print the model summary
model.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>actual_recovery_amount</td> <th>  R-squared:         </th> <td>   0.314</td>
</tr>
<tr>
  <th>Model:</th>                      <td>OLS</td>          <th>  Adj. R-squared:    </th> <td>   0.307</td>
</tr>
<tr>
  <th>Method:</th>                <td>Least Squares</td>     <th>  F-statistic:       </th> <td>   41.22</td>
</tr>
<tr>
  <th>Date:</th>                <td>Sun, 24 Mar 2019</td>    <th>  Prob (F-statistic):</th> <td>1.83e-15</td>
</tr>
<tr>
  <th>Time:</th>                    <td>15:25:04</td>        <th>  Log-Likelihood:    </th> <td> -1272.0</td>
</tr>
<tr>
  <th>No. Observations:</th>         <td>   183</td>         <th>  AIC:               </th> <td>   2550.</td>
</tr>
<tr>
  <th>Df Residuals:</th>             <td>   180</td>         <th>  BIC:               </th> <td>   2560.</td>
</tr>
<tr>
  <th>Df Model:</th>                 <td>     2</td>         <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>         <td>nonrobust</td>       <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
              <td></td>                <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th>                    <td>    3.3440</td> <td>  626.274</td> <td>    0.005</td> <td> 0.996</td> <td>-1232.440</td> <td> 1239.128</td>
</tr>
<tr>
  <th>expected_recovery_amount</th> <td>    0.6430</td> <td>    0.655</td> <td>    0.981</td> <td> 0.328</td> <td>   -0.650</td> <td>    1.936</td>
</tr>
<tr>
  <th>indicator_1000</th>           <td>  277.6344</td> <td>   74.043</td> <td>    3.750</td> <td> 0.000</td> <td>  131.530</td> <td>  423.739</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>65.977</td> <th>  Durbin-Watson:     </th> <td>   1.906</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td> 186.537</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 1.510</td> <th>  Prob(JB):          </th> <td>3.12e-41</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.917</td> <th>  Cond. No.          </th> <td>3.37e+04</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 3.37e+04. This might indicate that there are<br/>strong multicollinearity or other numerical problems.


---
## 9. Regression modeling: adjusting the window
<p>The regression coefficient for the true threshold was statistically significant with an estimated impact of around $278 and a 95 percent confidence interval of $132 to $424.  This is much larger than the incremental cost of running the higher recovery strategy which was $50 per customer. At this point, we are feeling reasonably confident that the higher recovery strategy is worth the additional costs of the program for customers just above and just below the threshold.  </p>
<p>Before showing this to our managers, we want to convince ourselves that this result wasn't due just to us choosing a window of $900 to $1100 for the expected recovery amount. If the higher recovery strategy really had an impact of an extra few hundred dollars, then we should see a similar regression coefficient if we choose a slightly bigger or a slightly smaller window for the expected recovery amount.  Let's repeat this analysis for the window of expected recovery amount from $950 to $1050 to see if we get similar results.</p>
<p>The answer? Whether we use a wide window ($900 to $1100) or a narrower window ($950 to $1050), the incremental recovery amount at the higher recovery strategy is much greater than the $50 per customer it costs for the higher recovery strategy.  So we can say that the higher recovery strategy is worth the extra $50 per customer that the bank is spending.</p>


```python
# Redefine era_950_1050 so the indicator variable is included
era_950_1050 = df.loc[(df['expected_recovery_amount']<1050) &
                      (df['expected_recovery_amount']>=950)]

# Define X and y
X = era_950_1050[['expected_recovery_amount','indicator_1000']]
y = era_950_1050['actual_recovery_amount']
X = sm.add_constant(X)

# Build linear regression model
model = sm.OLS(y,X).fit()

# Print the model summary
model.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>actual_recovery_amount</td> <th>  R-squared:         </th> <td>   0.283</td>
</tr>
<tr>
  <th>Model:</th>                      <td>OLS</td>          <th>  Adj. R-squared:    </th> <td>   0.269</td>
</tr>
<tr>
  <th>Method:</th>                <td>Least Squares</td>     <th>  F-statistic:       </th> <td>   18.99</td>
</tr>
<tr>
  <th>Date:</th>                <td>Sun, 24 Mar 2019</td>    <th>  Prob (F-statistic):</th> <td>1.12e-07</td>
</tr>
<tr>
  <th>Time:</th>                    <td>15:25:04</td>        <th>  Log-Likelihood:    </th> <td> -692.92</td>
</tr>
<tr>
  <th>No. Observations:</th>         <td>    99</td>         <th>  AIC:               </th> <td>   1392.</td>
</tr>
<tr>
  <th>Df Residuals:</th>             <td>    96</td>         <th>  BIC:               </th> <td>   1400.</td>
</tr>
<tr>
  <th>Df Model:</th>                 <td>     2</td>         <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>         <td>nonrobust</td>       <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
              <td></td>                <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th>                    <td> -279.5243</td> <td> 1840.707</td> <td>   -0.152</td> <td> 0.880</td> <td>-3933.298</td> <td> 3374.250</td>
</tr>
<tr>
  <th>expected_recovery_amount</th> <td>    0.9189</td> <td>    1.886</td> <td>    0.487</td> <td> 0.627</td> <td>   -2.825</td> <td>    4.663</td>
</tr>
<tr>
  <th>indicator_1000</th>           <td>  286.5337</td> <td>  111.352</td> <td>    2.573</td> <td> 0.012</td> <td>   65.502</td> <td>  507.566</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>39.302</td> <th>  Durbin-Watson:     </th> <td>   1.955</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  82.258</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 1.564</td> <th>  Prob(JB):          </th> <td>1.37e-18</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.186</td> <th>  Cond. No.          </th> <td>6.81e+04</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 6.81e+04. This might indicate that there are<br/>strong multicollinearity or other numerical problems.


---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/nodefault-debts).
{: .notice}
