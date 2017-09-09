
# Examining Racial Discrimination in the US Job Market

### Background
Racial discrimination continues to be pervasive in cultures throughout the world. Researchers examined the level of racial discrimination in the United States labor market by randomly assigning identical résumés to black-sounding or white-sounding names and observing the impact on requests for interviews from employers.

### Data
In the dataset provided, each row represents a resume. The 'race' column has two values, 'b' and 'w', indicating black-sounding and white-sounding. The column 'call' has two values, 1 and 0, indicating whether the resume received a call from employers or not.

Note that the 'b' and 'w' values in race are assigned randomly to the resumes when presented to the employer.

<div class="span5 alert alert-info">
### Exercises
You will perform a statistical analysis to establish whether race has a significant impact on the rate of callbacks for resumes.

Answer the following questions **in this notebook below and submit to your Github account**. 

   1. What test is appropriate for this problem? Does CLT apply?
   2. What are the null and alternate hypotheses?
   3. Compute margin of error, confidence interval, and p-value.
   4. Write a story describing the statistical significance in the context or the original problem.
   5. Does your analysis mean that race/name is the most important factor in callback success? Why or why not? If not, how would you amend your analysis?

You can include written notes in notebook cells using Markdown: 
   - In the control panel at the top, choose Cell > Cell Type > Markdown
   - Markdown syntax: http://nestacms.com/docs/creating-content/markdown-cheat-sheet


#### Resources
+ Experiment information and data source: http://www.povertyactionlab.org/evaluation/discrimination-job-market-united-states
+ Scipy statistical methods: http://docs.scipy.org/doc/scipy/reference/stats.html 
+ Markdown syntax: http://nestacms.com/docs/creating-content/markdown-cheat-sheet
</div>
****


```python
import pandas as pd
import numpy as np
from scipy import stats
```


```python
data = pd.io.stata.read_stata('data/us_job_market_discrimination.dta')
```


```python
# number of callbacks for black-sounding names
sum(data[data.race=='b'].call)
```




    157.0




```python
# Establish values to needed variables
# Means
w_mean = data[data.race == 'w'].call.mean()
b_mean = data[data.race == 'b'].call.mean()
diff_mean = w_mean - b_mean

# Sample sizes
w_size = data[data.race == 'w'].call.count()
b_size = data[data.race == 'b'].call.count()

# Standard deviations
w_std = data[data.race == 'w'].call.std()
b_std = data[data.race == 'b'].call.std()

b_std
```




    0.24564945697784424




```python
## 1. What test is appropriate for this problem? Does CLT apply?
# A two-sample t-test is appropriate for this problem. And yes, the CLT (Central Limit Theorem) does apply, because we are trying to see the frequency of callbacks depending on the race; we are testing to see if this applies to the population.

## 2. What are the null and alternate hypotheses?
# H0 (null); w_avg_call - b_avg_call = 0
# H1 (alt.); w_avg_call - b_acg_call != 0

## 3. Compute margin of error, confidence interval, and p-value.
# alpha = 0.05

MSE = ((w_std**2) + (b_std**2)) / 2 # mean squared error
diff_s = ((2*MSE)/w_size) ** 0.5

# Margin of error (ME)
# With 4868 degrees of freedom, t = 1.96
t = 1.96
ME = t * diff_s # ME = 0.0153

# 95% Confidence interval
lower = diff_mean - ME
upper = diff_mean + ME
# The 95% confidence interval for the difference in callback rate between 'w' and 'b' is (0.0168, 0.0473).

# P-value

t_pval = (w_mean - b_mean) / diff_s # we got a t-score = 4.115

n = (w_size - 1) + (b_size - 1)
pval = stats.t.sf(np.abs(t_pval), n-1)*2 # p-value = 3.9402530106012533e-05, so p-value < 0.0001
```


```python
## 4. Write a story describing the statistical significance in the context or the original problem.
## 5. Does your analysis mean that race/name is the most important factor in callback success? Why or why not? If not, how would you amend your analysis?

## Question: does race have a significant impact on the rate of callbacks for resumes?

# We are 95% confident that the difference in callback rate between black- and white-sounding names is between 0.0168 and 0.0473.
# This also means that white-sounding names have a range from 1.6% to 4.7% higher callback rate than black-sounding names.

# With a low p-value < 0.0001, we reject the null hypothesis. The difference between white- and black-sounding names is statistically significant.

# According to statistical analysis, race/name is an important factor in callback success.
```
