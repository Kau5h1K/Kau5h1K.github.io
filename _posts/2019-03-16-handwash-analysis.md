---
layout: post
title: "Dr. Semmelweis and the Discovery of Handwashing"
tags: [ Project, python, pandas, Statistical Thinking in Python, Bootstrap analysis]
date: 2019-03-16
excerpt: "In 1847 the Hungarian physician Ignaz Semmelweis makes a breakthrough discovery: He discovers handwashing. Contaminated hands was a major cause of childbed fever and by enforcing handwashing at his hospital he saved hundreds of lives.<br/>
In this python project, I have reanalyzed the medical data Semmelweis collected."
comments: True
project: True
---

---
## Data
This project uses a dataset which is available [here](https://github.com/Kau5h1K/handwash-analysis/tree/master/datasets).

---
## 1. Meet Dr. Ignaz Semmelweis
<p><img style="float: left;margin:5px 20px 5px 1px" src="https://s3.amazonaws.com/assets.datacamp.com/production/project_20/img/ignaz_semmelweis_1860.jpeg"></p>
<!--
<img style="float: left;margin:5px 20px 5px 1px" src="https://s3.amazonaws.com/assets.datacamp.com/production/project_20/datasets/ignaz_semmelweis_1860.jpeg">
-->
<p>This is Dr. Ignaz Semmelweis, a Hungarian physician born in 1818 and active at the Vienna General Hospital. If Dr. Semmelweis looks troubled it's probably because he's thinking about <em>childbed fever</em>: A deadly disease affecting women that just have given birth. He is thinking about it because in the early 1840s at the Vienna General Hospital as many as 10% of the women giving birth die from it. He is thinking about it because he knows the cause of childbed fever: It's the contaminated hands of the doctors delivering the babies. And they won't listen to him and <em>wash their hands</em>!</p>
<p>In this notebook, we're going to reanalyze the data that made Semmelweis discover the importance of <em>handwashing</em>. Let's start by looking at the data that made Semmelweis realize that something was wrong with the procedures at Vienna General Hospital.</p>


```python
# importing modules
import pandas as pd
# Read datasets/yearly_deaths_by_clinic.csv into yearly
yearly = pd.read_csv('datasets/yearly_deaths_by_clinic.csv')

# Print out yearly
print(yearly)
```

        year  births  deaths    clinic
    0   1841    3036     237  clinic 1
    1   1842    3287     518  clinic 1
    2   1843    3060     274  clinic 1
    3   1844    3157     260  clinic 1
    4   1845    3492     241  clinic 1
    5   1846    4010     459  clinic 1
    6   1841    2442      86  clinic 2
    7   1842    2659     202  clinic 2
    8   1843    2739     164  clinic 2
    9   1844    2956      68  clinic 2
    10  1845    3241      66  clinic 2
    11  1846    3754     105  clinic 2

---
## 2. The alarming number of deaths
<p>The table above shows the number of women giving birth at the two clinics at the Vienna General Hospital for the years 1841 to 1846. You'll notice that giving birth was very dangerous; an <em>alarming</em> number of women died as the result of childbirth, most of them from childbed fever.</p>
<p>We see this more clearly if we look at the <em>proportion of deaths</em> out of the number of women giving birth. Let's zoom in on the proportion of deaths at Clinic 1.</p>


```python
# Calculate proportion of deaths per no. births
yearly = yearly.assign(proportion_deaths = yearly['deaths']/yearly['births'])
# Extract clinic 1 data into yearly1 and clinic 2 data into yearly2
yearly1 = yearly[yearly.clinic.str.contains('clinic 1')]
yearly2 = yearly[yearly.clinic.str.contains('clinic 2')]

# Print out yearly1
print(yearly)
```

        year  births  deaths    clinic  proportion_deaths
    0   1841    3036     237  clinic 1           0.078063
    1   1842    3287     518  clinic 1           0.157591
    2   1843    3060     274  clinic 1           0.089542
    3   1844    3157     260  clinic 1           0.082357
    4   1845    3492     241  clinic 1           0.069015
    5   1846    4010     459  clinic 1           0.114464
    6   1841    2442      86  clinic 2           0.035217
    7   1842    2659     202  clinic 2           0.075968
    8   1843    2739     164  clinic 2           0.059876
    9   1844    2956      68  clinic 2           0.023004
    10  1845    3241      66  clinic 2           0.020364
    11  1846    3754     105  clinic 2           0.027970

---
## 3. Death at the clinics
<p>If we now plot the proportion of deaths at both clinic 1 and clinic 2  we'll see a curious pattern...</p>


```python
# This makes plots appear in the notebook
%matplotlib inline

# Plot yearly proportion of deaths at the two clinics
ax = yearly1.plot('year','proportion_deaths',label = 'clinic 1')
yearly2.plot('year','proportion_deaths',label = 'clinic 2',ax=ax)
ax.set_ylabel("Proportion deaths")
```




    <matplotlib.text.Text at 0x7ff86caad6d8>




![png](/assets/img/handwash-analysis_files/handwash-analysis_5_1.png)

---
## 4. The handwashing begins
<p>Why is the proportion of deaths constantly so much higher in Clinic 1? Semmelweis saw the same pattern and was puzzled and distressed. The only difference between the clinics was that many medical students served at Clinic 1, while mostly midwife students served at Clinic 2. While the midwives only tended to the women giving birth, the medical students also spent time in the autopsy rooms examining corpses. </p>
<p>Semmelweis started to suspect that something on the corpses, spread from the hands of the medical students, caused childbed fever. So in a desperate attempt to stop the high mortality rates, he decreed: <em>Wash your hands!</em> This was an unorthodox and controversial request, nobody in Vienna knew about bacteria at this point in time. </p>
<p>Let's load in monthly data from Clinic 1 to see if the handwashing had any effect.</p>


```python
# Read datasets/monthly_deaths.csv into monthly
monthly = pd.read_csv('datasets/monthly_deaths.csv',parse_dates=['date'])

# Calculate proportion of deaths per no. births
monthly = monthly.assign(proportion_deaths = monthly['deaths']/monthly['births'])
# Print out the first rows in monthly
print(monthly.head())
```

            date  births  deaths  proportion_deaths
    0 1841-01-01     254      37           0.145669
    1 1841-02-01     239      18           0.075314
    2 1841-03-01     277      12           0.043321
    3 1841-04-01     255       4           0.015686
    4 1841-05-01     255       2           0.007843

---
## 5. The effect of handwashing
<p>With the data loaded we can now look at the proportion of deaths over time. In the plot below I haven't marked where obligatory handwashing started, but it reduced the proportion of deaths to such a degree that one should be able to spot it!</p>


```python
# Plot monthly proportion of deaths
ax = monthly.plot('date','proportion_deaths')
ax.set_ylabel('Proportion deaths')
```




    <matplotlib.text.Text at 0x7ff86cb75240>




![png](/assets/img/handwash-analysis_files/handwash-analysis_9_1.png)

---
## 6. The effect of handwashing highlighted
<p>Starting from the summer of 1847 the proportion of deaths is drastically reduced and, yes, this was when Semmelweis made handwashing obligatory. </p>
<p>The effect of handwashing is made even more clear if I highlight this in the graph.</p>


```python
# Date when handwashing was made mandatory
import pandas as pd
handwashing_start = pd.to_datetime('1847-06-01')

# Split monthly into before and after handwashing_start
before_washing = monthly.loc[monthly.date < handwashing_start]
after_washing = monthly.loc[monthly.date >= handwashing_start]

# Plot monthly proportion of deaths before and after handwashing
ax = before_washing.plot('date','proportion_deaths',label='before washing')
after_washing.plot('date','proportion_deaths',label='after washing',ax=ax)
ax.set_ylabel('Proportion deaths')
```




    <matplotlib.text.Text at 0x7ff86cb07c18>




![png](/assets/img/handwash-analysis_files/handwash-analysis_11_1.png)

---
## 7. More handwashing, fewer deaths?
<p>Again, the graph shows that handwashing had a huge effect. How much did it reduce the monthly proportion of deaths on average?</p>


```python
# Difference in mean monthly proportion of deaths due to handwashing
before_proportion = before_washing.loc[:,'proportion_deaths']
after_proportion = after_washing.loc[:,'proportion_deaths']
mean_diff = after_proportion.mean()-before_proportion.mean()
mean_diff
```




    -0.08395660751183336


---
## 8. A Bootstrap analysis of Semmelweis handwashing data
<p>It reduced the proportion of deaths by around 8 percentage points! From 10% on average to just 2% (which is still a high number by modern standards). </p>
<p>To get a feeling for the uncertainty around how much handwashing reduces mortalities we could look at a confidence interval (here calculated using the bootstrap method).</p>


```python
# A bootstrap analysis of the reduction of deaths due to handwashing
boot_mean_diff = []
for i in range(3000):
    boot_before = before_proportion.sample(frac=1,replace=True)
    boot_after = after_proportion.sample(frac=1,replace=True)
    boot_mean_diff.append( boot_after.mean()-boot_before.mean())

# Calculating a 95% confidence interval from boot_mean_diff
confidence_interval = pd.Series(boot_mean_diff).quantile([0.025,0.975])
confidence_interval

```




    0.025   -0.101545
    0.975   -0.067406
    dtype: float64


---
## 9. The fate of Dr. Semmelweis
<p>So handwashing reduced the proportion of deaths by between 6.7 and 10 percentage points, according to a 95% confidence interval. All in all, it would seem that Semmelweis had solid evidence that handwashing was a simple but highly effective procedure that could save many lives.</p>
<p>The tragedy is that, despite the evidence, Semmelweis' theory — that childbed fever was caused by some "substance" (what we today know as <em>bacteria</em>) from autopsy room corpses — was ridiculed by contemporary scientists. The medical community largely rejected his discovery and in 1849 he was forced to leave the Vienna General Hospital for good.</p>
<p>One reason for this was that statistics and statistical arguments were uncommon in medical science in the 1800s. Semmelweis only published his data as long tables of raw data, but he didn't show any graphs nor confidence intervals. If he would have had access to the analysis we've just put together he might have been more successful in getting the Viennese doctors to wash their hands.</p>


```python
# The data Semmelweis collected points to that:
doctors_should_wash_their_hands = True
```

---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/handwash-analysis).
{: .notice}
