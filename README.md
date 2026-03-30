# Home Credit Default Risk Portfolio

Individual portfolio repository for my work on the Home Credit Default Risk project in `IS 6812 Capstone`.

This project asked a practical lending question: can we use alternative and behavioral data to predict default risk for applicants with limited traditional credit history? My work focused on building a reproducible feature pipeline, comparing candidate models, selecting a business operating threshold, and documenting the final model in a business-facing model card.

## Business Summary

Home Credit’s business challenge is balancing two competing goals:

- Expand access to credit for thin-file borrowers.
- Control losses from default.

I approached this as a credit-risk ranking problem. The core output is a predicted probability of default for each applicant, which can be used to support approval decisions, manual review, and broader lending strategy.

## Final Result

- Best model in my workflow: enhanced `XGBoost`
- Final cross-validated AUC in the model card: `0.7511`
- Kaggle public AUC: `0.73565`
- Kaggle private AUC: `0.72370`
- Recommended operating threshold: `12.0%` predicted default probability

At that operating threshold, the model card estimated:

- Approval rate of `86.1%`
- Recall of `42.7%` for defaults
- Expected value of about `$776,256` per 1,000 screened applications under the public benchmark assumptions documented in the model card

## Portfolio Notebooks

These notebooks represent my individual work product for the project.

| Notebook | Purpose |
|---|---|
| [EDA_sstrzalka.qmd](./EDA_sstrzalka.qmd) | Exploratory analysis of default patterns, missingness, and early risk signals |
| [data_preparation_sstrzalka.qmd](./data_preparation_sstrzalka.qmd) | Reproducible train/test-safe feature engineering and data preparation pipeline |
| [modeling_sstrzalka.qmd](./modeling_sstrzalka.qmd) | Candidate model comparison, imbalance handling, supplementary-data evaluation, and Kaggle submission generation |
| [model_card_sstrzalka.qmd](./model_card_sstrzalka.qmd) | Business-facing model documentation including thresholding, SHAP, fairness review, and limitations |

GitHub renders `.qmd` source files directly, so the repository keeps the code-first notebooks rather than relying on HTML outputs.

## What I Built

### 1. Reproducible feature engineering

I created a preparation workflow that:

- cleaned the `DAYS_EMPLOYED = 365243` anomaly
- imputed missing values using training-derived statistics
- created missingness indicators
- engineered affordability, stability, and interaction features
- aggregated supplementary tables including bureau, previous applications, installments, POS cash, and credit card history
- enforced train/test column consistency to reduce leakage risk

### 2. Model comparison with a clear baseline

I compared:

- majority-class and simple logistic baselines
- logistic regression on baseline, application-only, and full feature sets
- random forest
- XGBoost
- class-imbalance strategies including downsampling, upsampling, and SMOTE

The strongest model in my workflow was the enhanced XGBoost model with supplementary POS and credit-card features.

### 3. Business-oriented model documentation

The model card goes beyond accuracy. It addresses:

- who should use the model
- what decision threshold is economically reasonable
- how the model behaves using SHAP explanations
- how technical features translate into adverse-action style reasons
- where fairness gaps appear across demographic groups
- what risks would need to be addressed before deployment

## Key Findings

- External credit features (`EXT_SOURCE_*`) remained the strongest predictors of default.
- Affordability ratios such as credit-to-income and annuity-to-income added meaningful risk signal beyond raw amounts.
- Supplementary behavioral data improved performance over application-only inputs.
- Public leaderboard performance overstated final holdout performance slightly, which is a useful reminder not to overfit to leaderboard feedback.
- Fairness review showed notable approval-rate differences across education groups, so the current model should be treated as decision support rather than a production auto-decline tool.

## Interview Talking Points

If I were discussing this project in an interview, these are the parts I would emphasize:

- I built a train/test-safe feature engineering pipeline rather than doing one-off notebook cleaning.
- I compared simpler and more complex models instead of assuming gradient boosting would automatically win.
- I translated model performance into business terms by selecting a threshold based on lending economics rather than defaulting to `0.50`.
- I added explainability and fairness analysis because deployment quality is broader than AUC.
- I documented model limitations and governance concerns in a model card written for a business stakeholder.

## Reproducibility

### Data

The raw Home Credit competition files are not committed because of size and licensing constraints. To run the notebooks locally:

1. Download the Home Credit Default Risk data from Kaggle.
2. Place the raw files in `Data/`.
3. Render the notebooks in sequence or run the modeling and model card notebooks directly if prepared data already exist.

### Local-only artifacts

The repository ignores large or generated files such as:

- `Data/`
- `prepared_data/`
- `models/`
- `*.csv`
- rendered HTML outputs

This keeps GitHub focused on the code and documentation that make up the portfolio.

## Repository Structure

| Path | Description |
|---|---|
| [README.md](./README.md) | Portfolio landing page and project summary |
| [EDA_sstrzalka.qmd](./EDA_sstrzalka.qmd) | Exploratory analysis notebook |
| [data_preparation_sstrzalka.qmd](./data_preparation_sstrzalka.qmd) | Data preparation notebook |
| [modeling_sstrzalka.qmd](./modeling_sstrzalka.qmd) | Modeling notebook |
| [model_card_sstrzalka.qmd](./model_card_sstrzalka.qmd) | Final model card |
| `.gitignore` | Excludes data, artifacts, and generated outputs from version control |

## Tools

Primary tools used in this project:

- `R`
- `tidyverse`
- `caret`
- `xgboost`
- `pROC`
- `Quarto`

## Status

This repository is complete as a course portfolio project. The next steps, if this were moving toward production, would be:

- retrain on the full prepared dataset
- calibrate predicted probabilities
- validate threshold assumptions against real portfolio economics
- expand fairness testing and compliance review
- separate ranking, pricing, and adverse-action workflows more explicitly
