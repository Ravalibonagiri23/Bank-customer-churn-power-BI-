# Bank Customer Churn Analysis – Power BI

## 1. Project Overview

This project analyzes **bank customer churn** using a public Bank Customer Churn dataset and visualizes the results in **Power BI**.  

The goal is to identify which customer segments are most likely to leave the bank (churn) and to build a simple **star schema** model with an interactive dashboard.

---

## 2. Dataset

- Source: Bank Customer Churn dataset (UK bank) from Kaggle.  
- Each row represents **one customer**.  
- Key columns used:
  - CreditScore
  - Geography
  - Age
  - Tenure (years with the bank)
  - Balance
  - NumOfProducts
  - HasCrCard
  - IsActiveMember
  - EstimatedSalary
  - Exited (1 = churned customer, 0 = retained customer)

> Note: I have uploaded the original dataset file in the "data" folder.

---

## 3. Data Cleaning & Transformation

Performed in **Power Query** and **DAX**:

- Removed columns that are not useful for analysis:
  - 'RowNumber', 'Surname', 'CustomerId
- Fixed data types:
  - 'Converted Age', 'Tenure', 'CreditScore', 'Balance', 'NumOfProducts', 'EstimatedSalary' to numeric types.
  - Converted 'Exited' to **Whole Number** (0/1) for DAX comparisons.
- Created derived columns:
  - **AgeGroupName** (Young, Adult, Senior) – based on 'Age'
  - **TenureGroupName** (0–3 years, 4–7 years, 8–10 years) – based on 'Tenure'

Example (Age group DAX):
DAX

AgeGroupName =
VAR a = 'BankChurn'[Age]
RETURN
IF(
    a >= 18 && a <= 29, "Young",
    IF(
        a >= 30 && a <= 45, "Adult",
        "Senior"
    )
)


Example (Tenure group DAX):

TenureGroupName =
VAR t = 'BankChurn'[Tenure]
RETURN
IF(
    t <= 3, "0–3 years",
    IF(
        t <= 7, "4–7 years",
        "8–10 years"
    )
)


Data Model (Star Schema)
The model follows a star schema pattern:

Fact table
Fact: BankChurn

CreditScore

Geography

Age

AgeGroupName

Tenure

TenureGroupName

Balance

NumOfProducts

HasCrCard

IsActiveMember

EstimatedSalary

Exited

Dimension tables
DimAgeGroup

AgeGroupName (Young, Adult, Senior)

DimTenureGroup

TenureGroupName (0–3 years, 4–7 years, 8–10 years)

Relationships
BankChurn[AgeGroupName] → DimAgeGroup[AgeGroupName]

BankChurn[TenureGroupName] → DimTenureGroup[TenureGroupName]

(All relationships: many‑to‑one, single direction.)


Measures (DAX)
Main measures used in the dashboard:

Total Customers =
COUNTROWS('BankChurn')

Churned Customers =
CALCULATE(
    [Total Customers],
    'BankChurn'[Exited] = 1
)

Churn Rate =
DIVIDE(
    [Churned Customers],
    [Total Customers]
)

Avg Balance =
AVERAGE('BankChurn'[Balance])

Avg Balance (Churned) =
CALCULATE(
    [Avg Balance],
    'BankChurn'[Exited] = 1
)

Retained Customers =
CALCULATE(
    [Total Customers],
    'BankChurn'[Exited] = 0
)


 Power BI Dashboard
The main report page includes:

KPI Cards

Total Customers

Churned Customers (number of customers with Exited = 1)

Churn Rate (percentage of customers who churned)

Charts

Churn Rate by Age Group

Visual: Clustered column chart

Axis: DimAgeGroup[AgeGroupName]

Value: Churn Rate

Churn Rate by Tenure Group

Visual: Clustered column chart

Axis: DimTenureGroup[TenureGroupName]

Value: Churn Rate

Churn Rate by Geography

Visual: Clustered bar chart

Axis: BankChurn[Geography]

Value: Churn Rate

Detail Table

Table visual showing:

Geography

AgeGroupName

TenureGroupName

Balance

Exited (displayed as a churn indicator, e.g., 1 = churned, 0 = retained)

Slicers

DimAgeGroup[AgeGroupName]

DimTenureGroup[TenureGroupName]

BankChurn[Geography]


Key Insights:



Age: Churn rate differs across age bands, with certain age groups showing noticeably higher churn than others.

Tenure: Customers with shorter tenure (0–3 years) tend to churn more than long‑tenure customers (4–7 or 8–10 years), indicating early‑life cycle risk.

Geography: Some countries have higher churn rates than others, suggesting region‑specific retention strategies may be needed.

Balance & products: Customers balance levels and the number of products they hold are linked with churn behaviour, indicating opportunities for cross‑selling and targeted engagement.
