## Applied Survival Analysis

##### **D. Moore( 2015)**

Summary:

### Definitions 
- **Censoring**: A condition in which the value of a measurement or observation is only partially known 
- **Kaplam-Meier estimator**: The product over the failure times of the conditional probabilities of surviving to the next failure time.  It is a non-parametric statistic used to estimate the survival function from lifetime data.  In medical research, it is often used to measure the fraction of patients living for a certain amount of time after treatment
- **Deviance**: Goodnes-of-fit statistic for a statistical model; it is often used for statistical hypothesis testing 
- **Null Model**: Model with no fitted covariates
- **LOESS Regression**: Non-parametric regression methods that combine multiple regression models in a kNN based meta model
- **Survival models**: Relate the time that passes before some event occurs to one or more covariates that may be associated with that quantity in time
- **Proportional Hazards Model**: in PHMs, the unique effect of a unit increase in a covariate is multiplicative with respect to the hazard rate.  Other types of models such as accelerated failure time models do not exhibit proportional hazards

### Chapter 1: Introduction
- Survival analysis is the study of survival times and of the factors that influence them
- Types of studies with survival outcomes include clinical trials, prospective, and retrospective observational studies, and animal experiments 
- Survival analysis resembles linear and logistic regression analysis in several ways: 
  1. There is (typically) a single outcome variable and one more more predictors
  2. Testing statistical hypotheses about the relationship of the predictors to the outcome variable is of particular interest
  3. Adjusting for confounding covariates is crucial
  4. Model selection and checking of assumptions through analysis of residuals and other methods are key requirements
- **Characteristics of Survival Analysis**
  1. A key characteristic of survival data is that the response variable is a non-negative discrete or continuous RV, and represents the time from a well-defined origin to a well-defined event
  2. Arises when the starting or ending events are not precisely observed.  The most common example of this is right censoring
- Censoring can be classified into three types: Type I, Type II, or random.  
  - Type I: The censoring times are pre-specified
  - Type II: Censoring occurs when the experimental objects are followed until a pre-specified fraction have failed (rare in biomedical studies)
  - Random: Careful attention to the cause of the censoring is essential in order to avoid biased survival estimates
- In clinical trials, the most common source of random censoring is administrative censoring which results because some patients in a clinical trial have not yet died at the time the analysis is carried out
- Administrative censoring has the property that the censoring mechanism is (ordinarily) independent of the survival mechanism
- The goals of survival analysis are to estimate the survival distribution, to compare two or more survival distributions, or to assess the effects of a number of factors on survival
- Techniques bear some resemblance to regression analysis with the important distinction that the outcome is always positive and often censored

### Chapter 2: Basic Principles of Survival Analysis
- Survival analysis methods depend on the survival distribution, and two key ways of specifying it are the survival function and the hazard function
  - Survival function: Defines the probability of surviving up to a point t
  - Hazard function: The instantaneous failure rate (also known as the intensity function or the force of mortality)
- The CDF is another commonly used function outside 
- The hazard function can be expressed as the PDF divided by the survival function
- The cumulative hazard function is defined as the area under the hazard function up to time t
- The mean survival time is only defined if all subjects eventually fail.  If they do not, then the area under the survival curve is infinite
- The exponential distribution, the simplest survival distribution, has a constant hazard, h(t) = lambda
- The exponential distribution is easy to work with, but the constant hazard assumption is not often appropriate for describing the lifetimes of humans or animals.  The Weibull distribution offers more flexibility in survival data

### Chapter 3: Nonparametric Survival Curve Estimation
- When modeling human or animal survival, it is hard to know what paramedic family to choose, and often none of the available families have sufficient flexibility to model the usual shape of the distribution
- Thus in medical and health applications, nonparametric methods which have the flexibility to account for the vagaries of the survival of living things, have considerable advantages
- The most widely used of these is the product limit estimator, aka Kaplan-Meier estimator (the product over the failure times of the conditional probabilities of surviving to the next failure time)

### Chapter 4: Nonparametric Comparison of Survival Distributions
- Testing the equivalence of two groups is a familiar problem in statistics.  Typically we are interested in testing a null hypothesis that two population means are equal versus an alternative that the means are not equal (two sided test), or that the mean for an experiment is greater or less than that of a standard treatment
- If there is a need to compare two groups while adjusting for another covariate, there are two approaches one can use
  1) Include the other covariate as regression terms for the hazard function 
  2) If the covariate we are adjusting for is categorical with a small number of levels G, we may construct a stratified log-rank test.  Essentially, for each level of the second variable, we compute a score statistic

### Chapter 5: Regression Analysis Using the Proportional Hazards Model
- In the last chapter we introduced a parameter that indexes the difference between the two survival distributions
- We can see that we can re-express this relationship in terms of the hazard functions 
- This proportional hazards model will allow us to fit regression models to censored survival data, much as we can do in linear and logistic regression
- In this chapter, we shall see how to do this using a partial likelihood
- Parametric distributions require strong assumptions about the form of the underlying survival distribution.  The partial likelihood will allow us to use an unspecified baseline survival distribution to define the survival distributions of subjects based on their covariates.  Differs from likelihood in two ways
  1) It is a product of expressions, one for each failure time, while censoring times do not contribute any factors
  2) The factors of a partial likelihood are conditional probabilities
- In standard likelihood theory, one can derive three forms of the test H_0: \Beta = 0: the Wald test, the score test, and the likelihood ratio test.  In survival analysis, we may used the partial likelihood to derive these three tests
- The Wald test is perhaps the most commonly used test, and carrying it out is straightforward from computer output 
- The Score Test: score function is the first derivative of the partial log-likelihood.  We evaluate the score and information at the null hypothesis value of \Beta.  This test can be done without finding the MLE
- Likelihood Ratio Test: Uses the result form statistical theory that …. Follows approximately a chi-square  with one df
- Tied survival time can arise in two ways
  1) If the underlying data are continuous, ties may arise due to rounding
  2) When survival times are genuinely discrete
- If censoring times are tied with failure times, the general convention is to consider the failures to precede the censoring times, so that the censored individuals are still in the risk set at the time of failure

### Chapter 6: Model Selection and Interpretation
- SA studies typically include a wealth of clinical , demographic, and biomarker information on the patients as well s indicators for a therapy or other intervention
- For each covariate the parameter \Beta_j is the log hazard ratio for the effect of that parameter on survival, adjusting for the other covariates
  - Continuous covariates: Represents the effect of a unit change in the covariate
  - Dummy variables: Represents the effect of the corresponding level as compared to the reference covariate 
- Can enhance this model by transforming the variable if it is not linear related to the log hazard
- Section 6.2 covers material presented in ISLR: while these models are similar to ones used in linear and logistic regression, there are some key differences
  - EG: Since survival data evolve over time, there is a possibility that some covariate values may also change as time passes.  Initially, however, we require that all covariates are fixed at the beginning of the trial, and thus cannot change change in response to evolving conditions
  - Later we examine how to change the model to deal with time varying conditions
- In SA we do not include an intercept term
- Section 6.3 covers material presented in ISLR (on comparing nested models)
- Section 6.4 covers material presented in ISLR (on comparing non-nested models using AIC)
- When a covariate is continuous, we are interested in whether that covariate is related to survival and, if so, in what manner

### Chapter 7: Model Diagnostics
- The use of residuals for model checking has been well-developed in linear regression theory.  Many of these residuals have been generalized to survival analysis 
- In addition, the fact that survival data evolves over time, and requires assumptions such as proportional hazards, makes it necessary to develop additional diagnostic residual models
- An important tool for assessing the goodness of a model is to compare the censoring indicator for each subject to the expected value.  This varies based on whether or not there are time dependent covariates
- Martingale residuals may be used much as residuals that the reader may be familiar with from linear regression.  However, unlike those, the sum of squares of Martingale residuals cannot be used as a measure of goodness of fit
- The “deviance” residual is an alternative that does have this property 
  - These residuals are symmetrically distributed with expected value 0 (if the fitted model is correct)
  - The sum of squares of these residuals is the value of the likelihood ratio test, which is analogous to the deviance from generalized linear model theory 
  - While the properties of deviance residuals might seem preferable to those of martingale residuals, only the latter have the property of showing us the functional form of a covariate
- We can plot martingale residuals against continuous predictors to get a preliminary assessment of which of these predictors should be in the model, and what form they should take
- Example 7.1
  1) Read in the data and truncate the variable “priorAttempts”
  2) Fit the null model and obtain the martingale residuals
  3) We desire to assess the potential relationship of survival to age, prior attempts at quitting, and longest prior period without smoking.  We plot these null model residuals versus each of these variables and also versus log transformations
  4) Fir a “loess” smooth curve through each set of residuals to better assess the shapes of the relationships 
  5) Get 95% CI’s using “smoothSEcurve” function
- Some subjects have an especially large influence on the parameter estimates.  Since some such influential subjects may indicate a problem with the data, it is helpful for the data analyst to have tools that can identify those subjects
- Case deletion residuals (aka “jackknife residuals”) serve this purpose
- The proportional hazards assumption is key to the construction of the partial likelihood, since it is this property that allows once to cancel out the baseline hazard function from the partial likelihood factors
- If one has a binary predictor variable, such as experimental vs. standard treatment, what this assumption means is that the hazard functions are proportional, and hence the log hazard functions are separated by a constant
- Assumption is at beast an approximation in practice, and minor violations are unlikely to have jar effects on inferences, so using a hypothesis test for this is is usually of limited value
- Below we examine some commonly used assessment methods
  1) Log Cumulative Hazard Plots
  2) Schoenfeld Residuals: Provide a useful way to assess this assumption.  To see how they are derived, recall the partial log-likelihood function and its derivative, which is the score function

### Chapter 8: Time Dependent Covariates
- The partial likelihood theory for survival data, allows one to model survival times while accommodating covariate information.  An important caveat to this theory is that the values of the covariates must be determined at time t = 0, and remain constant thereafter
- This issue arises with survival data because such data evolve over time, and it would be improper to use the value a covariate to model survival information that is observed before the covariate’s value is known
- To accommodate covariates that may change their value over time (“time dependent covariates”), special measures are necessary to obtain valid parameter estimates
- An intervention that occurs after the start of the trial, or a covariate (such as air pollution exposure) that changes values over the course of the study are two examples of time dependent variables
- Rule: We cannot predict survival using covariate values from the future
- Simple fix is define a “landmark” time to divide patients into two groups.  In this approach, patients who receive the intervention prior to the landmark go into to the intervention group and those who did not are placed in the comparison group
- There is a better way to directly model time dependent variables using the framework of the classical Cox proportional hazards model, but important adjustments are required to obtain unbiased estimates
- To do this correctly, we need to modify the partial likelihood function to accommodate these types of variables.  Essentially, at each failure time, there are a certain number of patients at risk, and one falls 
- With a TDC the entire denominator has to be recalculated at each failure time
