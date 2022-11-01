#Q1

## A

Model: ŷ_ijk = mu + alpha_i + beta_j + alpha_i * beta_j + c_k(i) + eta_ijk

alpha: period (fixed effect)
beta: training (fixed effect)
alpha * beta: training effect over period (fixed effect)
c: nested effect of treatment in individuals (random effect)
eta: random error 

Where all factors are independent
Where all samples are independent

c and eta are normally distributed

## B

Expected MS

1. Assume all effects are RANDOM (hence the sigma -- variance components (VC)).
2. Fill an X where the source (row) letter is also contained in the VC (column). The residuals column is ALWAYS filled with 1s.

    | SOURCE | o^2_P | o^2_T | o^2_PT | o^2_S(T) | o^2_R |
    |--------|-------|-------|--------|----------|-------|
 i  | P      |   X   |       |        |          |   1   |
 j  | T      |       |   X   |   X    |    X     |   1   |
 ij | PT     |       |       |   X    |          |   1   |
k(j)| S(T)   |       |       |        |    X     |   1   |
    | R      |       |       |        |          |   1   |

3. Now replace the Xs with the indices missing. The rest of the cells are filled with 0.

    | SOURCE | o^2_P | o^2_T | o^2_PT | o^2_S(T) | o^2_R |
    |--------|-------|-------|--------|----------|-------|
 i  | P      |  JK   |       |        |          |   1   |
 j  | T      |       |  IK   |   IK   |     I    |   1   |
 ij | PT     |       |       |    K   |          |   1   |
k(j)| S(T)   |       |       |        |     I    |   1   |
    | R      |       |       |        |          |   1   |

4. VCs are REPLACED by quadratic functions for the fixed effects, that is fixed factors sigmas are replaced by a function Q.
Remove non-diagonal values for the quadratic functions. Also, add the degrees of freedom column.

degrees of freedom: 

    | SOURCE |  Q_P  |  Q_T  |  Q_PT  | o^2_S(T) | o^2_R |     DF     |
    |--------|-------|-------|--------|----------|-------|------------|
 i  | P      |  JK   |       |        |          |   1   |   (I-1)    |
 j  | T      |       |  IK   |        |     I    |   1   |   (J-1)    |
 ij | PT     |       |       |   K    |          |   1   | (I-1)(J-1) |
k(j)| S(T)   |       |       |        |     I    |   1   |   J(K-1)   |
    | R      |       |       |        |          |   1   |J(I-1)(K-1) |

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

#TODO hypothesis

For degrees of freedom, look at anova table in SAS.

P-value from LRT: 0.0195. Below 0.05, then reject null hypothesis. 

There is significant effect from treatment.


# 2

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
