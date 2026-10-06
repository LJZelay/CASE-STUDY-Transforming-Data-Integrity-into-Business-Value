# IBM HR Analytics: Employee Attrition Model Evaluation & Fairness Audit

## Overview

This project evaluates and audits an algorithmic data system (ADS) designed to predict employee attrition using the IBM HR Analytics Employee Attrition dataset.

Rather than evaluating the models solely on overall predictive accuracy, this project examines the system from a broader **data quality, model performance, fairness, interpretability, and operational risk** perspective.

The analysis compares **Logistic Regression** and **Random Forest** models and investigates whether their predictions are reliable and equitable across demographic and organizational groups.

> **Key finding:** Overall model performance can mask substantial subgroup failures. The analysis identified significant disparities in recall across age groups and departments, raising concerns about fairness, reliability, and responsible deployment.

---

## Project Objectives

The audit evaluates the attrition prediction system across five areas:

* **Data Quality** — Assess dataset composition, feature imbalance, correlations, and potential representation issues.
* **Model Performance** — Evaluate precision, recall, F1-score, and confusion matrices.
* **Fairness** — Measure disparities across age, gender, department, and marital status.
* **Interpretability & Explainability** — Examine feature importance, model behavior, and SHAP-based feature interactions.
* **Deployment Risk** — Assess prediction consistency, probability calibration, and whether the system is appropriate for HR decision-making.

---

## Dataset

The analysis uses the **IBM HR Analytics Employee Attrition dataset**, containing:

* **1,470 employee records**
* Demographic attributes including Age, Gender, and Marital Status
* Employment attributes including Department, Job Role, and Job Level
* Compensation attributes including Monthly Income and Hourly Rate
* Work-environment attributes including OverTime, Work-Life Balance, and Job Satisfaction
* Binary attrition outcome: `Yes` / `No`

The dataset contains no detected missing values, but several variables exhibit substantial imbalance.

The attrition target is also imbalanced, with approximately **16% of employees labeled as having left the organization**.

---

## Methodology

### 1. Data Preparation

Categorical variables were label encoded, including:

* BusinessTravel
* Department
* EducationField
* Gender
* JobRole
* MaritalStatus
* Over18
* OverTime

The dataset was divided into:

* **70% training set:** 1,029 employees
* **30% test set:** 441 employees
* **Random seed:** 42

Standard scaling was applied to the feature set.

---

### 2. Model Development

Two classification approaches were evaluated.

#### Logistic Regression

A Logistic Regression model was used as a relatively interpretable baseline.

Configuration included:

* `lbfgs` solver
* L2 regularization
* `C = 1.0`
* Maximum of 100 iterations
* No class weighting

Its coefficients provide a direct indication of feature direction and magnitude.

#### Random Forest

A Random Forest model was evaluated to capture nonlinear relationships and feature interactions.

Configuration included:

* 100 decision trees
* `max_depth = None`
* Default parameters otherwise

While Random Forest can model more complex relationships, its decision process is less directly interpretable and requires additional explainability techniques.

---

## Evaluation Framework

### Predictive Performance

The models were evaluated using:

* Precision
* Recall
* F1-score
* Confusion matrices

Recall received particular attention because **false negatives represent employees who may be at risk of leaving but are not identified by the system**.

### Model Results

For the attrition class:

| Metric    | Logistic Regression | Random Forest |
| --------- | ------------------: | ------------: |
| Precision |                 62% |           50% |
| Recall    |                 25% |           10% |
| F1-score  |                 36% |           17% |

The results indicate that both models had difficulty identifying actual leavers, with Random Forest producing particularly low recall.

---

## Fairness Audit

The audit evaluates disparities across demographic and organizational groups using multiple fairness measures.

### Equal Opportunity Difference

A threshold greater than **0.1** was treated as an indicator of potential discrimination.

| Group      | Logistic Regression | Random Forest |
| ---------- | ------------------: | ------------: |
| Age Group  |               0.316 |         0.211 |
| Department |               0.348 |         0.457 |
| Gender     |               0.062 |         0.071 |

The largest disparities were observed across **age groups and departments**.

### Statistical Parity Difference

Selection-rate disparities were also evaluated.

The analysis identified moderate-risk disparities involving:

* Age group
* Marital status

False-positive-rate disparities were also examined across age and gender.

---

## Model Consistency & Explainability

The audit extends beyond standard classification metrics to evaluate how consistently the models behave.

### Cross-Model Analysis

The two models demonstrated:

* **68% prediction agreement**
* **32% conflicting risk assessments**

OverTime was ranked as the most important feature by both models:

* Logistic Regression: **10.7%**
* Random Forest: **13.6%**

### SHAP Analysis

SHAP-based analysis was used to investigate feature interactions and nonlinear behavior.

The analysis identified patterns involving:

* Age and Job Level
* Gender and Department
* Marital Status and OverTime

These interactions provide additional evidence that protected or potentially sensitive attributes may influence model behavior indirectly.

---

## Robustness & Calibration

Prediction consistency was evaluated under feature perturbation using 5% noise.

| Model               | Prediction Consistency |
| ------------------- | ---------------------: |
| Logistic Regression |                    92% |
| Random Forest       |                    78% |

Logistic Regression demonstrated greater prediction stability under the tested perturbation.

Probability calibration was also evaluated:

| Model               | Mean Calibration Error |
| ------------------- | ---------------------: |
| Logistic Regression |                   0.08 |
| Random Forest       |                   0.15 |

The results suggest that Random Forest produced less reliable probability estimates for interpreting attrition risk.

---

## Audit Findings

### Performance Risk

Overall accuracy does not adequately represent the system's usefulness for attrition prediction.

The low recall for the attrition class means that a substantial number of employees who ultimately leave may not be identified as high-risk.

### Fairness Risk

Significant subgroup disparities were identified, particularly across:

* Age
* Department
* Gender
* Marital status

Age and department demonstrated the most substantial disparities in the analysis.

### Interpretability Risk

Logistic Regression provides more transparent feature-level reasoning, while Random Forest provides greater modeling flexibility at the cost of interpretability.

### Operational Risk

The models produced conflicting risk assessments for approximately **32% of test-set predictions**, creating potential uncertainty for HR decision-makers.

---

## Audit Recommendation

Based on the evaluated performance, fairness, and robustness measures, the current system **should not be deployed for direct HR decision-making without further mitigation and auditing**.

The analysis recommends:

1. Evaluating the removal of direct protected attributes such as Age, Gender, and Marital Status.
2. Recognizing that removing protected attributes alone does not eliminate proxy discrimination.
3. Incorporating fairness-aware modeling techniques during training.
4. Continuing SHAP-based monitoring to identify potentially problematic feature behavior.
5. Monitoring representation and performance across demographic and organizational groups.
6. Conducting continuous auditing before and after deployment.

The primary concern is that a system with acceptable overall performance can still produce **systematic subgroup failures** that create ethical, operational, and legal risks in employment-related decisions.

---

## Technical Stack

**Languages & Tools**

* Python
* scikit-learn
* Pandas
* NumPy
* SHAP
* Matplotlib / visualization tooling

**Modeling**

* Logistic Regression
* Random Forest

**Evaluation**

* Precision
* Recall
* F1-score
* Confusion Matrix
* Equal Opportunity Difference
* Statistical Parity Difference
* False Positive Rate Disparity
* Feature Importance
* SHAP Analysis
* Prediction Consistency
* Probability Calibration

---

## Audit Perspective

This project demonstrates an approach to evaluating machine-learning systems as **decision-making systems rather than simply predictive models**.

A model can achieve strong aggregate performance while still producing unacceptable outcomes for specific groups.

For high-impact applications such as HR, model evaluation should therefore consider:

**Data Quality → Model Performance → Fairness → Explainability → Robustness → Deployment Risk**

This framework helps identify risks that conventional accuracy metrics alone may overlook.

---

## Limitations

The underlying dataset is derived from historical HR records and does not provide a documented sampling methodology. This creates limitations regarding representativeness.

The analysis also identifies correlations and disparities within the available data but does not establish causal relationships.

The fairness thresholds used in this audit provide a screening framework for identifying potential disparities; they should not be interpreted as definitive legal determinations.

---

## Project Takeaway

The central finding of this audit is that **predictive performance and responsible deployment are not the same thing**.

For an HR attrition system, identifying who may leave is only one part of the problem. The system must also demonstrate that its predictions are sufficiently accurate, stable, interpretable, and equitable across the populations affected by its decisions.

**The audit therefore recommends further bias mitigation, validation, and continuous monitoring before deployment.**

