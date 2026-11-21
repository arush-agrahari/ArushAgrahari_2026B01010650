# Telco Customer Churn Analysis & Retention Simulation

An end-to-end data cleaning, exploratory analysis, risk segmentation, and retention simulation pipeline built on the Telco Customer Churn dataset (`7,043` customer records).

---

## Dataset & Tools

* **Data Source:** Telco Customer Churn dataset sourced from **Kaggle** (`WA_Fn-UseC_-Telco-Customer-Churn.csv`).
* **Tools & Environment:** JupyterLab, Microsoft Excel, Git & GitHub.
* **Python Libraries:** `pandas`, `numpy`, `matplotlib`, `os`[cite: 1, 2].

---

## Project Overview

This repository processes raw telecom customer data to identify high-attrition segments, quantify the relationship between monthly billing and churn, categorize customers into actionable risk tiers, and simulate the financial impact of targeted contract-conversion interventions.

### Key Findings

* **Overall Churn Rate:** **26.54%** (`1,869` out of `7,043` customers).
* **Highest-Risk Segment:** Customers on **Month-to-month contracts with Fiber optic internet** exhibit a **54.61%** churn rate (`2,128` customers, average monthly charge of **$87.02**, median tenure of `16.0` months).
* **Billing Impact:** Churned customers pay a median of **$79.65/month** (IQR: `$38.05`) compared to **$64.43/month** (IQR: `$63.30`) for retained customers. Bootstrap resampling (`5,000` iterations) shows a mean monthly charge difference of **+$13.18** (95% CI: `[$11.75, $14.58]`).
* **Risk Tier Concentration:** The **High Risk** tier (`Month-to-month`, `tenure <= 12` months, `MonthlyCharges > $70`) comprises `856` customers with a **69.04%** churn rate.
* **Retention Simulation:** Converting **15%** of High Risk customers to 1-year contracts (assuming a 50% relative reduction in churn probability) retains approximately **44 customers** and preserves **$44,192.84** in annual recurring revenue.

---

## Repository Structure

```text
├── WA_Fn-UseC_-Telco-Customer-Churn.csv   # Raw Kaggle dataset (7,043 rows, 21 columns)
├── telco_churn_clean.csv                   # Cleaned & feature-engineered dataset
├── cleaning_and_validation.ipynb           # Data audit, cleaning, and feature engineering
└── analysis.ipynb                          # Statistical analysis, segmentation, and chart generation
```[cite: 1, 2]

---

## Pipeline Breakdown

### 1. Data Cleaning & Validation (`cleaning_and_validation.ipynb`)
* **Grain & Key Verification:** Confirmed `1 row = 1 unique customer` (`7,043` unique `customerID`s, zero duplicates).
* **Type Coercion & Imputation:** Converted whitespace strings in `TotalCharges` to numeric values and imputed `11` missing entries (all belonging to customers with `tenure == 0`) with `0.0`.
* **Standardization:** Encoded `Churn` into a binary `Churn_Flag` (`1`/`0`) and mapped `SeniorCitizen` from `0`/`1` to `"No"`/`"Yes"`.
* **Engineered Features:**
  * `Active_Services_Count`: Total count of active add-on/core services (`0` to `8`).
  * `Tenure_Band`: Categorized tenure into `0-6m`, `7-12m`, `13-24m`, `25-48m`, and `49-72m`.
  * `Expected_Total` & `Billing_Delta`: Calculated cumulative expected charges (`tenure * MonthlyCharges`) and variance against `TotalCharges`.

### 2. Exploratory Analysis & Risk Segmentation (`analysis.ipynb`)
* **Segment Aggregation:** Multi-dimensional grouping across `Contract`, `InternetService`, `Tenure_Band`, and `PaymentMethod`.
* **Non-Parametric & Bootstrap Inference:** Computed medians, interquartile ranges (IQR), and a 5,000-sample bootstrap confidence interval (`seed=42`) for monthly charge differences.
* **Vectorized Risk Tiers (`np.select`):**
  * **High Risk:** Month-to-month, tenure $\le 12$ months, monthly charges $> \$70$ (`856` customers | `69.04%` churn)
  * **Elevated Risk:** Month-to-month, tenure $\le 24$ months (`1,875` customers | `37.92%` churn)
  * **Moderate Risk:** One-year contract (`1,473` customers | `11.27%` churn)
  * **Low Risk:** All remaining customers, primarily two-year contracts (`2,839` customers | `14.12%` churn)[cite: 1]

---

## Setup & Execution

1. **Install dependencies:**
   ```bash
   pip install numpy pandas matplotlib jupyterlab
