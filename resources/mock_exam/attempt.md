# Q1

## A

Model: ŷ_ijk = mu + alpha_i + beta_j + alpha_i * beta_j + c_k(i) + eta_ijk

alpha: period (fixed effect)
beta: training (fixed effect)
alpha * beta: training effect over period (fixed effect)
c: nested effect of treatment in individuals (random effect)
eta: random error 

Where all factors are independent
Where all samples are independent

* All random terms are independent

c and eta are normally distributed

## B

Expected MS

1. Assume all effects are RANDOM (hence the sigma -- variance components (VC)).
2. Fill an X where the source (row) letter is also contained in the VC (column). The residuals column is ALWAYS filled with 1s.

|idx | SOURCE | o^2_P | o^2_T | o^2_PT | o^2_S(T) | o^2_R |
|----|--------|-------|-------|--------|----------|-------|
| i  | P      |   X   |       |        |          |   1   |
| j  | T      |       |   X   |   X    |    X     |   1   |
| ij | PT     |       |       |   X    |          |   1   |
|k(j)| S(T)   |       |       |        |    X     |   1   |
|    | R      |       |       |        |          |   1   |

3. Now replace the Xs with the indices missing. The rest of the cells are filled with 0.

|idx | SOURCE | o^2_P | o^2_T | o^2_PT | o^2_S(T) | o^2_R |
|----|--------|-------|-------|--------|----------|-------|
| i  | P      |  JK   |       |        |          |   1   |
| j  | T      |       |  IK   |   IK   |     I    |   1   |
| ij | PT     |       |       |    K   |          |   1   |
|k(j)| S(T)   |       |       |        |     I    |   1   |
|    | R      |       |       |        |          |   1   |

4. VCs are REPLACED by quadratic functions for the fixed effects, that is fixed factors sigmas are replaced by a function Q.
Remove non-diagonal values for the quadratic functions. Also, add the degrees of freedom column.

degrees of freedom: 

|idx | SOURCE |  Q_P  |  Q_T  |  Q_PT  | o^2_S(T) | o^2_R |     DF     |
|----|--------|-------|-------|--------|----------|-------|------------|
| i  | P      |  JK   |       |        |          |   1   |   (I-1)    |
| j  | T      |       |  IK   |        |     I    |   1   |   (J-1)    |
| ij | PT     |       |       |   K    |          |   1   | (I-1)(J-1) |
|k(j)| S(T)   |       |       |        |     I    |   1   |   J(K-1)   |
|    | R      |       |       |        |          |   1   |J(I-1)(K-1) |

5. Then build the linear system as:

E[MS_P] = Q_P + o^2_R
E[MS_T] = Q_T + I * o^2_S(T) + o^2_R
E[MS_PT] = Q_PT + o^2_R
E[MS_S(T)] = I * o^2_S(T) + o^2_R
E[MS_R] = o^2_R

The variance components estimators are:

ô^2_R = MS_R and,
ô^2_S(T) = (MS_S(T) - MS_R) / I

F-test:

H0: ô^2_S(T) = 0
H1: ô^2_S(T) != 0

F-test = MS_S(T) / MS_R ~ F_df df(MS_S(T)), df(MS_R).
- NOTE: F distribution takes two parameters and it must be the degrees of freedom of the numerator and denominator. By default it is: F = MSb / MSw

## C

SKIP

## D

With an unbalanced design all estimation techniques could still be used.
However, the order of terms is known: subject (ID), treatment (TRT), period (P) and the degrees of freedom for treatment and period are 4 and 2, respectivelly. 
These details make the ANOVA Type 1 the most recommended for this case.

## E

TRT and PERIOD and TRT * PERIOD are significant

## F

MS_B = 40.44
MS_W = 10.58

F = MS_B / MS_W = 3.82

IĈC = ô^2_g / (ô^2_g + ô^2_e) = 15.8758 / (15.8758 + 10.5788) = 0.6
LOWER = 0.5361 
UPPER = 0.6568 

With ICC = 0.600, 60% of the variance is attributable to between-unit differences and 40% is explaind by within-unit variability.

## G

H0: alpha_i = 0 and alpha_i * beta_i = 0 for all i.
H1: there is at least one alpha_i != 0 and alpha_i * beta_i != 1 for all i.

For degrees of freedom, look at anova table in SAS.

P-value from LRT: 0.0195. Below 0.05, then reject null hypothesis. 

There is significant effect from treatment.


# Q2

## A

```sas
DATA data; 
  set '/home/$USER/data_exam.sas7bdat'; 
RUN; 

PROC SQL;
	CREATE TABLE Q2 AS 
	(SELECT * FROM DATA
	WHERE ORDER IN (1, 2));
RUN;

PROC SORT DATA=Q2 OUT=Q2;
	BY ID PERIOD;
RUN;

PROC MIXED DATA=Q2;
	CLASS ID PERIOD TRT;
	MODEL WORD = PERIOD TRT /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;
```

We cannot test the interaction because it with just periods 1 and 2, we have a nested and not crossed relationship between treatment and period.
When period="baseline" then TRT=4 always.

## B

H0: there is no effect of training i.e. b_i = 0 for i in {1,2,3,4} where b_i is the effect of training i
H1: there is effect of training i.e. b_i != 0 for some i in {1,2,3,4}

The solutions for fixed effects table shows that:

- TRT=2 has a statistically significant result, therefore the null hypothesis is rejected.

## C

Best model:
```sas
PROC MIXED DATA=Q2;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;
```

1. Overspecify: use all potential fixed effects factors

First we start with a model with all interactions with TRT.

```sas
PROC MIXED DATA=Q2;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*SEX TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;
```

2. Select appropriate covariance structure using REML estimation (LRT, AIC, AICC, ...)

Then we run a LRT to assert the appropriate covariance structure. In this case, we compare UN vs CS.

```sas
ODS OUTPUT FITSTATISTICS=FULL1;
PROC MIXED DATA=Q2;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*SEX TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;

ODS OUTPUT FITSTATISTICS=NULL1;
PROC MIXED DATA=Q2;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*SEX TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=CS;
RUN;

DATA FULL1;
    SET FULL1;
    WHERE DESCR = "-2 Res Log Likelihood";
    MIN2L_F = VALUE;
    KEEP MIN2L_F;
RUN;

DATA NULL1;
    SET NULL1;
    WHERE DESCR = "-2 Res Log Likelihood";
    MIN2L_N = VALUE;
    KEEP MIN2L_N;
RUN;

DATA LRT1;
    MERGE FULL1 NULL1;
    LRT = MIN2L_N - MIN2L_F;
    DF = 1;
    P = 1-PROBCHI(LRT,DF);
RUN;

PROC PRINT DATA=LRT1;
RUN;
```

Result: LTR = 3.7088E-10, therefore the null hypothesis can be rejected and we conclude that UN is the optimal covariance structure because that is the model that leads to the highest likelihood (because we are looking at negative likelihood), hence better fit.

```sas
/* model selection Q2 C */
/* Check whether TRT*SEX should be in the model */

ODS OUTPUT FITSTATISTICS=FULL1;
PROC MIXED DATA=Q2 METHOD=ML;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*SEX TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;

ODS OUTPUT FITSTATISTICS=NULL1;
PROC MIXED DATA=Q2 METHOD=ML;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;

DATA FULL1;
    SET FULL1;
    WHERE DESCR = "-2 Log Likelihood";
    MIN2L_F = VALUE;
    KEEP MIN2L_F;
RUN;

DATA NULL1;
    SET NULL1;
    WHERE DESCR = "-2 Log Likelihood";
    MIN2L_N = VALUE;
    KEEP MIN2L_N;
RUN;

DATA LRT1;
    MERGE FULL1 NULL1;
    LRT = MIN2L_N - MIN2L_F;
    DF = 1;
    P = 1-PROBCHI(LRT,DF);
RUN;

PROC PRINT DATA=LRT1;
RUN;
```

Result: LTR = 0.24, therefore the null hypothesis cannot be rejected. Thus, both models have more or less the same fit, this means that the interaction TRT*SEX was not adding relevant information to the model, so we can drop it.

4. Select best fixed effects model with ML estimation
    - Use LRT for nested fixed effects 
    - Use information criteria (AIC, AICC, BIC) for non-hierarchical fixed effects models

Since we are dealing with a dataset with nested hierarchies, we use LRT for model selection.

```SAS
ODS OUTPUT FITSTATISTICS=FULL1;
PROC MIXED DATA=Q2 METHOD=ML;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*SEX TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=UN;
RUN;

ODS OUTPUT FITSTATISTICS=NULL1;
PROC MIXED DATA=Q2 METHOD=ML;
	CLASS SITE ID PERIOD TRT SEX AGEC;
	MODEL WORD = PERIOD TRT SITE SEX AGEC TRT*SITE TRT*AGEC /SOLUTION DDFM=SAT CL;
	REPEATED PERIOD / SUBJECT=ID TYPE=CS;
RUN;

DATA FULL1;
    SET FULL1;
    WHERE DESCR = "-2 Log Likelihood";
    MIN2L_F = VALUE;
    KEEP MIN2L_F;
RUN;

DATA NULL1;
    SET NULL1;
    WHERE DESCR = "-2 Log Likelihood";
    MIN2L_N = VALUE;
    KEEP MIN2L_N;
RUN;

DATA LRT1;
    MERGE FULL1 NULL1;
    LRT = MIN2L_N - MIN2L_F;
    DF = 1;
    P = 1-PROBCHI(LRT,DF);
RUN;

PROC PRINT DATA=LRT1;
RUN;
```

Result: LTR = 3.2972E-10, therefore the null hypothesis can be rejected and we conclude that TRT*SEX can be safely removed from the model.

5. Fix the fixed effects model (?)
6. Refit final model with REML estimates and collect residuals
7. Investigate goodness-of-fit

# Q3

## A

```sas
PROC MIXED DATA=LMM;
    CLASS ID SITE TRT;
    MODEL AVLT = TRT TIME TRT*TIME/ SOLUTION DDFM=SAT;
    RANDOM INT TIME/SUBJECT=ID TYPE=UN;
RUN;
```

The interpretation for the tests in this question is the same as the ANOVA.

## B

```python
def minimize_variability(tau_0, tau_1, off_diag, s2_r):
    rho = off_diag/(np.sqrt(tau_0)*np.sqrt(tau_1))
    min_var = (1-rho**2)*tau_0 + s2_r 
    min_t = -rho*np.sqrt(tau_0)/np.sqrt(tau_1)
    print(f"*** Minimum variance: {min_var:.3f} *** \n*** Minimum variance achieved at time point {min_t:.3f} ***")
```

Covariance Parameter Estimates
Cov Parm	Subject	    Estimate
UN(1,1)	    ID	        89.7452     % tau0^2
UN(2,1)	    ID	        1.4801      % off diagonal. Rho.
UN(2,2)	    ID	        0.4232      % tau1^2
Residual	     	    29.1645     % sigma_r^2

```python
In [2]: t2_0 = 89.7452 # tau0^2
   ...: t2_1 = 0.4232 # tau1^2
   ...: off_diag = 1.4801 # off-diagonal value
   ...: s2_r = 29.1645 # sigma_r^2
   ...:
In [3]: minimize_variability(t2_0, t2_1, off_diag, s2_r)
*** Minimum variance: 113.733 ***
*** Minimum variance achieved at time point -3.497 ***
```

Minimum variance: 113.733
Minimum variance achieved at time point -3.497

## C

% TODO this is a guide, not part of the answer. Couldn't solve this.
The model for subject-specific follows the pattern: y_ij = b_i0 + b_i1 * t_ij + e_ij

b_0 = intercept
b_1 = slope estimate

- Results for this model:

    Covariance Parameter Estimates
    Cov Parm	Subject	 Estimate
    UN(1,1)	    ID	     89.7452    % tâu^2_0
    UN(2,1)	    ID	     1.4801     % rho
    UN(2,2)	    ID	     0.4232     % tâu^2_1
    Residual	 	     29.1645    % sigma^2_r

    Solution for Fixed Effects
    Effect	        TRT	Estimate	Std     DF	    t Value	    Pr > |t|
                                    Error	
    Intercept	 	    44.9110	    0.4228	2581	106.22	    <.0001   % b_0
    TRT	            1   2.2049	    0.5964	2577	3.70	    0.0002
    TRT	            2   0.7729	    0.5988	2576	1.29	    0.1969
    time	 	        0.8685	    0.1154	2043	7.53	    <.0001   % b_1
    time*TRT	    1	0.02304	    0.1616	2044	0.14	    0.8866
    time*TRT	    2	-0.06880    0.1617	2023	-0.43	    0.6705
    time*TRT	    3	0.1328	    0.1612	2040	0.82	    0.4100
    time*TRT	    4	0	.	    .	.	.

