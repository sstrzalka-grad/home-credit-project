# Home Credit Default Risk Prediction

**IS 6812 Capstone Project - Spring 2026**  
**Author:** Sarah Strzalka

## Project Overview

This project develops a predictive model to assess default risk for loan applicants with limited or no credit history (thin-file and no-file borrowers). Using historical Home Credit data, the model aims to improve lending decisions by identifying creditworthy applicants who would traditionally be rejected due to insufficient credit history.

**Business Objective:** Enable Home Credit to expand financial inclusion while maintaining responsible risk management by accurately predicting loan default probability for underserved populations.

**Primary Metric:** ROC-AUC (Receiver Operating Characteristic - Area Under Curve)

## Repository Structure

```
home-credit-project/
├── Data/                           # Raw data files (not tracked in git)
│   ├── application_train.csv
│   ├── application_test.csv
│   ├── bureau.csv
│   ├── previous_application.csv
│   └── installments_payments.csv
├── prepared_data/                  # Processed data (not tracked in git)
│   ├── train_prepared.csv
│   ├── test_prepared.csv
│   └── prep_params.rds
├── EDA_sstrzalka.qmd              # Exploratory Data Analysis (Quarto)
├── data_preparation_sstrzalka.qmd  # Data preparation pipeline (Quarto)
├── .gitignore                      # Files to exclude from version control
└── README.md                       # This file
```

## Key Findings from EDA

The exploratory data analysis (`EDA_sstrzalka.qmd`) revealed several important patterns:

- **Class Imbalance:** 92% non-default vs 8% default rate
- **Strongest Predictors:** External credit scores (EXT_SOURCE variables), age, employment duration
- **Important Ratios:** Credit-to-income ratio shows better separation than absolute amounts
- **Missing Data:** Structural missingness in housing/asset variables; missing patterns are informative
- **Data Quality:** DAYS_EMPLOYED anomaly (365243 = ~1000 years) represents unemployment status
- **Categorical Signals:** Education, occupation, income type, and housing type show meaningful variation in default rates

## Data Preparation Document

### Overview

`data_preparation_sstrzalka.qmd` is a comprehensive Quarto document that implements a production-ready data preparation pipeline. The document transforms raw Home Credit data into model-ready features while ensuring **train/test consistency** to prevent data leakage. Unlike the exploratory phase, this preparation phase emphasizes reproducibility, consistency, and production-readiness.

### What It Does

The data preparation pipeline performs the following operations:

1. **Data Cleaning**
   - Handles DAYS_EMPLOYED anomaly (365243) by creating indicator variable
   - Creates missing data indicators for key predictive features
   - Imputes missing values using training-derived medians/modes

2. **Feature Engineering - Demographics**
   - Converts day-based features to years (age, employment duration)
   - Creates age and employment tenure bins
   - Calculates registration and ID document ages

3. **Feature Engineering - Financial Ratios**
   - Core affordability: credit-to-income, annuity-to-income
   - Loan-to-value ratio
   - Debt service coverage (income-to-annuity)
   - Per-capita metrics (credit/income per family member)
   - External score combinations (mean and weighted average)
   - Document submission count

4. **Feature Engineering - Interactions**
   - Age × income, age × credit-to-income
   - Employment × income
   - External score × credit ratio
   - Age squared (non-linear effects)

5. **Supplementary Data Aggregation**
   - **Bureau data:** Credit counts, debt levels, overdue amounts, debt ratios
   - **Previous applications:** Approval rates, credit amounts, application patterns
   - **Installment payments:** Late payment rates, payment trends, days past due

6. **Train/Test Consistency Management**
   - Stores imputation values, binning thresholds from training data
   - Applies identical transformations to test data
   - Ensures matching column sets between train and test

### How to Use

The Quarto document can be rendered to HTML to view the complete pipeline with results, or you can extract and use individual code chunks.

#### Render the Complete Document

```r
# In RStudio/Positron, open data_preparation_sstrzalka.qmd and click "Render"
# Or from command line:
quarto render data_preparation_sstrzalka.qmd
```

#### Use as Code Source

```r
# The document defines all functions and can be sourced
# Extract function definitions from the .qmd file or use interactively

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
  prep_params = train_result$params  # Use training params!
)

# Save prepared datasets
save_prepared_data(train_result, test_result, output_dir = "prepared_data")

# Access the prepared data
train_prepared <- train_result$data
test_prepared <- test_result$data
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
| `load_all_data()` | Convenience function to load all CSV files |
| `save_prepared_data()` | Saves prepared datasets and parameters |

### Output

The script produces:

- **train_prepared.csv**: Cleaned and engineered training dataset
- **test_prepared.csv**: Cleaned and engineered test dataset with identical transformations
- **prep_params.rds**: Stored parameters (imputation values, feature names) for reproducibility

### Important Notes

⚠️ **Data Leakage Prevention**
- All statistics (means, medians, modes) are computed ONLY from training data
- Test data uses stored training parameters
- Never fit on test data

⚠️ **Column Consistency**
- Script ensures train and test have identical columns (except TARGET)
- Missing columns in test are added with value 0
- Column order matches between datasets

## Dependencies

```r
library(tidyverse)   # Data manipulation and visualization
```

Install with: `install.packages("tidyverse")`

## Data Files

**Note:** Data files are NOT tracked in this repository due to size constraints. 

To run this project:
1. Download the [Home Credit Default Risk dataset from Kaggle](https://www.kaggle.com/c/home-credit-default-risk/data)
2. Place CSV files in the `Data/` directory
3. Run the data preparation script

## Next Steps

1. ✅ Exploratory Data Analysis (EDA)
2. ✅ Data Preparation and Feature Engineering
3. ⏳ Model Development (Upcoming)
4. ⏳ Model Evaluation and Validation
5. ⏳ Final Report and Recommendations

## Contact

**Sarah Strzalka**  
IS 6812 Capstone Project - Spring 2026

---

*Last Updated: February 7, 2026*
