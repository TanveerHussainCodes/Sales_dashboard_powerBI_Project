# 🏆 Enterprise Retail Analytics System | Power BI End-to-End Project

## 📌 Project Overview

This project is a complete **Enterprise Retail Analytics System** developed in **Power BI** using a large-scale synthetic retail dataset.

The project was designed to simulate a **real-world enterprise BI environment** and covers the complete Business Intelligence lifecycle:

- Data Generation
- ETL & Data Cleaning
- Data Modeling
- DAX & Time Intelligence
- Business KPIs
- Interactive Dashboards
- Inventory & Operations Analytics
- Drillthrough & Navigation
- Row Level Security (RLS)
- Performance Optimization
- Executive Storytelling

The goal was not just to create dashboards, but to build a **fully connected analytics ecosystem** using enterprise-level best practices.

---

# 🎯 Business Objectives

The project answers key business questions such as:

- Which products contribute most to revenue?
- Which stores perform best or worst?
- Are sales improving year-over-year?
- Which products are at low-stock risk?
- Which categories drive profitability?
- How efficient is inventory turnover?
- Which products/customers follow the 80/20 rule?
- Which regions require operational attention?

---

# 🏗️ Tech Stack

| Tool | Purpose |
|------|----------|
| Power BI Desktop | Data Modeling & Visualization |
| Power Query | ETL & Data Cleaning |
| DAX | Business Logic & Analytics |
| CSV Files | Data Source |
| Star Schema | Data Modeling Architecture |

---

# 📂 Dataset Architecture

The project uses a large-scale retail dataset containing:

## 📊 Fact Tables

- `FactSales`
- `FactInventory`
- `FactMarketingSpend`

## 📐 Dimension Tables

- `DimDate`
- `DimCustomers`
- `DimProducts`
- `DimStores`
- `DimEmployees`
- `DimSuppliers`
- `DimCampaigns`

### 📌 Dataset Characteristics

- 750,000+ rows
- Multiple fact tables
- Multiple dimensions
- Multi-domain business simulation
- Enterprise-level data relationships

---

# 🔄 ETL & Data Cleaning (Power Query)

## 🧱 ETL Architecture

A professional **staging architecture** was implemented:

```text
Raw Data → Staging (stg_) → Final Model Tables → Dashboards
```

---

# 🧹 Data Cleaning Operations

## 📌 FactSales

### Cleaning Performed
- Removed invalid quantity values (`Quantity <= 0`)
- Validated sales calculations
- Corrected data types

### Business Reason
Invalid quantities produce incorrect KPIs and sales analytics.

---

## 📌 FactInventory

### Cleaning Performed
- Replaced negative stock values with `0`

### Business Reason
Negative stock is operationally invalid and impacts inventory analysis.

---

## 📌 DimCustomers

### Cleaning Performed
- Replaced null cities with `"Unknown"`

### Business Reason
Missing geography values break regional analysis and slicers.

---

# ⚙️ ETL Best Practices Applied

- Created `stg_` tables for cleaning logic
- Used `Reference` tables for final model
- Disabled load for staging queries
- Applied proper data types
- Separated ETL from reporting layer

---

# ⭐ Data Modeling (Star Schema)

A professional **Star Schema** was implemented.

---

# 🧠 Star Schema Structure

```text
                 DimDate
                    |
                    |
DimCustomers --- FactSales --- DimProducts --- DimSuppliers
                    |
                DimStores
                    |
              DimEmployees


                 DimDate
                    |
              FactInventory
                    |
               DimProducts
                    |
                 DimStores


                 DimDate
                    |
          FactMarketingSpend
                    |
               DimCampaigns
```

---

# 🔗 Relationship Rules Applied

| Rule | Applied |
|------|----------|
| Dimension → Fact | ✅ |
| Single Direction Filtering | ✅ |
| Many-to-One Relationships | ✅ |
| Shared Date Dimension | ✅ |
| No Fact-to-Fact Relationships | ✅ |
| No Bi-Directional Relationships | ✅ |

---

# 🧠 Why Star Schema Was Used

### Benefits

- Faster performance
- Cleaner DAX
- Better scalability
- Simpler filtering behavior
- Easier troubleshooting
- Enterprise-grade architecture

---

# 📅 Date Table Implementation

A dedicated `DimDate` table was created and marked as the official Date Table.

### Purpose

- Enable Time Intelligence
- Maintain continuous dates
- Support YTD / YoY calculations

---

# 🧮 Measures Architecture

A dedicated `_Measures` table was created to organize all DAX measures.

---

# 🧱 Base Measures

## Total Sales

```DAX
Total Sales =
SUM ( FactSales[NetSales] )
```

## Total Cost

```DAX
Total Cost =
SUM ( FactSales[CostAmount] )
```

## Total Quantity

```DAX
Total Quantity =
SUM ( FactSales[Quantity] )
```

## Total Orders

```DAX
Total Orders =
COUNTROWS ( FactSales )
```

---

# 📈 Business KPI Measures

## Profit

```DAX
Profit =
[Total Sales] - [Total Cost]
```

## Profit Margin %

```DAX
Profit Margin % =
DIVIDE ( [Profit], [Total Sales] )
```

## Average Order Value (AOV)

```DAX
AOV =
DIVIDE ( [Total Sales], [Total Orders] )
```

---

# 📅 Time Intelligence Measures

## Sales YTD

```DAX
Sales YTD =
TOTALYTD (
    [Total Sales],
    DimDate[Date]
)
```

### Purpose
Tracks cumulative yearly sales performance.

---

## Sales Last Year

```DAX
Sales LY =
CALCULATE (
    [Total Sales],
    SAMEPERIODLASTYEAR ( DimDate[Date] )
)
```

### Purpose
Compares current period against previous year.

---

## YoY Growth %

```DAX
YoY Growth % =
DIVIDE (
    [Total Sales] - [Sales LY],
    [Sales LY]
)
```

### Purpose
Measures business growth or decline.

---

# 📊 Advanced Analytics Measures

---

## Rolling 12 Months

```DAX
Sales Rolling 12M =
CALCULATE (
    [Total Sales],
    DATESINPERIOD (
        DimDate[Date],
        MAX ( DimDate[Date] ),
        -12,
        MONTH
    )
)
```

### Purpose
Tracks moving annual trend without yearly reset.

---

## Running Total

```DAX
Sales Running Total =
CALCULATE (
    [Total Sales],
    FILTER (
        ALL ( DimDate ),
        DimDate[Date] <= MAX ( DimDate[Date] )
    )
)
```

### Purpose
Tracks cumulative sales progression over time.

---

## Product Ranking

```DAX
Product Rank =
RANKX (
    ALL ( DimProducts[ProductName] ),
    [Total Sales],
    ,
    DESC,
    DENSE
)
```

### Purpose
Ranks products based on sales performance.

---

## Sales Contribution %

```DAX
Sales Contribution % =
DIVIDE (
    [Total Sales],
    CALCULATE ( [Total Sales], ALL ( DimProducts ) )
)
```

### Purpose
Measures percentage contribution of each product.

---

## Pareto / Cumulative Contribution

```DAX
Cumulative Sales Contribution % =
VAR CurrentRank = [Product Rank]
RETURN
DIVIDE (
    CALCULATE (
        [Total Sales],
        FILTER (
            ALL ( DimProducts[ProductName] ),
            [Product Rank] <= CurrentRank
        )
    ),
    CALCULATE ( [Total Sales], ALL ( DimProducts ) )
)
```

### Purpose
Implements 80/20 analysis.

---

# 🧠 Key DAX Concepts Applied

- Filter Context
- Row Context
- Context Transition
- CALCULATE
- ALL
- FILTER
- RANKX
- DIVIDE
- Time Intelligence Functions

---

# 📊 Dashboard Pages

---

# 🏠 Page 1 — Executive Overview Dashboard

## 🎯 Business Goal

Provide executives with a high-level overview of company performance.

---

## 📌 KPIs

- Total Sales
- Profit
- Profit Margin %
- YoY Growth %
- Sales YTD

---

## 📈 Visuals

- Sales Trend Line Chart
- Sales by Category
- Sales by City
- Customer Segment Analysis

---

# 📦 Page 2 — Product Performance Dashboard

## 🎯 Business Goal

Analyze product contribution, ranking, and Pareto distribution.

---

## 📌 Analytics

- Product Ranking
- Top Products
- Contribution %
- Pareto Analysis
- Product Trends

---

## 📈 Visuals

- Pareto Chart
- Product Performance Matrix
- Top Products Bar Chart

---

# 🏬 Page 3 — Store & Inventory Dashboard

## 🎯 Business Goal

Monitor store efficiency and inventory performance.

---

## 📌 KPIs

- Total Stock
- Stock Value
- Inventory Turnover

---

## 📈 Visuals

- Sales by Store
- Stock by Store
- Stock vs Sales Comparison
- Low Stock Risk Table

---

# 🔍 Drillthrough & Interactivity

Implemented interactive analytics features:

## ✅ Features

- Drillthrough Pages
- Tooltips
- Synced Slicers
- Dynamic Filtering
- Interactive Navigation

---

# 🔐 Row Level Security (RLS)

Implemented city-based role security.

## Example

```DAX
DimStores[City] = "Lahore"
```

### Business Use

- Lahore Manager → sees Lahore data only
- Karachi Manager → sees Karachi data only

---

# 🎨 UI / UX Design Principles

Applied professional dashboard design practices:

- Consistent theme
- Visual hierarchy
- KPI-first layout
- Minimal clutter
- Dynamic titles
- Clean spacing & alignment

---

# ⚡ Performance Optimization

## Optimization Techniques Applied

| Optimization | Applied |
|--------------|----------|
| Star Schema | ✅ |
| Measures instead of Calculated Columns | ✅ |
| Disabled Auto Date/Time | ✅ |
| Hidden Technical Columns | ✅ |
| Disabled Staging Table Load | ✅ |
| Optimized Relationships | ✅ |

---

# 📈 Business Insights Enabled

The solution enables stakeholders to:

- Monitor revenue trends
- Track profitability
- Analyze inventory efficiency
- Identify top-performing products
- Detect low-stock risk
- Evaluate operational performance
- Perform Pareto analysis
- Improve decision-making

---

# 🏆 Key Learnings

This project strengthened expertise in:

- Power Query ETL
- Enterprise Data Modeling
- Advanced DAX
- Time Intelligence
- Dashboard Storytelling
- Business Analytics
- Row Level Security
- Performance Optimization

---

# 📌 Project Highlights

✅ End-to-End BI Solution  
✅ 750k+ Rows Dataset  
✅ Enterprise Star Schema  
✅ Advanced DAX Measures  
✅ Time Intelligence Analytics  
✅ Pareto Analysis  
✅ Interactive Dashboards  
✅ Inventory & Operations Analytics  
✅ Drillthrough & Tooltips  
✅ Row Level Security  
✅ Performance Optimized  

---

# 📷 Dashboard Screenshots

## Executive Overview

```text
Add Screenshot Here
```

## Product Performance

```text
Add Screenshot Here
```

## Store & Inventory Dashboard

```text
Add Screenshot Here
```

---

# 🚀 Future Enhancements

- Power BI Service Deployment
- Scheduled Refresh
- SQL Database Integration
- Incremental Refresh
- Forecasting Models
- AI-Based Insights

---

# 👨‍💻 Author

## Tanveer Hussain Shah

Data Analyst | Power BI Developer | SQL & Python Enthusiast

---

# ⭐ If you found this project useful

Give this repository a ⭐ on GitHub.
