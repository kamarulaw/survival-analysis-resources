## Boosted Trees for Risk Prognosis

##### **A. Bellot, M.v.d.Schaar (2018)**

Summary: Paper presents a new approach to ensemble learning for risk prognosis in heterogeneous medical populations.  Goal is to improve overall prognosis by focusing on under-represented patent subgroups with an atypical disease presentation: Method proceeds sequentially by learning non-parametric survival estimators which learn to improve predictions via boosting.  Ends up producing fully non-parametric survival estimates

### Definitions
- **Boosting**: A general ensemble machine learning approach that combines simple predictive models (also referred to as hypotheses) trained sequentially such that each one is explicitly encouraged to correct mistakes of previous hypotheses
- **Fully Non-parametric Survival Estimates**: Estimates constrained neither by assumptions regarding the baseline hazard nor assumptions regarding the underlying covariate interactions
- **Boosting for survival analysis**: The idea is to pursue iterative steepest ascent of the log likelihood function
- **Bagging for survival analysis**: Include Random Survival Forests methods.  These are parallel ensembles in which fully grown survival trees are built independently on a bootstrapped sample of the data
- **Score**: Measures the accuracy of probabilistic predictions.  Proper scoring rules 
  - If a scoring rule is proper, then the highest expected reward is obtained by reporting the true probability distribution.  The use of a proper scoring rule encourages the forecaster to be honest to maximize the expected reward
- **Brier score**: A proper score function that measures the accuracy of probabilistic predictions.  It is applicable to tasks in which predictions must assign probabilities to a set of mutually exclusive discrete outcomes
  - Can be thought of as either a measure of the “calibration” of a set of probabilistic predictions, or as a “cost function”.  The score measures the mean squared difference between 1) the predicted probability, and 2) the actual outcome o_i
  - The BS is appropriate for binary and categorical outcomes that can be structured as true or false, but is inappropriate for ordinal variables which can take on three or more values (this is b/c the Brier score assumes that all possible outcomes are equivalently “distant” from one another)

### Notes

#### Introduction
- We are examining the problem of predicting event probabilities over time, such as death, with the aim of providing a fully individualized survival function for each patient.  This effectively extends the general approach of boosting to event-time estimation
- Authors developed two non-parametric boosting-based algorithms that iteratively train shallow survival trees on samples of the patient population.  After each iteration, the patient population is re-weighted so as to bias the next iteration toward correcting previous errors in the predicted survival function
- Final survival estimates result from a weighted average of individual tree predictions dependent on each tree’s predictive performance
- Paper is *technically significant* because it develops an extension of boosting architectures to survival analysis.  In contrast to single time predictions, the authors estimate full probability distributions and propose a notion of prediction “correctness” which successfully drives weak learners towards frequently mis-estimated patients over successive iterations

#### Background
- Goal is to develop a prognostic risk score for heterogeneous populations.  Each patient is characterized by a d-dimensional vector of covariates, and a positive outcome variables with represents the time until occurrence of the event of interest and indicator variable that indicates whether the event observed is censored or not
- Goal: estimate that survival function, the probability of event occurrence after time t as a function of time t and patient covariates
- The probability of event within a suitable time window is used as a risk score based on which clinicians design therapies for patients
- Prognostic tools most often build upon the CPH model, and probabilistic frameworks based on parametric survival distributions
- The extensions cited above very flexibly model the interactions between a patient’s covariates and her survival but issue predictions from parametric functions that restrict the survival behavior over time (e.g the Weibull distribution)
- Paper proposes a sequential procedure with shallow survival trees grown on a data sample dependent on performance of previous trees (boosting)/. Bagging-based algorithms do not aim at correcting mistakes of previous hypotheses but aim to decrease overall variability of single tree predictions

#### Boosted Trees for Risk Prognosis

##### Measuring misdiagnoses
- The key to boosting architectures is the re-weighting of patients that are “misclassified” at each iteration 
- We propose to measure the “mis-classification” between the model survival predictions and the true survival state of the patient at a given time as the mean squared difference I(T_i > t) and predicted h_hat(t, X_i) survival outcomes over time, called the Brier Score
- For prediction over the range of all future times we aggregate the Brier Score over time resulting in the Integrated Brier Score
- For censored patients, the TTE of interest will be unobserved and thus we approximate the integrand by its empirical mean weighted by the inverse probability of censoring at each time t

##### Survival Tree Construction
- Leafs define a partition for the data and are responsible for making predictions and nodes guide examples towards appropriate leaves using binary splits based on boolean-valued rules
- Each node of our trees partitions the population in more homogeneous subsets based on the split that results in the greatest reduction in the deviance, assuming an exponential likelihood for the data
- The splitting criterion, as a function of the splitting covariate and cut-off value, then chooses the partition of the population (left and right children nodes in a tree representation) that maximizes the likelihood ratio statistic
- The deviance has the advantage of quantifying the goodness of fit of a single split with respect to the overall tree which is used to understand the benefit of a single split and therefore also the influence of the covariate used in that split

##### Terminal node predictions 
- Terminal node predictions of survival trees are made with the Kaplan-Meier estimator.  Let C_j denote the index set of patients with terminal node j, we compute survival prediction at terminal node j with the Kaplan-Meier estimator
- The terminal nodes partition the sample space in order to define the survival function for the tree

##### Ensemble Model
- Introduces two ensemble implementations: SurvivalBoost.R and SurvivalBoost.T, which differ in the interpretation of errors or misdiagnoses by individual hypotheses
- SurvivalBoost.R: The error of hypothesis on each patient is defined as the individual estimate of the IBS
- SurvivalBoost.T: Maps individual prediction errors to the set {0, 1}  by comparing to a threshold phi specified by the user, typically set by cross-validation and thus recovering the familiar classification setting of Adaboost
- SurvivalBoost Algorithm Outline
  - In each iteration m of the algorithm, following the error computation e_i for all I in the training set, we assign confidence Beta^m, a function of the average error with respect to the data distribution, to 
individual hypotheses adjusted to lie in there interval [0, 1] 
  - Random guessing corresponds to an average error of e_i = 1/3.  Increased weight is subsequently assigned to patients for which the error is largest, and lowered 
  - Increased weight is subsequently assigned to patients for which the error is largest, and lowered weight for more accurate predictions; the magnitude of this update is determined by the confidence of an individual tree
  - The algorithm is thus encouraged to focus on a potentially different subset of individuals sampled from the training set which become relatively harder to predict
  - We learn each individual hypothesis on a sub-sample of the original training data drawn with probabilities proportional tot he weights updated in the period 
  - Incorporating randomness as an integral of the procedure decreases computational complexity and tends to reduce correlation in solutions of successive weak learners which we observed empirically to impact performance favorably 
  - Final survival estimates result from a weighted average of individual survival trees with respect to the negative logarithm of their individual confidence

##### Covariate importance
- Covariates that are used to make splits that improve goodness of fit are informative of survival predictions in comparison to others with less evidence of improving the fit
- The deviance criterion can be used to measure this relevance.  Similarly , we measure overall covariate importance of an individual covariate by examining the sum of the likelihood ratio statistics, measuring goodness of fit, for each split using the covariate of interest in each tree of the ensemble
- This is in contrast to Random Survival Forests which uses a covariate permutation approach to identify changes in prediction error due to that covariate

#### Experimental Setup
- Present predictive performance results in comparison to competitive baseline algorithms on 7 different medical data sets related to cardiology

##### Evaluation
- In the presence of censoring the authors adopted two common approaches used in the literature: (1) the time-dependent concordance index (C-index).  This corresponds, it thus serves as a measure of the discriminative power of a model.  It is defined on [.5, 1], (2) also uses the IBS

##### Medical data studies
- Predictive performance is computed by 5-fold cross-validation.  Hyper-parameters of SurvivalBoost.R and SurvivalBoost.T were defaulted to 250 trees and a tree depth of 3 which were found to perform consistently well
- 
