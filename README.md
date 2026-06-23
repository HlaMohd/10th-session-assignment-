#  Applied Statistics --- Assignment 10

### Chess Dataset & WHO Dataset Analysis (Full Report with Code, Outputs & Interpretation)

------------------------------------------------------------------------

#  1. Project Overview

This project applies statistical analysis techniques on two datasets: -
Chess games dataset - WHO life expectancy dataset

We perform: - Descriptive statistics - Distribution analysis - Normality
testing - Correlation analysis - Chi-square testing - Confidence
intervals (parametric + bootstrap)

------------------------------------------------------------------------

#  AQ1 --- Descriptive Statistics

##  1.1 Rating Difference (rating_diff)

###  Code

``` python
chess['rating_diff'].describe()
chess['rating_diff'].skew()
chess['rating_diff'].quantile(0.75) - chess['rating_diff'].quantile(0.25)
```

###  Output

    count    20058.000000
    mean       173.091435
    std        179.214854
    min          0.000000
    25%         45.000000
    50%        115.000000
    75%        241.000000
    max       1605.000000
    Skew = 1.9487
    IQR = 196

###  Interpretation

-   Strong right skew (1.95)
-   Mean \>\> median → presence of extreme mismatches
-   Most games are balanced, but few outliers inflate the mean

------------------------------------------------------------------------

##  1.2 Turns

###  Code

``` python
chess['turns'].describe()
chess['turns'].skew()
chess['turns'].quantile(0.75) - chess['turns'].quantile(0.25)
```

###  Output

    mean = 60.46
    median = 55
    std = 33.57
    min = 1
    max = 349
    Skew = 0.897
    IQR = 42

###  Interpretation

-   Moderately right-skewed
-   Most games are short (typical \~55 moves)
-   Few long games increase the average

------------------------------------------------------------------------

#  AQ2 --- Distribution Analysis

##  2.1 Normality Test (Shapiro-Wilk)

###  Code

``` python
stats.shapiro(chess['turns'].sample(1000))
stats.shapiro(chess['rating_diff'].sample(1000))
```

###  Output

    Shapiro-Wilk p = 0.000000
    Shapiro-Wilk p = 0.000000

###  Interpretation

-   p \< 0.05 → reject normality
-   Data is NOT normally distributed
-   Large dataset increases sensitivity of test

------------------------------------------------------------------------

##  2.2 Log Transformation

###  Code

``` python
np.log1p(chess['turns'])
np.log1p(chess['rating_diff'])
```

###  Output

    Turns skew (original): 0.897
    Turns skew (log): -1.37

    Rating_diff skew (original): 1.95
    Rating_diff skew (log): -0.90

###  Interpretation

-   Log reduces skew significantly
-   Converts multiplicative patterns into additive ones
-   Improves symmetry but does not fully normalize data

------------------------------------------------------------------------

#  AQ3 --- WHO Correlation Matrix

##  Code

``` python
who_num = who.select_dtypes(include='number')
corr = who_num.corr()
corr['Life expectancy']
```

------------------------------------------------------------------------

##  Output

###  Positive correlations

    Schooling                          0.751975
    Income composition of resources    0.724776
    BMI                                0.567694
    Diphtheria                         0.479495

###  Negative correlations

    Adult Mortality        -0.696359
    HIV/AIDS              -0.556556
    Thinness 1–19 yrs     -0.477183
    Thinness 5–9 yrs      -0.471584
    Under-five deaths     -0.222529

------------------------------------------------------------------------

##  Interpretation

-   Education strongly improves life expectancy
-   Disease-related variables reduce life expectancy
-   Correlation does NOT imply causation

###  Confounder

GDP likely influences both education and life expectancy

------------------------------------------------------------------------

#  AQ4 --- Chi-Squared Test

##  Code

``` python
table = pd.crosstab(chess['rating_group'], chess['white_win'])
stats.chi2_contingency(table)
```

------------------------------------------------------------------------

##  Output

    Chi-square = 3.672
    p-value = 0.159465
    Cramer's V = 0.014

------------------------------------------------------------------------

##  Interpretation

-   p \> 0.05 → fail to reject H0
-   No statistically significant relationship
-   Cramér's V ≈ 0 → negligible effect size

### Conclusion:

Rating group does NOT influence win rate meaningfully.

------------------------------------------------------------------------

#  AQ5 --- Confidence Intervals

##  Code (Parametric)

``` python
confidence_interval(rated_turns)
confidence_interval(unrated_turns)
```

##  Output (Parametric)

    Rated:   (61.44, 62.48)
    Unrated: (53.26, 55.28)

------------------------------------------------------------------------

##  Code (Bootstrap)

``` python
bootstrap_ci(rated_turns)
bootstrap_ci(unrated_turns)
```

##  Output (Bootstrap)

    Rated:   (61.43, 62.50)
    Unrated: (53.20, 55.30)

------------------------------------------------------------------------

##  Interpretation

-   Rated games are longer than unrated games
-   No overlap between confidence intervals
-   Bootstrap confirms parametric results → strong robustness

------------------------------------------------------------------------

#  Final Insights

-   Chess data is heavily skewed → non-normal distributions
-   Log transformation improves distribution shape
-   WHO data shows strong social/health correlations
-   Chi-square test shows no meaningful categorical association
-   Confidence intervals confirm stable differences in gameplay

------------------------------------------------------------------------

#  Final Conclusion

This analysis demonstrates how statistical methods help: - Describe data
behavior - Test hypotheses - Measure uncertainty - Validate conclusions
across multiple methods

Rated chess games are consistently longer, while WHO data highlights
strong links between education, health, and life expectancy.
