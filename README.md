# Home Credit Default Risk Prediction

**IS 6812 Capstone Project - Spring 2026**  
**Author:** Sarah Strzalka

## Project Overview

This project develops a predictive model to assess default risk for loan applicants with limited or no credit history. Using historical Home Credit data, the model aims to improve lending decisions by identifying creditworthy applicants who would traditionally be rejected due to insufficient credit history.

**Business Objective:** Enable Home Credit to expand financial inclusion while maintaining responsible risk management by accurately predicting loan default probability for underserved populations.

**Primary Metric:** ROC-AUC (Receiver Operating Characteristic - Area Under Curve)

## Key Findings from EDA

The exploratory data analysis revealed several important patterns:

- **Class Imbalance:** 92% non-default vs 8% default rate
- **Strongest Predictors:** External credit scores (EXT_SOURCE variables), age, employment duration
- **Important Ratios:** Credit-to-income ratio shows better separation than absolute amounts
- **Missing Data:** Structural missingness in housing and asset variables; missing patterns are informative
- **Data Quality Issues:** DAYS_EMPLOYED anomaly (365243) represents unemployment or missing status
- **Categorical Signals:** Education, occupation, income type, and housing type show meaningful variation in default rates

## Data Preparation Pipeline

### Overview

`data_preparation_sstrzalka.qmd` implements a production-ready data preparation pipeline that transforms raw Home Credit data into model-ready features while ensuring train/test consistency to prevent data leakage.

### What It Does

The pipeline performs the following operations:

**1. Data Cleaning**
- Handles DAYS_EMPLOYED anomaly by creating indicator variable and replacing with NA
- Creates missing data indicators for key predictive features
- Imputes missing values using training-derived medians for numeric variables and modes for categorical variables

**2. Feature Engineering - Demographics**
- Converts day-based features to years (age, employment duration, registration age, ID document age, phone change recency)
- Creates age bins (18-25, 26-35, 36-45, 46-55, 56-65, 65+)
- Creates employment tenure bins (0-1yr, 1-3yr, 3-5yr, 5-10yr, 10+yr)

**3. Feature Engineering - Financial Ratios**
- Core affordability metrics: credit-to-income, annuity-to-income, loan-to-value
- Coverage metrics: payment rate, income-to-annuity, goods-price-to-income
- Per-capita metrics: credit and income per family member
- External score combinations: mean and weighted average
- Risk indicators: high-risk income type flag, document submission count

**4. Feature Engineering - Interactions**
- Age × income interaction
- Age × credit-to-income interaction
- Employment × income interaction
- External score × credit ratio interaction
- Age squared for non-linear effects

**5. Supplementary Data Aggregation**
- **Bureau data (17 features):** Credit counts (active, closed, bad debt), total credit and debt amounts, overdue amounts and ratios, credit diversity metrics
- **Previous applications (15 features):** Application counts by status, approval rate, credit and application amounts, down payment information, processing time
- **Installment payments (8 features):** Late payment rates, days past due metrics, payment difference totals

**6. Train/Test Consistency Management**
- Stores imputation values and feature names from training data
- Applies identical transformations to test data using stored parameters
- Ensures matching column sets between train and test datasets

### How to Run

The Quarto document can be rendered to HTML or used as a code source.

**Render the document:**
```r
# In RStudio/Positron, open data_preparation_sstrzalka.qmd and click "Render"
# Or from command line:
quarto render data_preparation_sstrzalka.qmd
```

**Use as code source:**
```r
# Load data
data <- load_all_data("Data")

# Prepare training data
train_result <- prepare_data(
  app_data = data$app_train,
  bureau_data = data$bureau,
  prev_app_data = data$prev_app,
  install_data = data$installments,
  is_train = TRUE
)

# Prepare test data using training parameters
test_result <- prepare_data(
  app_data = data$app_test,
  bureau_data = data$bureau,
  prev_app_data = data$prev_app,
  install_data = data$installments,
  is_train = FALSE,
  prep_params = train_result$params
)

# Save prepared datasets
save_prepared_data(train_result, test_result, output_dir = "prepared_data")
```

### Key Functions

| Function | Purpose |
|----------|---------|
| `prepare_data()` | Main pipeline orchestrating all preparation steps |
| `clean_employment_anomaly()` | Handles DAYS_EMPLOYED = 365243 anomaly |
| `impute_missing()` | Fills missing values with train-derived statistics |
| `create_missing_indicators()` | Creates binary flags for missing values |
| `engineer_demographic_features()` | Creates age, employment, and time-based features |
| `engineer_financial_ratios()` | Creates affordability and risk ratios |
| `engineer_interactions()` | Creates interaction terms between key predictors |
| `aggregate_bureau_data()` | Summarizes credit bureau history |
| `aggregate_previous_applications()` | Summarizes Home Credit application history |
| `aggregate_installments()` | Calculates payment behavior metrics |
| `load_all_data()` | Loads all CSV files |
| `save_prepared_data()` | Saves prepared datasets and parameters |

### Inputs

The pipeline requires the following CSV files in the `Data/` directory:

- `application_train.csv` - Training dataset with loan outcomes
- `application_test.csv` - Test dataset without loan outcomes
- `bureau.csv` - Credit bureau data
- `previous_application.csv` - Previous Home Credit applications
- `installments_payments.csv` - Payment history for previous loans

### Outputs

The pipeline produces:

- `train_prepared.csv` - Cleaned and engineered training dataset (194 columns)
- `test_prepared.csv` - Cleaned and engineered test dataset (193 columns, excluding TARGET)
- `prep_params.rds` - Stored parameters for reproducibility (imputation values, feature names)

### Important Notes

**Data Leakage Prevention**
- All statistics (means, medians, modes) are computed only from training data
- Test data uses stored training parameters
- Never fit on test data

**Column Consistency**
- Script ensures train and test have identical columns except TARGET
- Missing columns in test are added with value 0
- Column order matches between datasets

## Dependencies

```r
library(tidyverse)
```

Install with: `install.packages("tidyverse")`

## Data Files

Data files are not tracked in this repository due to size constraints. To run this project:

1. Download the Home Credit Default Risk dataset from Kaggle
2. Place CSV files in the `Data/` directory
3. Run the data preparation script

---

*Last Updated: February 7, 2026*
