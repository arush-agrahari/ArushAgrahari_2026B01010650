# System Requirements Specification (SRS)
**Project Title:** Telco Customer Churn Analysis & Retention Simulation  
**Submitted By:** Arush Agrahari (2026B01010650)  
**Repository:** `ArushAgrahari_2026B01010650`  

---


## 1. Introduction & Objective
### 1.1 Purpose
This document specifies the functional, data, hardware, and software requirements for the **Telco Customer Churn Analysis & Retention Simulation** project. The system processes raw telecommunications customer data to diagnose churn drivers, segment customers by attrition risk, and simulate financial retention outcomes.

### 1.2 Scope
The pipeline operates on the Kaggle Telco Customer Churn dataset (`7,043` customer records across `21` attributes) and produces a validated clean dataset, statistical summaries, risk tier classifications, and five analytical visualizations[cite: 1, 2].

---

## 2. Dataset Specifications
* **Source:** Kaggle (`WA_Fn-UseC_-Telco-Customer-Churn.csv`)
* **Grain:** 1 row = 1 unique customer (`customerID` primary key, `7,043` records)[cite: 2]
* **Output Dataset:** `telco_churn_clean.csv` (`7,043` rows, `25` columns after standardization and feature engineering)

---

## 3. Functional Requirements
### FR-1: Data Auditing & Cleaning (`cleaning_and_validation.ipynb`)
* **FR-1.1 Key Uniqueness Audit:** Verify zero duplicate `customerID` records exist in the raw file.
* **FR-1.2 Type Coercion & Imputation:** Strip whitespace and convert `TotalCharges` to floating-point numeric format; impute `11` missing values (all verified as `tenure == 0`) with `0.0`.
* **FR-1.3 Field Standardization:** Map `Churn` (`Yes`/`No`) to binary `Churn_Flag` (`1`/`0`) and decode `SeniorCitizen` (`0`/`1`) to (`No`/`Yes`).
* **FR-1.4 Feature Engineering:** Compute `Active_Services_Count` (`0–8`), `Tenure_Band` (`0-6m`, `7-12m`, `13-24m`, `25-48m`, `49-72m`), `Expected_Total` (`tenure * MonthlyCharges`), and `Billing_Delta` (`TotalCharges - Expected_Total`)[cite: 2].

### FR-2: Statistical Analysis & Segmentation (`analysis.ipynb`)
* **FR-2.1 Baseline & Segment Metrics:** Calculate overall churn rate (`26.54%`) and aggregate churn rates, customer counts, average monthly charges, and median tenure across `Contract`, `InternetService`, `Tenure_Band`, and `PaymentMethod`.
* **FR-2.2 Non-Parametric & Bootstrap Inference:** Compute median and IQR for `MonthlyCharges` by churn status and run a `5,000`-iteration bootstrap resampling (`seed=42`) to estimate the 95% confidence interval (`[$11.75, $14.58]`) for mean billing difference.
* **FR-2.3 Risk Band Classification:** Segment customers via vectorized conditional rules (`np.select`) into **High Risk** (`69.04%` churn), **Elevated Risk** (`37.92%` churn), **Moderate Risk** (`11.27%` churn), and **Low Risk** (`14.12%` churn)[cite: 1].

### FR-3: Retention Simulation & Reporting (`analysis.ipynb`)
* **FR-3.1 Financial Intervention Model:** Quantify customer and annual recurring revenue retention (`~44` customers; `$44,192.84/yr`) from converting `15%` of High Risk customers to 1-year contracts (assuming a `50%` relative churn reduction).
* **FR-3.2 Automated Visualization Export:** Generate and save five PNG charts (`120 DPI`) into a local `figures/` directory[cite: 1].

---

## 4. Non-Functional Requirements
* **Reproducibility:** Random number generation uses a fixed seed (`np.random.default_rng(42)`) so bootstrap confidence intervals remain deterministic across runs[cite: 1].
* **Data Integrity:** Raw input data (`WA_Fn-UseC_-Telco-Customer-Churn.csv`) remains unmodified; all transformations write to `telco_churn_clean.csv`[cite: 2].
* **Performance:** Full execution of both notebooks completes in under 10 seconds on standard consumer hardware.

---

## 5. System & Environment Requirements
### 5.1 Hardware Requirements
* **Processor:** Intel Core i5 / AMD Ryzen 5 (64-bit) or higher
* **Memory (RAM):** Minimum 4 GB (8 GB+ recommended)
* **Storage:** Minimum 500 MB free space for Python environment, CSV datasets, and generated charts

### 5.2 Software Requirements
* **Operating System:** Windows 10/11, macOS, or Linux
* **Runtime:** Python 3.10+ (developed and verified on Python 3.12)
* **Development & Inspection Tools:** JupyterLab, Microsoft Excel, Git & GitHub
* **Python Dependencies:** `pandas`, `numpy`, `matplotlib`, `os` (standard library)[cite: 1, 2]
