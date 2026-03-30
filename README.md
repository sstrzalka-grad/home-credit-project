# Home Credit Default Risk Portfolio

Individual portfolio repository for my work on the Home Credit Default Risk project in `IS 6812 Capstone`.

This project asked a practical lending question: can we use alternative and behavioral data to predict default risk for applicants with limited traditional credit history? My work focused on building a reproducible feature pipeline, comparing candidate models, selecting a business operating threshold, and documenting the final model in a business-facing model card.

## Business Problem and Project Objective

Home Credit's business challenge is balancing two competing goals:

- expand access to credit for thin-file borrowers
- control losses from default

The project objective was to build a default-risk model that could help the business rank applicants, support approval decisions, and make lending tradeoffs more explicit. Rather than treating the assignment as a pure Kaggle exercise, I treated it as a credit-risk decision problem with business, explainability, and fairness implications.

## Final Result

- Best model in my workflow: enhanced `XGBoost`
- Final cross-validated AUC in the model card: `0.7511`
- Kaggle public AUC: `0.73565`
- Kaggle private AUC: `0.72370`
- Recommended operating threshold: `12.0%` predicted default probability

At that operating threshold, the model card estimated:

- approval rate of `86.1%`
- recall of `42.7%` for defaults
- expected value of about `$776,256` per 1,000 screened applications under the public benchmark assumptions documented in the model card

## My Solution to the Business Problem

My solution was to frame the problem as a default-risk ranking and thresholding workflow rather than a simple yes-or-no classifier. I built a feature-rich training dataset from application, bureau, prior loan, installment, POS cash, and credit-card data; compared several candidate models; selected the strongest XGBoost model; and translated the final score into a business recommendation using threshold analysis, explainability, and fairness review.

## Portfolio Notebooks

These notebooks represent my individual work product for the project.

| Notebook | Purpose |
|---|---|
| [EDA_sstrzalka.qmd](./EDA_sstrzalka.qmd) | Exploratory analysis of default patterns, missingness, and early risk signals |
| [data_preparation_sstrzalka.qmd](./data_preparation_sstrzalka.qmd) | Reproducible train/test-safe feature engineering and data preparation pipeline |
| [modeling_sstrzalka.qmd](./modeling_sstrzalka.qmd) | Candidate model comparison, imbalance handling, supplementary-data evaluation, and Kaggle submission generation |
| [model_card_sstrzalka.qmd](./model_card_sstrzalka.qmd) | Business-facing model documentation including thresholding, SHAP, fairness review, and limitations |

GitHub renders `.qmd` source files directly, so the repository keeps the code-first notebooks rather than relying on HTML outputs.

## Professional Use of Notebooks

The notebooks are organized as a portfolio, not as scratch work. Each notebook has a distinct role in the project:

- `EDA_sstrzalka.qmd` establishes the business context, data patterns, and early hypotheses
- `data_preparation_sstrzalka.qmd` documents a reproducible pipeline rather than one-off cleaning steps
- `modeling_sstrzalka.qmd` shows baseline comparison, model tradeoffs, and selection logic
- `model_card_sstrzalka.qmd` translates the final model into deployment-oriented language for a business stakeholder

Together, they show the full analytics lifecycle: exploration, preparation, modeling, and communication.

## My Contribution

This repository contains my individual work, not just a group deliverable. My contributions included:

- exploratory data analysis to identify early default signals and missing-data patterns
- a reproducible feature-engineering pipeline that preserves train/test separation
- model comparison across logistic regression, random forest, and XGBoost
- supplementary feature construction from multiple transactional tables
- a model card that translated technical results into business language, threshold decisions, SHAP explanations, and fairness findings

## Business Value of the Solution

The business value is not just that the model predicts defaults better than a baseline. The stronger value is that it gives a lender a structured way to:

- expand credit more confidently to applicants with thin traditional credit files
- quantify the tradeoff between approval volume and expected losses
- explain why a model is making higher-risk predictions
- surface fairness and governance issues before deployment

That makes the work more useful to a real lending organization than a leaderboard score alone.

## Key Findings

- External credit features (`EXT_SOURCE_*`) remained the strongest predictors of default.
- Affordability ratios such as credit-to-income and annuity-to-income added meaningful risk signal beyond raw amounts.
- Supplementary behavioral data improved performance over application-only inputs.
- Public leaderboard performance overstated final holdout performance slightly, which is a useful reminder not to overfit to leaderboard feedback.
- Fairness review showed notable approval-rate differences across education groups, so the current model should be treated as decision support rather than a production auto-decline tool.

## Project Difficulties

The most important difficulties in the project were:

- severe class imbalance, which made accuracy misleading and required threshold and recall-focused analysis
- extensive missingness and anomaly handling, especially around employment and sparse bureau-style variables
- joining multiple supplementary tables without creating leakage or train/test mismatch
- balancing leaderboard performance against a model that would still be explainable and defensible in a lending context
- translating technical model outputs into language appropriate for business stakeholders and adverse-action style reasoning

## What I Learned

This project pushed me beyond just fitting a model. The main things I learned were:

- strong portfolio work needs reproducibility, not just a final score
- good credit-risk modeling requires careful feature engineering and threshold design, not only algorithm selection
- public leaderboard results can be optimistic, so validation discipline matters
- explainability and fairness analysis are part of model quality when the use case affects real people
- a professional analytics portfolio should communicate business impact, tradeoffs, and limitations clearly

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
