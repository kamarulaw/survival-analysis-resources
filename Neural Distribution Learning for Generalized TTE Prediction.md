## Neural Distribution Learning for Generalized TTE Prediction

##### **Anonymous Authors**

### Abstract 
- TTE is important, however due to censoring and irregularly sampled sequences, the prediction has resulted in limited success only for particular tasks, architectures, and data 
- Unlike previous work, the proposed method can use asynchronously sampled features for censored, discrete, and multivariate data

### Definitions
- **Censoring**: Form of missing data problem in which TTE is not observed for reasons such as termination of study before all recruited subjects have shown the event of interest or the subject has left the study prior to experiencing an event
- **Right Censored**: We know the event will take place, but we do not know when it will occur
- **Semi-Parametric**: Model that has parametric and non-parametric components
- **Non-Parametric**: Not involving any assumptions about about the form of of parameters of a frequency or distribution
- **Variational Bayesian Methods**: Family of techniques for approximating intractable integrals arising in Bayesian inference and machine learning.  Typically used in complex statistical models consisting of observed variables as well as unknown parameters and latent variables.  Primarily used for two purposes
  1. To provide an analytical approximation to the posterior probability of the unobserved variables
  2. To derive a lower bound on the marginal likelihood (often called the “evidence”) of the observed data (the marginal probability of the data given the model, with marginalization performed over unobserved variables) 
- **Stochastic formulation**:
- **Hazard Function**: the event rate at time t conditional on survival until time t or later
- **Cumulative Hazard Function**: can be thought of as the accumulation of hazard over time 
- **F1 Score**: 
- **Calibration**: 
 
### 1 - Introduction
- Many real world problems can be formulated as TTE prediction problems, forecasting the time taken until an event of interest happens in the future
- Examples are predicting time to the onset of a particular disease, predicting when a machine fails or predicting future user logins
- The data often consists of temporal features and recurrent events which are sparsely and irregularly sampled over time.  This makes the data challenging to work with
- Furthermore, the main challenge is that if we do not observe a terminating event for a sequence the TTE becomes right censored
- There are multiple proposed solutions that take account for censoring including classification approaches, predicting TTE point-wise, ranking the risk of subjects (semi-parametric and non-parametric), or estimating the target and feature distributions jointly (variational, stochastic formulations)
- But most methods have problems with tight couplings to particular types of queries, restrictive, and data-dependent loss formulations, complex model architectures, or improper handling of censored data
- Paper believes that the most straightforward approach has not been examined thoroughly enough
- Paper also mentions a lot of other methods that can be used to 
- Paper shows the benefits of defining distributions in terms of cumulative hazard functions, and defines the technique as HazardNet

### 2 - Related Work
- The methods for censored data are usually based on classical semi-parametric Cox-models for continuous target values.  Others formulate it as a classification or multi-task learning problem
- Paper provides other methods to help solve these problems, but states that they all have limitations
- The authors did not find any in-depth experiments or convincing results on how to evaluate performance, especially calibration for probabilistic predictions  
- Authors also state that they did not find any general discussions any in-depth examples using the Weibull, Pareto, LogLogistic, their mixtures, or other distributions in discretized form so by thoroughly investigating this method they hope to establish a a strong baseline for tte predictions.  The implementation will be released online  

### 3 - HazardNet
- While many approaches have focused on specific data distributions or tasks, the goal of the paper is to provide a general framework by focusing on the problem formulation for TTE-prediction itself.  The problem is presented as a distribution learning problem
- The basic idea is a density network coupled with a parametric survival approach
- Let a neural network predict parameters of some TTE-distribution and train it using log-likelihood for censored data
- In every step we let the predictor map features x_t are used to predict the parameters that determine the shape of the distribution
- Authors employ the use of cumulative hazard functions to define probability distributions.  In the survival analysis context it is common to focus on hazard functions, but the authors thought using the integral would be more useful (CHF) 
- A CHF is a straightforward way of representing a positive distribution
- Worth noting that a CHF is a monotonically increasing positive function
- CHFs make a good abstraction for implementation because since the conditions required to prove it is a monotonically increasing function are easily provable
- Another clear benefit of the Cumulative Hazards - perspective of distributions is that it simplifies the creation of new distributions
- Recent developments on simplifying probabilistic programming have made it possible to effectively compose and work with distributions and their mixtures 
- It is easy to show that the space of CHF’s are closed under strictly increasing mappings, composition, multiplication, addition, and multiplication with positive scalars

##### 3.3 - Censored Log-Likelihood
- Under milder assumptions discussed here, we can accurately do MLE using censored data utilizing censored log-likelihood
- Come back to derivations later

### 4 - Experimental Results
- Authors compared a diverse number of time to event distributions learned with different network architectures on three different datasets against a binary baseline

##### 4.2 - Baseline: Binary Window Model
- In order to show there reliability of the model we need to show proper calibration of predicted probabilities and good discriminatory performance
- Done by training individual binary models predicting probability of event within a threshold for set time steps ahead
- While technically not a TTE model, in practice the binary window model is the dominant modeling method for TTE problems
- No other relevant baseline could be justifiable compared for discrete TTE over different network architectures and different probabilistic queries
- Different architectures performed differently on different datasets
- HazardNet was better calibrated (lower min ECE) and had better disciminative performance (higher calibration independent AUC).  
- The results are most pronounced for the stronger models (CNN, RNN) implying that the model is most of the time better or equal and less prone to overfitting

### 6 - Summary and Conclusion
- In the experiments the authors trained the proposed model and queried it on classification subtasks for which baseline binary models were explicitly trained for
- Since the model predicts a distribution one could easily calculate other meaningful quantities 
- Despite being more excessive there is no proof that MixedHazzard functions perform better 
