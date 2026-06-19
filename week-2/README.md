💳 Fraud Detection — Week 2: Modelling & Evaluation

Decode Labs | Week 2 Project

Overview
This notebook builds a fraud detection system on a financial transactions dataset. It covers the full machine learning pipeline — from data cleaning and feature engineering through to model comparison, SMOTE-based class balancing, hyperparameter tuning, and feature importance analysis.
The target variable is is_fraud: whether a transaction is fraudulent (binary: 0 = Legitimate, 1 = Fraud).

Dataset
Source: transactions.csv

A financial transactions dataset containing user behaviour, transaction metadata, and card verification signals used to identify fraudulent activity.
Key features include:
FeatureDescriptionuser_idUnique user identifiertransaction_idUnique transaction identifiertransaction_timeTimestamp of the transactionamountTransaction amountavg_amount_userUser's average transaction amountcountryCountry of transactionbin_countryCountry associated with the card BINaccount_age_daysAge of the account in daysshipping_distance_kmDistance between billing and shipping addressavs_matchAddress Verification System match resultcvv_resultCVV verification resultis_fraudTarget — whether the transaction is fraudulent

Notebook Breakdown
1. Data Understanding

Loaded and inspected the dataset (shape, dtypes, column names)
Checked for missing values — no missing values were found
Checked for duplicates — no duplicate rows were found

2. Feature Engineering
Five new features were derived to improve signal for fraud detection:
New FeatureLogicamount_ratioamount / avg_amount_user — flags unusually large transactions relative to user historycountry_match1 if transaction country matches card BIN country, else 0hourHour of day extracted from transaction_timenew_account1 if account_age_days < 30, else 0high_shipping_distance1 if shipping distance exceeds the 75th percentile, else 0
Identifier columns (user_id, transaction_id, transaction_time) were dropped before modelling.
3. Exploratory Data Analysis (EDA)

Correlation heatmap across all numerical features
Histograms for distribution of all numerical columns
Fraud rate analysis for high-signal features:

AVS match vs fraud rate
CVV result vs fraud rate
New account vs fraud rate
Shipping distance vs fraud (boxplot)



4. Modelling
Preprocessing Pipeline

Numerical columns: StandardScaler
Categorical columns: OneHotEncoder (with handle_unknown='ignore')
Built using ColumnTransformer for clean, leak-free preprocessing

Base Models vs SMOTE Models
Both base models (no class balancing) and SMOTE variants were tested across four algorithms to measure the effect of oversampling on fraud detection performance:

Logistic Regression
Linear SVM
Random Forest
XGBoost

Key observations:

Logistic Regression and Linear SVM performed poorly in both the base and SMOTE versions — SMOTE did not meaningfully improve their ability to detect fraud
XGBoost delivered the best overall performance and was selected as the model to take forward for hyperparameter tuning

Evaluation Metrics
Models compared across:
MetricWhy it matters for fraudPrecisionHow many flagged transactions are actually fraudRecallHow many actual frauds were caughtF1 ScoreBalance between precision and recallROC-AUCOverall discrimination ability
Results sorted by ROC-AUC to identify the best performer.
5. Hyperparameter Tuning

XGBoost (base and SMOTE variants) selected as best model
Tuned using RandomizedSearchCV with 3-fold cross-validation
Parameters searched: n_estimators, max_depth, learning_rate, subsample
Best estimators evaluated against the test set

6. Feature Importances

Extracted feature importances from the tuned XGBoost model
Feature names retrieved from the preprocessing pipeline using get_feature_names_out()
Ranked by importance to identify the strongest fraud signals


Output Files
FileDescriptionfraud_correlation.pngCorrelation heatmap of numerical featuresavs_match_vs_fraud.pngFraud rate by AVS match resultcvv_result_vs_fraud.pngFraud rate by CVV resultnew_account_vs_fraud.pngFraud rate: new vs old accountsshipping_distance_vs_fraud.pngShipping distance distribution by fraud label
