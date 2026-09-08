# 📊 Enterprise Sales Data Transformation & Analytics Platform

An end-to-end sales analytics project that transforms raw Superstore sales data into clean, validated, analysis-ready data using Python, Pandas, MySQL, SQL, Power BI, and Generative AI.

---

## 🔄 1. Project Workflow

```text
                  Raw Superstore Data
                          │
                          ▼
                ┌──────────────────┐
                │  Python + Pandas │
                └────────┬─────────┘
                         │
                         ▼
                 Data Quality Checks
                         │
                         ▼
                   Data Cleaning
                         │
                         ▼
                Data Transformation
                         │
                         ▼
             cleaned_superstore.csv
                         │
                         ▼
                ┌──────────────────┐
                │      MySQL       │
                │  SQL Analysis    │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │     Power BI     │
                │    Dashboard     │
                └────────┬─────────┘
                         │
                         ▼
                Generative AI
               Business Insights
```

---

## 🛠️ 2. Technologies Used

| Technology | Purpose |
|------------|---------|
| 🐍 Python | Data processing and analysis |
| 🐼 Pandas | Data cleaning and transformation |
| 🔢 NumPy | Numerical calculations |
| 🗄️ MySQL | Database storage and SQL analysis |
| 📊 Power BI | Interactive business dashboard |
| 🤖 Generative AI | Business insights and recommendations |

---

## 📁 3. Dataset

The project uses the **Superstore sales dataset**.

### Dataset Information

- **Total Records:** 9,994
- **Input File:** `superstore.csv`
- **Processed File:** `cleaned_superstore.csv`

The raw dataset is loaded into Pandas and inspected before cleaning and transformation.

---

## 🐍 4. Data Loading & Understanding

The raw dataset is loaded using Pandas.

```python
import pandas as pd
import numpy as np

df = pd.read_csv("superstore.csv", encoding="cp1252")

print("Dataset Shape:", df.shape)
print(df.columns.tolist())
display(df.head())
```

The dataset is inspected to understand:

- Dataset shape
- Column names
- Data types
- Sample records
- Overall structure

---

## 🔍 5. Data Quality Checks

Multiple data-quality checks are performed before processing the data.

### Missing Values

```python
df.isnull().sum()
```

### Duplicate Records

```python
df.duplicated().sum()
```

### Statistical Summary

```python
df.describe()
```

### Sales, Profit & Quantity Ranges

```python
print("Sales Range:")
print("Minimum:", df["Sales"].min())
print("Maximum:", df["Sales"].max())

print("Profit Range:")
print("Minimum:", df["Profit"].min())
print("Maximum:", df["Profit"].max())

print("Quantity Range:")
print("Minimum:", df["Quantity"].min())
print("Maximum:", df["Quantity"].max())
```

These checks help identify missing values, duplicates, invalid dates, and unusual numerical values.

---

## 🧹 6. Data Cleaning

The dataset is cleaned before performing business analysis.

### Date Conversion

```python
df["Order Date"] = pd.to_datetime(
    df["Order Date"],
    errors="coerce"
)

df["Ship Date"] = pd.to_datetime(
    df["Ship Date"],
    errors="coerce"
)
```

### Duplicate Removal

```python
df = df.drop_duplicates()
```

Invalid dates are also identified using:

```python
print("Invalid Order Dates:",
      df["Order Date"].isnull().sum())

print("Invalid Ship Dates:",
      df["Ship Date"].isnull().sum())
```

---

## ⚙️ 7. Data Transformation

Additional analytical features are created from the existing dataset.

### Year

```python
df["Year"] = df["Order Date"].dt.year
```

### Month

```python
df["Month"] = df["Order Date"].dt.month
```

### Month Name

```python
df["Month Name"] = df["Order Date"].dt.month_name()
```

### Profit Margin

```python
df["Profit Margin"] = np.where(
    df["Sales"] != 0,
    (df["Profit"] / df["Sales"]) * 100,
    0
)
```

### Shipping Days

```python
df["Shipping Days"] = (
    df["Ship Date"] - df["Order Date"]
).dt.days
```

These derived features make the dataset more useful for SQL analysis and Power BI visualization.

---

## 📋 8. Final Data Quality Report & Export

A final quality report is generated after cleaning and transformation.

```python
quality_report = {
    "Total Records": len(df),
    "Total Columns": len(df.columns),
    "Missing Values": df.isnull().sum().sum(),
    "Duplicate Records": df.duplicated().sum(),
    "Invalid Order Dates": df["Order Date"].isnull().sum(),
    "Invalid Ship Dates": df["Ship Date"].isnull().sum()
}

print("========== FINAL DATA QUALITY REPORT ==========")

for key, value in quality_report.items():
    print(f"{key}: {value}")
```

The processed dataset is then exported:

```python
df.to_csv(
    "cleaned_superstore.csv",
    index=False
)

print("Cleaned dataset exported successfully!")
print("File: cleaned_superstore.csv")
```

The resulting `cleaned_superstore.csv` is used as the input for MySQL and Power BI.

---

# 🗄️ 9. MySQL Database

A dedicated database is created for the cleaned sales data.

```sql
CREATE DATABASE enterprise_sales;

USE enterprise_sales;
```

The processed CSV is imported into MySQL using the **Table Data Import Wizard**.

The cleaned dataset is then stored in a MySQL table for structured querying and business analysis.

---

# 🔎 10. MySQL Data Verification

The imported data is verified using:

```sql
USE enterprise_sales;

SELECT *
FROM sales
LIMIT 10;
```

The total number of records is checked using:

```sql
SELECT COUNT(*) AS total_records
FROM sales;
```

This confirms that the cleaned dataset has been successfully loaded into the database.

---

# 📈 11. SQL Business Analysis

SQL is used to analyze the sales data and answer important business questions.

## 💰 Total Sales

```sql
SELECT SUM(Sales) AS Total_Sales
FROM sales;
```

## 💵 Total Profit

```sql
SELECT SUM(Profit) AS Total_Profit
FROM sales;
```

## 🌎 Sales by Region

```sql
SELECT
    Region,
    SUM(Sales) AS Total_Sales
FROM sales
GROUP BY Region
ORDER BY Total_Sales DESC;
```

## 📊 Profit by Region

```sql
SELECT
    Region,
    SUM(Profit) AS Total_Profit
FROM sales
GROUP BY Region
ORDER BY Total_Profit DESC;
```

## 🏷️ Category Performance

```sql
SELECT
    Category,
    SUM(Sales) AS Revenue,
    SUM(Profit) AS Profit
FROM sales
GROUP BY Category
ORDER BY Profit DESC;
```

## 🏆 Top 10 Products

```sql
SELECT
    `Product Name`,
    SUM(Sales) AS Revenue
FROM sales
GROUP BY `Product Name`
ORDER BY Revenue DESC
LIMIT 10;
```

## 👥 Customer Segment Analysis

```sql
SELECT
    Segment,
    SUM(Sales) AS Revenue,
    SUM(Profit) AS Profit
FROM sales
GROUP BY Segment
ORDER BY Revenue DESC;
```

## 🚚 Shipping Performance

```sql
SELECT
    `Ship Mode`,
    COUNT(*) AS Orders,
    ROUND(AVG(`Shipping Days`), 2) AS Avg_Shipping_Days
FROM sales
GROUP BY `Ship Mode`
ORDER BY Avg_Shipping_Days;
```

## ⚠️ Worst-Performing Region

```sql
SELECT
    Region,
    SUM(Profit) AS Total_Profit
FROM sales
GROUP BY Region
ORDER BY Total_Profit ASC
LIMIT 1;
```

---

# 🔄 12. Data Reconciliation

A source-to-database reconciliation step is included to validate the consistency of the data between Python and MySQL.

### Python

```python
source_total_sales = df["Sales"].sum()

print("Python Total Sales:", source_total_sales)
```

### MySQL

```sql
SELECT
    SUM(Sales) AS MySQL_Total_Sales
FROM sales;
```

The two values are compared to validate the data pipeline.

---

# 📊 13. Power BI Dashboard

The processed `cleaned_superstore.csv` dataset is used to create an interactive Power BI dashboard.

### Power BI Data Flow

```text
Power BI Desktop
      ↓
Get Data
      ↓
Text/CSV
      ↓
cleaned_superstore.csv
      ↓
Create Measures
      ↓
Create Visualizations
      ↓
Interactive Dashboard
```

---

## 📌 KPI Cards

The dashboard contains four key performance indicators:

### 💰 Total Sales

```DAX
Total Sales =
SUM(sales[Sales])
```

### 💵 Total Profit

```DAX
Total Profit =
SUM(sales[Profit])
```

### 📦 Total Orders

```DAX
Total Orders =
DISTINCTCOUNT(sales[Order ID])
```

### 🧾 Average Order Value

```DAX
Average Order Value =
DIVIDE(
    [Total Sales],
    [Total Orders]
)
```

---

## 📈 Dashboard Visualizations

### 1. Sales by Region

- Axis → Region
- Values → Total Sales

### 2. Profit by Region

- Axis → Region
- Values → Total Profit

### 3. Sales by Category

- Axis → Category
- Values → Total Sales

### 4. Monthly Sales Trend

- X-axis → Order Date
- Y-axis → Total Sales
- Visualization → Line Chart

### 5. Sales by Customer Segment

- Axis → Segment
- Values → Total Sales

---

## 🖥️ Dashboard Layout

```text
┌────────────┬────────────┬────────────┬────────────┐
│ Total Sales│Total Profit│Total Orders│ Avg Order  │
└────────────┴────────────┴────────────┴────────────┘

┌──────────────────────┬─────────────────────────────┐
│   Sales by Region    │     Profit by Region        │
└──────────────────────┴─────────────────────────────┘

┌──────────────────────┬─────────────────────────────┐
│ Monthly Sales Trend  │      Sales by Category      │
└──────────────────────┴─────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│             Sales by Customer Segment               │
└─────────────────────────────────────────────────────┘
```

---

# 🤖 14. Generative AI Business Insights

Generative AI is used to convert analytical results into business-oriented insights and recommendations.

Example questions include:

- Which region is performing worst based on profitability?
- Which category has high sales but relatively low profit?
- What are the major sales trends in the dataset?
- What business recommendations can be made based on the sales and profitability analysis?

The actual SQL and Power BI results are provided to the AI to generate meaningful and data-driven recommendations.

---

# 🎯 15. Key Business Questions

The project focuses on answering questions such as:

| Business Question | Analysis |
|-------------------|----------|
| What are the total sales? | Overall revenue analysis |
| What is the total profit? | Overall profitability |
| Which region generates the most sales? | Regional sales analysis |
| Which region is least profitable? | Regional profitability |
| Which category performs best? | Category analysis |
| Which products generate the most revenue? | Top product analysis |
| Which customer segment contributes most? | Segment analysis |
| How does shipping mode affect delivery time? | Shipping analysis |
| How do sales change over time? | Monthly trend analysis |

---

# 🧠 16. Skills Demonstrated

```text
                    DATA ANALYTICS
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
       PYTHON           MYSQL          POWER BI
          │               │               │
      Pandas/NumPy      SQL Queries      DAX
          │               │               │
   Data Cleaning      Aggregation     KPI Cards
   Data Validation    GROUP BY        Visualizations
   Feature Creation   ORDER BY        Dashboard
          │               │               │
          └───────────────┼───────────────┘
                          ▼
                 BUSINESS INSIGHTS
                          │
                          ▼
                  GENERATIVE AI
```

### Core Skills

- Python
- Pandas
- NumPy
- Data Cleaning
- Data Validation
- Feature Engineering
- SQL
- MySQL
- Data Reconciliation
- Power BI
- DAX
- Business Analysis
- Generative AI

---

# 🚀 17. Project Highlights & Business Value

### Project Highlights

- Processed **9,994 sales records** using Python and Pandas.
- Performed data-quality checks for missing values and duplicate records.
- Converted and validated date fields.
- Created analytical features such as **Year, Month, Profit Margin, and Shipping Days**.
- Exported a cleaned and transformed CSV for downstream analysis.
- Built a structured MySQL database for sales analysis.
- Performed SQL-based analysis across regions, categories, products, customer segments, and shipping modes.
- Designed an interactive Power BI dashboard for sales and profitability analysis.
- Applied Generative AI to convert analytical results into business insights and recommendations.
- Included source-to-database reconciliation to validate data consistency.

### Business Value

The project demonstrates an end-to-end approach to converting raw business data into actionable information:

```text
Raw Data
   ↓
Data Cleaning
   ↓
Data Validation
   ↓
Data Transformation
   ↓
SQL Analysis
   ↓
Power BI Visualization
   ↓
Generative AI Insights
   ↓
Data-Driven Business Decisions
```

---

# 📂 Project Structure

```text
Enterprise-Sales-Analytics/
│
├── cleaned_superstore.ipynb
├── cleaned_superstore.csv
├── README.md
│
└── powerbi/
    └── sales_dashboard.pbix
```

---

# 📌 Project Status

| Component | Status |
|-----------|--------|
| Data Loading | ✅ Completed |
| Data Quality Checks | ✅ Completed |
| Data Cleaning | ✅ Completed |
| Data Transformation | ✅ Completed |
| CSV Export | ✅ Completed |
| MySQL Database | ✅ Completed |
| SQL Analysis | ✅ Completed |
| Data Reconciliation | 🔄 Under Validation |
| Power BI Dashboard | 🔄 In Progress |
| Generative AI Insights | 🔄 In Progress |

---

## 👩‍💻 Author

**Deeksha**

**Thapar Institute of Engineering and Technology | ECE**

---
