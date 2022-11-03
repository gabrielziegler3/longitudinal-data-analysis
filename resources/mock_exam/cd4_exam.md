# Q1

## A (FULL MODEL WITH ALL FACTORS)

Let CD4 be our response variable for the m-th subject. Then we have:

Model: ŷ_ijklm = mu + alpha_i + beta_j + gama_k + delta_l + epsilon_m(i) + alpha_i * beta_j + alpha_i * gama_k + alpha_i * delta_l + eta_ijklm

alpha_i: trt - fixed effect
beta_j: age - fixed effect
gama_k: sex - fixed effect
delta_l: time - fixed effect
epsilon_m: subject - nested random effect of treatment in individuals
eta_ijklm: random error 

With 4 treaments and 3 times, relevant assumptions for the model are:

    alpha_i + ... + alpha_I = 0, for every i.
    delta_l + ... + delta_L = 0, for every l.
    alpha_i * delta_l ... alpha_I * delta_L = 0, for every i and l.

Where all factors are independent
Where all samples are independent

epsilon is normally distributed with mean 0 and variance sigma^2_epsilon(I)
eta is normally distributed with mean 0 and variance sigma^2_r. I.I.D.


## A - Model considering only factors ID, TIME, TRT and CD4 as response variable

Let log(CD4+1) be our response variable for the m-th subject. Then we have:

Model: ŷ_ijk = mu + alpha_i + beta_j + alpha_i * beta_j + c_k(i) + eta_ijk

alpha_i: trt - fixed effect
beta_j: time - fixed effect
c_k: subject - nested random effect of treatment in individuals
eta_ijk: random error 

With 4 treaments and 3 times, relevant assumptions for the model are:

    alpha_i + ... + alpha_I = 0, for every i.
    beta_j + ... + beta_J = 0, for every l.
    alpha_i * beta_j ... alpha_I * beta_J = 0, for every i and l.

Where all factors are independent
Where all samples are independent

c is normally distributed with mean 0 and variance sigma^2_c(I)
eta is normally distributed with mean 0 and variance sigma^2_r. I.I.D.

## B

Let P = time, T = treatment, S = subject.

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

ô²_R = MSR
ô²_S(T) = [MS_S(T) - MSR] / I
Q_PT = [MS_PT - MSR] / K
Q_T = [MS_T - MS_S(T)] / IK
Q_P = [MS_P - MSR] / JK


## C

We have Q_T = [MS_T - MS_S(T)] / IK.

Using the assumptions we have:

ŷ - y_i

% TODO

## D

Same as mock exam

## E

time and treatment * time seem to have a significant effect, however treatment alone cannot be considered to have an effect because it is not significant.

## F

From the TYPE ANOVA Table we have

MS_B = 2.555816 (id(trt))
MS_W = 0.391730 (residual)
C_n = 3 (var residual + *VAL* * var(id(trt)))
df_b = 178
df_w = 356

From the table Covariance Parameter Estimates:
ô^2_g = 0.7214 % id(trt)        
ô^2_e = 0.3917 % residual

IĈC = ô^2_g / (ô^2_g + ô^2_e) = 0.7214 / (0.7214 + 0.3917) = 0.6480
F = MS_B / MS_W = 6.52443264493401

% Calculate with python function
LOWER = 0.5765
UPPER = 0.7134

With ICC = 0.6480, 64.8% of the variance is attributable to between-unit differences and 45.2% is explaind by within-unit variability.

## G

H0: alpha_i = 0 and alpha_i * beta_i = 0 for all i.
H1: there is at least one alpha_i != 0 and alpha_i * beta_i != 1 for all i.

For degrees of freedom, look at anova table in SAS. Difference in the number of PARAMETERS

Obs	MIN2L_F	MIN2L_N	LRT	    DF	P
1	1367.00	1400.00	33.0001	2	6.8254E-8

There is significant effect from treatment.

# Q2

## A

Covariance Parameter Estimates
Cov Parm	Subject	Estimate
UN(1,1)	    id	    0.7047
UN(2,1)	    id	    0.6308
UN(2,2)	    id	    1.4211
UN(3,1)	    id	    0.6308
UN(3,2)	    id	    0.9025
UN(3,3)	    id	    1.2135

Fit Statistics
-2 Res Log Likelihood	    1353.4
AIC (Smaller is Better)	    1365.4
AICC (Smaller is Better)	1365.5
BIC (Smaller is Better)	    1384.6

## B

Answer: The null hypothesis is that there is no effect for TRT, i.e. that b_i = 0 for i in {1, 2, 3, 4},
where b_i is the effect of TRT i. The alternative hypothesis, then, is that there is an effect, i.e. that there is some i in {1, 2, 3, 4} such that b_i != 0. We run the following code:

```sas
DATA data; 
  set '/home/$USER/assignment.sas7bdat'; 
RUN;
 
/* normalize CD4 */
DATA DATA;
  SET DATA;
  CD4 = LOG(1 + CD4);
RUN;

PROC MIXED DATA=DATA;
	CLASS ID TIME TRT;
	MODEL CD4 = TIME TRT TIME * TRT /SOLUTION DDFM=SAT CL;
	REPEATED TIME / SUBJECT=ID TYPE=UN;
RUN;
```

The output for the fixed effects is shown below. 

                    Solution for Fixed Effects
    Effect	    time	trt	    Estimate	Std         DF      t-Value	    Pr > |t|	Alpha	Lower	    Upper
                                            Error 
    Intercep    t	              3.2096	0.1513	    178	    21.21	    <.0001	    0.05	2.9110	    3.5082
    time	    1	             -0.2219	0.1113	    178	    -1.99	    0.0477	    0.05	-0.4416	    -0.00230
    time	    2	             0.07967	0.1251	    178	    0.64	    0.5251	    0.05	-0.1672	    0.3266
    time	    3	             0	        .		    .	    .	        .	        .       .	        .
    trt	 	    1	            -0.6807	    0.2307	    178	    -2.95	    0.0036	    0.05	-1.1361	    -0.2254
    trt	 	    2	            -0.6073	    0.2195	    178	    -2.77	    0.0063	    0.05	-1.0404	    -0.1741
    trt	 	    3	            -0.00694	0.2291	    178	    -0.03	    0.9759	    0.05	-0.4591	    0.4452
    trt	 	    4	            0	        .		    .	    .	        .	        .       .	        .
    time*trt	1	    1	    0.6160	    0.1697	    178	    3.63	    0.0004	    0.05	0.2811	    0.9509
    time*trt	1	    2	    0.6506	    0.1615	    178	    4.03	    <.0001	    0.05	0.3320	    0.9692
    time*trt	1	    3	    -0.08727	0.1685	    178	    -0.52	    0.6052	    0.05	-0.4198	    0.2453
    time*trt	1	    4	    0	        .		    .	    .	        .	        .       .	        .
    time*trt	2	    1	    0.3337	    0.1908	    178	    1.75	    0.0819	    0.05	-0.04271    0.7102
    time*trt	2	    2	    0.1786	    0.1815	    178	    0.98	    0.3263	    0.05	-0.1795	    0.5368
    time*trt	2	    3	    -0.1456	    0.1894	    178	    -0.77	    0.4432	    0.05	-0.5194	    0.2283
    time*trt	2	    4	    0	        .		    .	    .	        .	        .       .	        .
    time*trt	3	    1	    0	        .		    .	    .	        .	        .       .	        .
    time*trt	3	    2	    0	        .		    .	    .	        .	        .       .	        .
    time*trt	3	    3	    0	        .		    .	    .	        .	        .       .	        .
    time*trt	3	    4	    0	        .		    .	    .	        .	        .       .	        .

We see that TRT 1 and 2 lie outside the confidence interval with significant result, hence we reject the null hypothesis

## C

1. Overspecify: use all potential fixed effects factors
2. Select appropriate covariance structure using REML estimation (LRT, AIC, AICC, ...)
3. Fix best covariance structure
4. Select best fixed effects model with ML estimation
    - Use LRT for nested fixed effects 
    - Use information criteria (AIC, AICC, BIC) for non-hierarchical fixed effects models
5. Fix the fixed effects model
6. Refit final model with REML estimates and collect residuals
7. Investigate goodness-of-fit (check for assumptions)

Checking UN vs CS:

Obs	MIN2L_F	MIN2L_N	LRT	    DF	P
1	1155.23	1181.90	26.6684	1	.000000242

Therefore we can reject the null hypothesis and select the model with highest log likelihood value, hence we select UN.

Checking SEX:

Obs	MIN2L_F	MIN2L_N	LRT	    DF	P
1	1181.34	1187.27	5.93574	5	0.31252

Therefore the null hypothesis cannot be rejected. We can remove SEX and TRT*SEX to make the model less complex.

The final model is 

```sas
PROC MIXED DATA=Q2 METHOD=ML;
	CLASS ID TIME TRT AGE;
	MODEL CD4 = TIME TRT AGE TIME * TRT AGE * TRT/SOLUTION DDFM=SAT CL;
	REPEATED TIME / SUBJECT=ID TYPE=UN;
RUN;
```

# Q3

