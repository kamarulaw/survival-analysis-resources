## Countdown Regression: Sharp and Calibrated Survival Predictions

##### **A. Avati, T. Duan, K. Jung, N. H. Shah, A. Ng (2018)**

Summary: In regression problems, it has been shown that optimizing the continuous ranked probability score (CPRS) instead of maximum likelihood leads to sharper prediction distributions while maintaining calibration.  Paper introduces the Survival-CRPS.  Authors present the Survival-AUPRC evaluation metric, an analog to area under the precision-recall curve.  Authors apply these ideas by building a RNN for mortality prediction, using an EHR dataset covering millions of patients.  

### Definitions
- **Censored data**: Cases in which the event was not observed, but we know that the event did not occur up to a certain time
  - **Right censoring**: a data point is above a certain value but it is unknown by how much
  - **Interval censoring**: A data point is somewhere on an interval between two values
- **Proper scoring rule**: Forces the model to output calibrated probabilities (eg: log scoring rule, equivalent to the MLE objective)
- **Calibration**: Asses how well forecasted event probs match up to observed event probs.  It is crucial to development of useful predictive models, especially for clinical decision making (statistical consistency between the distributional forecasts and the observations and is a joint property of the predictions and the events that materialize)
- **Sharpness**: The concentration of the predictive distributions and is a property of the forecasts only
- **Probability Integral Transform**: Relates to the result that data values that are modeled as being random variables from any given continuous distribution can be converted to random variables having a standard uniform distribution (an alternative to the proper scoring rule for checking calibration and sharpness)

### (1) Introduction 
- Historically, prognosis scores have served as simple tools to stratify patient risk within a predefined time window.  However, such models tend to be too simplistic to be widely useful
- They are often estimated from a large population of patients, and do not take into account patient-specific information to make individualized predictions
- One way to obtain patient-specific survival predictions is to treat the problem as probabilistic classification: that is, training a binary classifier to predict outcomes of event by a particular time of interest
  - Drawbacks: 1) Model is specific to the time of interest it was trained upon. 2) Not usually possible to use data on all patients - eg: if a patient has only 3 months of history in the EHR system, it is neither possible to include that patient as a positive case nor a negative case in the 1-year mortality prediction risk, 3) The process of constructing the data set implicitly conditions on the future outcome to select prediction times
- Alternative approach t the problem is survival prediction: that is, predicting time to event by estimating a distribution over future time.  In this setting, traditional survival analysis methods such as the CPH model, or accelerated failure time models are capable of handling data with censored observations
- This addresses concerns raised by the classification approach, but there are a few nuances
  1) Traditional models typically make strong assumptions, such as proportional hazards or linearity
  2) Challenges of low prevalence often arise when these methods are applied to large-scale observational datasets with heavy censoring, which is the case in real EHR data
  3) These SA methods are typically evaluated as point estimates of risk such as 10-year prob events, rather than holistic measures of quality of the predicted distributions
- Evaluation of predictive performance is assessed by the paradigm of maximizing the sharpness of the predictive distribution, subject to calibration.  The intuition behind this paradigm is that probabilities have to be calibrated in order to be correct
- Main Contributions: 
  1) Introduce the proper scoring rule Survival-CRPS as an objective score in survival prediction, 2) Propose a new metric, Survival-AUPRC, inspired by the paradigm of maximizing sharpness subject to calibration, 3) Give practical recommendations for the mortality prediction risk, 4) Employ the above techniques and demonstrate their efficacy by training a DRNN model for accurate survival prediction of patient mortality using EHR data

### (2) Countdown Regression
- Parametric survival prediction methods model the time to an event of interest with a family of probability distributions, uniquely identified by the distribution parameters
- Every survival function has a corresponding CDF, and PDF.  The choice of the family of probability distribution implies assumptions made about the nature of the data generating process
- Traditional methods in SA are designed to handle right-censored outcomes, but we observe that in my many common scenarios outcomes are actually interval-censored 
- A scoring rule is a measure of the quality of a probabilistic forecast.  A forecast over a continuous outcome is a probability density function over all possible outcomes
- Scoring rule takes a predicted distribution and an actual outcomes and returns a loss 
- Since sharpness is only a function of the predicted distributions, a measure of sharpness is only meaningful if the model is sufficiently calibrated 
- By exploring the full range of precision from 0 to 1, we obtain the Survival Precision Recall Curve.  The area under this curve measures how quickly predicted mass concentrates around the true outcome as we expand the precision window
- Authors applied the techniques to the mortality prediction task by building a multilayer RNN with parameters theta that takes as input a sequence of features (in this case, information about a patient recorded in the EHR, for each interaction they had with the hospital) to predict parameters of a parametric probability distribution over time to death at each tilmestep
- The approach is similar to the Weibull time to event RNN, through this one generalizes to any choice of noise distribution
- The distributions that are output in each tilmestep are used to construct an overall loss
- Common parametric distributions over time to event used in traditional SA models include the Weibull, log-normal, log-logistic, and game (in order to be sufficiently expressive in model space, we seek distributions with at least two parameters)

### (3) Experiments 
- Authors ran experiments for the mortality prediction task to evaluate four different training objectives, MLE (RIGHT, INTVL), and scoring based loss (RIGHT, INTVL).  For interval censoring authors assumed a maximum lifespan of A = 120 years
- Input at each time step consisted of both real values and discrete valued (eg: ICD codes)
- Discrete data is embedded into a trainable real-valued vector space, and vectors corresponding to the codes recorded at a given tilmestep are combined into a weighted mean by a soft self-attention mechanism
- All real valued inputs are appended to the averaged embedding vector
- Also provide the real valued features to every layer by appending them to the output of previous layer.  The input vector feeds into a fully connected layer, followed by multiple recurrent layers
- We use the Swish activation function and layer normalization at every layer
- The final layer in each branch has scalar output, optionally enforced positive with the softplus function, Bernoulli dropout was used at all fully connected layers, and Variational RNN dropout in the recurrent layers, with a dropout prob of .5, and optimization is performed using the Adam optimizer, with a fixed learning rate of 1e-3
- Used EHR from the STARR Data Warehouse for training and evaluation 
- Results were good

### (4) Related Work
- Recent works have demonstrated potential to significantly improve patient care by making predictions with deep learning models on EHR data, but these have been limited in treating the task as binary classification over a fixed time frame 
- Predicting survival curves instead of dichotomous outcomes have been explored, but only over finite length horizons
- Deep survival analysis has been proposed, but it is limited to a fixed shape Weibull  
- DeepSurv used a Cox proportional hazards model, which similarly makes a set of inflexible assumptions
- WRRE-RNN has a similar network architecture to the one in the paper, but is also limited to a Weibull distribution

### (5) Conclusion
- Better survival prediction models can be build by exploring objectives beyond MLE
