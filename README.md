# Insurance Claims Model

Insurance pricing requires estimating the expected cost of future claims for each policyholder. Rather than modelling total claim cost directly, it is typically decomposed into two separate components:

- Claim Frequency - the expected number of claims made by a policyholder.
- Claim Severity - the expected cost of each claim, conditional on a claim occurring.

This project develops an actuarial pricing framework using Generalised Linear Models (GLMs) to model these two components independently before combining them to estimate pure premiums. The implementation follows a standard actuarial pricing workflow including data validation, preprocessing, model fitting, diagnostics, and interpretation of rating factors.

# Dataset

This project uses the French Motor Third-Party Liability dataset.
The dataset contains policy-level information including:

- Driver characteristics (Age)
- Vehicle characteristics (Age, Power)
- Policy exposure
- Number of claims
- Claim amounts

# Methodology

1. Before modelling, the data was cleaned and validated by:
- Checking for missing values.
- Removing invalid observations such as infinite values
- Validating categorical variables
- Calculating claim frequency

2. Claim frequency was modelled using a Poisson Generalised Linear Model.
The response variable is "Number of Claims"
An exposure offset "log(Exposure)" was included to account for differing policy durations, allowing annualised claim frequencies to be estimated consistently across policies.

3. Claim severity was modelled using a Gamma GLM.
Only policies with positive claim amounts were included, since severity is conditional on a claim occurring.
The response variable is "Average Claim Cost"

4. The two models were combined to estimate Pure Premium = Expected Frequency × Expected Severity
This represents the expected claims cost for each policy.

# Model Assumptions

Poisson GLM: The frequency model assumes claims occur independently and follow a Poisson distribution.
Gamma GLM: The severity model assumes claim costs occur independently, are positive, and follow a Gamma distribution.

Furthermore, the model assumes that:
- Policyholders are independant.
- Predictor variables capoture the major sources of risk.
- Historical claims are representative of future experience.

# Model Validation

The models were assessed using residual plots and comparison of observed vs predicted outputs.

![alt text](https://github.com/MattWebbb/InsuranceClaimsModel/blob/main/Screenshots/Residuals%20Frequency.png "Poisson GLM Residuals vs Fitted Values")

![alt text](https://github.com/MattWebbb/InsuranceClaimsModel/blob/main/Screenshots/Residuals%20Severity.png "Gamma GLM Residuals vs Fitted Values")

Residual plots for both models show patterns consistent with the characteristics of insurance claims data. The Poisson GLM residuals show the expected fan shape at low fitted values, reflecting the skewed distribution of claim counts across policies. The Gamma GLM residuals are well-behaved across the majority of observations, with a small number of large outliers reflecting extreme individual claims that are inherent to insurance severity data.

![alt text](https://github.com/MattWebbb/InsuranceClaimsModel/blob/main/Screenshots/Actual%20vs%20Predicted%20Frequency.png "Gamma GLM Observed vs Predicted")

![alt text](https://github.com/MattWebbb/InsuranceClaimsModel/blob/main/Screenshots/Actual%20vs%20Predicted%20Severity.png "Poisson GLM Observed vs Predicted")

The fitted models identified several variables with strong influence on expected claim costs, including:

- Driver Age
- Vehicle Age
- Bonus-Malus

![alt text](https://github.com/MattWebbb/InsuranceClaimsModel/blob/main/Screenshots/Pure%20Premium%20By%20Driver%20Age.png "Premium by Driver Age")

![alt text](https://github.com/MattWebbb/InsuranceClaimsModel/blob/main/Screenshots/Pure%20Premium%20By%20Vehicle%20Age.png "Premium by Vehicle Age")
