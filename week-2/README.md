# 💳 Fraud Detection — Week 2: Modelling & Evaluation


## Overview

This notebook builds a **fraud detection system** on a financial transactions dataset. It covers the full machine learning pipeline, from data cleaning and feature engineering through to model comparison, SMOTE-based class balancing, hyperparameter tuning, and feature importance analysis.

The target variable is **`is_fraud`**: whether a transaction is fraudulent (binary: 0 = Legitimate, 1 = Fraud).

---

## Dataset

**Source:**
Dataset Source: E-Commerce Fraud Detection Dataset — Kaggle

To run this notebook, download transactions.csv from https://www.kaggle.com/datasets/umuttuygurr/e-commerce-fraud-detection-dataset and place it in the week-2/ folder before running.
It is a financial transactions dataset containing user behaviour, transaction metadata, and card verification signals used to identify fraudulent activity.

**Key features include:**

| Feature | Description |
|---|---|
| `user_id` | Unique user identifier |
| `transaction_id` | Unique transaction identifier |
| `transaction_time` | Timestamp of the transaction |
| `amount` | Transaction amount |
| `avg_amount_user` | User's average transaction amount |
| `country` | Country of transaction |
| `bin_country` | Country associated with the card BIN |
| `account_age_days` | Age of the account in days |
| `shipping_distance_km` | Distance between billing and shipping address |
| `avs_match` | Address Verification System match result |
| `cvv_result` | CVV verification result |
| `is_fraud` | **Target** — whether the transaction is fraudulent |

---

## Notebook Breakdown

### 1. Data Understanding
- Loaded and inspected the dataset (shape, dtypes, column names)
- Checked for missing values — **no missing values were found**
- Checked for duplicates — **no duplicate rows were found**

### 2. Feature Engineering
Five new features were derived to improve signal for fraud detection:

| New Feature | Logic |
|---|---|
| `amount_ratio` | `amount / avg_amount_user` — flags unusually large transactions relative to user history |
| `country_match` | `1` if transaction country matches card BIN country, else `0` |
| `hour` | Hour of day extracted from `transaction_time` |
| `new_account` | `1` if `account_age_days < 30`, else `0` |
| `high_shipping_distance` | `1` if shipping distance exceeds the 75th percentile, else `0` |

Identifier columns (`user_id`, `transaction_id`, `transaction_time`) were dropped before modelling.

### 3. Exploratory Data Analysis (EDA)
- **Correlation heatmap** across all numerical features
- **Histograms** for distribution of all numerical columns
- **Fraud rate analysis** for high-signal features:
  - AVS match vs fraud rate
  - CVV result vs fraud rate
  - New account vs fraud rate
  - Shipping distance vs fraud (boxplot)

### 4. Modelling

#### Preprocessing Pipeline
- **Numerical columns:** `StandardScaler`
- **Categorical columns:** `OneHotEncoder` (with `handle_unknown='ignore'`)
- Built using `ColumnTransformer` for clean, leak-free preprocessing

#### Base Models vs SMOTE Models
Both base models (no class balancing) and SMOTE variants were tested across four algorithms to measure the effect of oversampling on fraud detection performance:

- Logistic Regression
- Linear SVM
- Random Forest
- XGBoost

**Key observations:**
- **Logistic Regression and Linear SVM** performed poorly in both the base and SMOTE versions — SMOTE did not meaningfully improve their ability to detect fraud
- **XGBoost** delivered the best overall performance and was selected as the model to take forward for hyperparameter tuning

#### Evaluation Metrics
Models compared across:

| Metric | Why it matters for fraud |
|---|---|
| **Precision** | How many flagged transactions are actually fraud |
| **Recall** | How many actual frauds were caught |
| **F1 Score** | Balance between precision and recall |
| **ROC-AUC** | Overall discrimination ability |

Results sorted by ROC-AUC to identify the best performer.

### 5. Hyperparameter Tuning
- **XGBoost** (base and SMOTE variants) selected as best model
- Tuned using `RandomizedSearchCV` with 3-fold cross-validation
- Parameters searched: `n_estimators`, `max_depth`, `learning_rate`, `subsample`
- Best estimators evaluated against the test set

### 6. Feature Importances
- Extracted feature importances from the tuned XGBoost model
- Feature names retrieved from the preprocessing pipeline using `get_feature_names_out()`
- Ranked by importance to identify the strongest fraud signals

---

## Output Files

| File | Description |
|---|---|
| `fraud_correlation.png` | Correlation heatmap of numerical features |
| `avs_match_vs_fraud.png` | Fraud rate by AVS match result |
| `cvv_result_vs_fraud.png` | Fraud rate by CVV result |
| `new_account_vs_fraud.png` | Fraud rate: new vs old accounts |
| `shipping_distance_vs_fraud.png` | Shipping distance distribution by fraud label |

---
