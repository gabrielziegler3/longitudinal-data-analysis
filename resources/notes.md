# TODO answer these

- when to use anova, LMM or marginal models.
- what is the difference with marginal models
- anova model
    - understand when residual is affected
- correlation and covariance
    - why are they important
    - how do they affect the analysis/model
- when to choose type 1, type 2, type 3?
- for unbalanced data, which method to use?
- Calculate correlation ICC with confidence interval


# ANOVA Design

## Balanced

Balanced designs offer the following advantages over unbalanced designs:

1. The power of an ANOVA is highest when sample sizes are equal across all treatment combinations. When the power is highest, we have the best chance of detecting differences among the means across treatment combinations when the means truly are different.

2. The overall F-statistic of the ANOVA is less sensitive to violations of the assumption of equal variance.

## Unbalanced

General approach is to use Henderson's third method

- All four estimation techniques can be used: TYPE 1, TYPE 3, ML and REML.

## Model Summary

- Moment-based estimations (GLS)
    - TYPE 1
        - More reliable for nested models
        - Used when the order of terms is known. (Does sex come before class or class comes before sex?)
    - TYPE 3
        - recommended method, but not always with nested models
        - less suitable for nested structure

- Likelihood-based estimations 
    - ML
        - When degrees of freedom for all terms in the model are large
    - REML
        - Most general accepted estimation. Except when one or more VC (variance components ?) become negative with TYPE 1/TYPE 3
        - More recent and less biased. (https://stat.ethz.ch/~meier/teaching/anova/random-and-mixed-effects-models.html)
    
NOTE: for balanced data, type 1 = type 3 = REML

i.i.d.: Independent and identically distributed random variables

# ANOVA Assumptions

- All factors are categorical variables.
- The populations have the same variance. This assumption is called the assumption of homogeneity of variance. Homoscedasticity.
- The populations are normally distributed.
- Each value is sampled independently from each other value. This assumption requires that each subject provide only one value. If a subject provides two scores, then the values are not independent. The analysis of data with two scores per subject is shown in the section on within-subjects ANOVA later in this chapter.

# Sums of Squares

In ANOVA, the term sum of squares (SS) is used to indicate variation. 
The total variation is defined as the sum of squared differences between each score and the mean of all subjects. 
The mean of all subjects is called the grand mean and is designated as µ.

## Total Sums of Squares

SSt = sum of all values from i=1 to m and j=1 to n_i (y_{ij} - ỹ_{..})²
ỹ_{..} = grand mean

Which means to take each score, subtract the grand mean from it, square the difference, and then sum up these squared values.

SSt is partitioned in two parts:

## Within group Sums of Squares

TODO

## Between group Sums of Squares

TODO

# Mean Squares

## MSw (Mean Square Within)

$MSw = SSw / dfw$

Differences among levels within the groups. MSw estimates σ_e² regardless of whether the null hypothesis is true.

## MSb (Mean Square Between)

$MSb = SSb / dfb$

Differences among the sample means. MSb only estimates σ_e² if the population means are equal. If the population means are not equal, then MSb estimates a quantity larger.
    
### F-statistic

F = MSb / MSw

# Intra Class Correlation (ICC)

ICC values can be interpreted as the total amount of variance in the dependent variable that is attributable to between-unit rather than within-unit differences over time. 
Higher values also indicate a nontrivial degree of observation nonindependence that, if found, renders traditional regression approaches inappropriate.

Example: if ICC = 0.75, 3/4 of the variance is attributable to between-unit differences and 1/4 is explaind by within-unit variability over time.

ICC = 0 # TODO Check

## Estimator of ICC

IĈC = ô^2_g / (ô^2_g + ô^2_e)

# Likelihood Ratio Test

Uses maximum likelihood estimation to compare the likelihood values for two hierarchical models

# Linear Mixed Models

## Subject-specific models

Goal: to describe individual units as good as possible

- Advantage: knowledge on individuals
- Disadvantage: requires several modeling assumptions that are difficult to verify

## Marginal models

In a marginal model (AKA, the population averaged model), the model equation is written just like any linear model. 
There is a single response and a single residual.  
The difference between the marginal model and a linear model is that the residuals are not assumed to be independent with constant variance.

In a marginal model, we can directly estimate the correlations among each individual’s residuals.  (We do assume the residuals across different individuals are independent of each other). We can specify that they are equally correlated, as in the RM ANOVA, but we’re not limited to that assumption.  Each correlation can be unique, or measurements closer in time can have higher correlations than those farther away.  There are a number of common patterns that the residuals tend to take.

Likewise, the residual variances don’t have to be equal as they do in the RM ANOVA.

So in cases where the assumptions of equal variances and equal correlations are not met, we can get much better fitting models by using a marginal model.  The other big advantage is by taking a univariate approach, we can do post-hoc tests on the repeated measures factor.

Goal: to describe the population as good as possible

- Advantage: less sensitive to assumptions
- Disadvantage: no knowledge on units

NOTE: marginal models DO NOT have a random statement

# Model Selection

1. Overspecify: use all potential fixed effects factors
2. Select appropriate covariance structure using REML estimation (LRT, AIC, AICC, ...)
3. Fix best covariance structure (?)
4. Select best fixed effects model with ML estimation
    - Use LRT for nested fixed effects 
    - Use information criteria (AIC, AICC, BIC) for non-hierarchical fixed effects models
5. Fix the fixed effects model (?)
6. Refit final model with REML estimates and collect residuals
7. Investigate goodness-of-fit

AIC: favours models with fewer parameters as possible.
BIC is similar to AIC, but penalizes the number of parameters more than AIC. Thus, AIC would pick more complex models than BIC.

NOTE:
Interpretation three way ANOVA: Slide 92
Model selection: Slides midex models 71
