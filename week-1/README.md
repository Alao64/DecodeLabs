# 🫀 Framingham Heart Study — Week 1: Data Preparation & EDA

> **Decode Labs** | Week 1 Project

## Overview

This notebook walks through the end-to-end preparation and exploratory data analysis (EDA) of the **Framingham Heart Study** dataset — a longitudinal cardiovascular study used to identify risk factors for coronary heart disease (CHD).

The target variable is **`TenYearCHD`**: whether a patient develops coronary heart disease within ten years (binary: 0 = No, 1 = Yes).

---

## Dataset

**Source:** `framingham.csv`  
The Framingham Heart Study dataset contains patient demographic, behavioural, and clinical measurements collected to study cardiovascular risk factors.

**Key features include:**

| Feature | Description |
|---|---|
| `age` | Patient age |
| `male` | Sex (1 = male, 0 = female) |
| `education` | Education level (categorical) |
| `cigsPerDay` | Cigarettes smoked per day |
| `BPMeds` | On blood pressure medication |
| `prevalentStroke` | History of stroke |
| `prevalentHyp` | History of hypertension |
| `diabetes` | Diabetic status |
| `totChol` | Total cholesterol |
| `sysBP` | Systolic blood pressure |
| `diaBP` | Diastolic blood pressure |
| `BMI` | Body Mass Index |
| `heartRate` | Resting heart rate |
| `glucose` | Blood glucose level |
| `TenYearCHD` | **Target** — 10-year CHD risk |

---

## Notebook Breakdown

### 1. Data Loading & Type Casting
- Loaded the dataset with `pandas`
- Converted binary/ordinal columns (`education`, `currentSmoker`, `BPMeds`, `prevalentStroke`, `prevalentHyp`, `diabetes`, `TenYearCHD`) to the `category` dtype for memory efficiency and correct downstream handling

### 2. Missing Value Handling
- Identified nulls across all columns
- Imputed **numerical columns** with the **median** (robust to skew)
- Imputed **categorical columns** with the **mode** (most frequent value)

### 3. Feature Engineering
Four new columns were derived to make patterns more interpretable:

| New Feature | Logic |
|---|---|
| `sex` | Recoded `male` → `'male'` / `'female'` |
| `age_cat` | `< 18` → `Child`, else `Adult` |
| `smoking_category` | `0` → Non-Smoker, `1–10` → Light, `11–20` → Moderate, `21+` → Heavy |
| `bp_category` | Classified into `Hypotensive`, `Normal`, or `Hypertensive` using systolic/diastolic thresholds |
| `glucose_category` | Classified into `Hypoglycemia`, `Normal`, `Prediabetes`, or `Diabetes` |

Redundant source columns (`currentSmoker`, `male`) were dropped after encoding.

### 4. Exploratory Data Analysis (EDA)
- **Numerical distributions:** Histograms + boxplots for `totChol`, `sysBP`, `diaBP`, `BMI`, `heartRate`, `glucose`
- **Correlation heatmap:** Identified relationships between numerical features
- **Categorical distributions:** Count plots with percentage labels for `education`, `BPMeds`, `prevalentHyp`, `prevalentStroke`, `diabetes`, `TenYearCHD`
- **Feature vs Target:** Count plots of engineered features (`sex`, `age_cat`, `smoking_category`, `bp_category`, `glucose_category`) split by `TenYearCHD`

### 5. Outlier Handling
- Applied **5th–95th percentile clipping** to `heartRate`, `totChol`, and `glucose`
- Visualised before/after distributions to confirm clean data

---

## Output Files

| File | Description |
|---|---|
| `outlier_check.png` | Pre-cleaning distributions and boxplots |
| `clean.png` | Post-cleaning distributions for clipped columns |
| `{col}_countplot.png` | Categorical distribution plots |
| `{col}_vs_TenYearCHD.png` | Feature vs target breakdown plots |

---

## Requirements

```
pandas
numpy
matplotlib
seaborn
```





**Decode Labs**  
Building week by week. 🚀
