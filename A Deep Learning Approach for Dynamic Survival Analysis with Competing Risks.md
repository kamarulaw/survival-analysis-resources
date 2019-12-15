## A Deep Learning Approach for Dynamic Survival Analysis with Competing Risks

##### **Anonymous authors**

### Definitions

 
### Abstract 
- Currently available survival analysis methods are limited in their ability to deal with complex, heterogeneous, and longitudinal data such as that available in primary care records, or in their ability to deal with multiple competing risks
- This paper develops a novel deep learning architecture that flexibly incorporates the available longitudinal data comprising various repeated measurements (rather than only the last available measurements) in order to issue dynamically updated survival predictions for one or multiple competing risks
- Unlike existing works in the SA on the basis of longitudinal data, the proposed method learns the TTE distributions without specifying underlying stochastic assumptions of the longitudinal or the TTE processes
- Designing the best clinical intervention for a patient is a daunting task, as the appropriate level of interventions or the corresponding outcome often depend on whether this patient is susceptible to or suffers from competing risks.  

### 1- Introduction 
- Designing the best clinical intervention for a patient is a daunting task, as the appropriate level of interventions  or the corresponding outcome often depends on whether this patient is susceptible to competing risks
- As more EHRs have been deployed in hospitals, modern clinical data in EHR often comprises longitudinal measurements; especially with chronic diseases
- Dynamic-DeepHit learns on the basis of available longitudinal measurements
- DDH learns on the basis of the available longitudinal measurements 
  - It is a flexible data-driven distribution of first hitting times of various events of interest
- Authors incorporate historical information of each patient using a recurrent neural network (RNN) structure in the shared subnetwork, which allows the method to update personalized and cause-specific risk predictions as additional measurements in a fully dynamic fashion
- In addition, the authors provide variable importance and dynamic risk predictions of the model to yield clinical usefulness for model interpretation 

### 2 - Background
- All theory

### 3 - Proposed Methods
- In this section, the authors describe the network architecture for SA with competing risks on the basis of longitudinal measurements.  Authors seek to train the network to learn an estimate of the joint distribution of the first hitting time and competing events given the longitudinal observations

#### 3.1 - Network Architecture

##### Shared Subnetwork
- The shared subnetwork employs a dynamic RNN structure to handle the longitudinal survival data with each subject having different numbers of measurements, observed at irregular time intervals
- The RNN structure allows DDH to capture correlations both within and across the longitudinal measurements and to unravel the temporal patterns that are common to the K competing risks

##### Cause-specific Subnetworks
- Each cause-specific subnetwork utilizes a feed-forward network to capture relationships between the cause-specific risk and the longitudinal measurements
- The inputs include the hidden states of the shared subnetwork and the measurements at the last observation.  This gives the subnetworks access to the learned common representation of the longitudinal history, which has progressed along with the trajectory of the past longitudinal measurements, as well as the latest observation

##### Output Layer 
- DDH employs a soft-max layer in order to summarize the outcomes of each cause-specific subnetwork, and to map into one output vector 

#### 3.2 Training DDH
- To train DDH, we minimize L_total that is specifically designed to handle longitudinal measurements and right-censoring.  The total loss function is the sum of the three terms L_total = L_1 + L_2 + L_3 
  - L_1: negative log-likelihood of the join distribution of the first hitting time and events
  - L_2: combines cause-specific ranking losses to concentrate the network on discriminating estimated individual risks for each cause
  - L_3: incorporates the prediction error on trajectories of time-varying covariates to regularize the network

##### Log-likelihood Loss
- The first loss function is the negative log-likelihood of the joint distribution of the first hitting time and corresponding event considering the right-censoring, conditioned on the measurements recorded until the last observation.  This is an extension to the survival setting with K competing risks on the basis of longitudinal measurements
- More specifically, for a subject how is not censored, it captures both the event that occurs and the time at which the event occurs, while for a subject who is censored, it captures the time at which the subject is censored

##### Ranking Loss
- To fine-tune the network, the authors utilize a ranking loss function which adapts the idea of concordance: a subject two dies at time tau should have a higher risk at time tao than a subject who survived longer than tau
- The longitudinal measurements of subjects can begin at any point in their lifetime or disease progression, and this makes direct comparison of the risks at different time points difficult to assess.  Thus, we compare the risks of subjects at times elapsed since their last measurements

##### Prediction Loss
- Longitudinal measurements on time-varying covariates, such as the trajectory of biomarkers and the presence of comorbidities over time, may be highly associated with the occurrence of clinical events
- Thus, we introduce an auxiliary task in the shared subnetwork, which is making step-ahead predictions on covariates of our interest, to find hidden representations of historical information and to regularize the overall network

#### 3.3 - Discussion on Scalability
- For accurate estimation of CIF’s, it is desirable to have the time interval for the time horizon discretization be fine rather than coarse since it maintains more information on TTE/censoring
- To prevent the proposed network from over-fitting, the authors do the following: 
  1. early stopping based on the performance metric of interest
  2. L_1 regularization over weights in the cause-specific subnetwork and the output layer

### 4 - Experiments
- Throughout the experiments, the authors use two real-world medical datasets comprising two competing risks to evaluate our prop[osed method against the state-of-the-art benchmarks

#### 4.1 - Dataset

##### Cystic Fibrosis Data
- Retrospective longitudinal data from the UK Cystic Fibrosis Registry, sponsored and hosted by the UK Cystic Fibrosis Trust
- Authors divided the mortality cause into (i) deaths due to respiratory failures, and (ii) deaths due to other causes including CF-associated liver failure
- It is important that patients who are at risk of respiratory failure be provided with a joint prognosis of morality due to other causes in order to properly manage therapeutic interventions; lung transplantation is particularly recommended for patients with end-stage respiratory failure
- Since CF is a genetic disease where the longitudinal measurements of patents can begin at any point in their disease progression, throughout the experiments, all patients are aligned based on their date of birth to synchronize the time to compare risk predictions made at different ages  

##### Mayo Clinic Primary Biliary Cirrhosis Data
- This data is from the Mayo Clinic trail in primary biliary cirrhosis of the liver, which comprises of 312
- Two competing events are considered in this experiment
  1. deaths
  2. patients who underwent liver transplantation

#### 4.2 Benchmarks
- DDH was compared with: cs-Cox, RSF, DeepHit, JM, Dynamic-Exp
- DeepHit and Dynamic-Exp are introduced to highlight the gain of the proposed architecture

#### 4.3 Discriminative Performance
- In SA, predictions of survival models are most commonly assessed and compared with respect to how well the survival models discriminate individual risks.  To assess the discriminative performance of the various models, we use a cause-specific time-dependent concordance index, which is an extension adapted to the competing risks setting on the basis of longitudinal measurements

#### 4.4 - Variable Importance via Partial Difference
- In this subsection, the authors utilize a post-processing statistic that can be used by clinicians to interpret predictions issued by DDH and to understand the associations of covariates and the survival
- Section goes over a lot of theory 

##### 4.5 - Dynamic Risk Prediction 
- At run-time, DDH issues cause specific risk predictions for each subject incorporating his/her medical history; we illustrate dynamic risk predictions and the trajectory of the hidden states for representative patients of the CF dataset
- Whenever a new observation is made, DDH updates the predictions that start from 0 due to the fact that this patient is alive at the time of the measurement
- However, the predicted risks can vary significantly depending on the new measurements

### 5 - Conclusion 
- Authors developed a novel approach to perform dynamic survival analysis with competing risks on the basis of longitudinal data
- Network was trained by leveraging a combination of loss functions that capture the right-censoring and the associations of longitudinal measurements, both of which are inherent in TTE data
