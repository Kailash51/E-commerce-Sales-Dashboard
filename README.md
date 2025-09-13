# 📊 E-Commerce Sales Dashboard

[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=Power%20BI&logoColor=black)](https://powerbi.microsoft.com/)
[![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)](https://www.microsoft.com/en-us/sql-server)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

> An interactive E-Commerce Sales Dashboard built with Power BI featuring comprehensive analytics, time intelligence, and dynamic visualizations.

## 📌 Overview

This project is an interactive E-Commerce Sales Dashboard built in Power BI that provides comprehensive insights into sales performance, trends, and regional analysis. The dashboard includes:

- **KPI Banner** - YTD Sales, Profit, Quantity, Margin with YoY% comparisons
- **Trend Sparklines** - Monthly sales trends visualization
- **Interactive Maps** - Region/State analysis with bubble charts
- **Product Analytics** - Top 5 and Bottom 5 product performance
- **Shipping Analysis** - Ship mode distribution insights
- **Dynamic Slicers** - Segment, Category, Region, State filtering
- **Conditional Formatting** - Growth indicators (green) and decline alerts (red)

## 🧩 Key Features

### Time Intelligence
- **TOTALYTD** calculations for year-to-date metrics
- **SAMEPERIODLASTYEAR** for period-over-period comparisons
- **YoY%** growth calculations with proper handling of edge cases

### Data Modeling
- **Calendar Table** with proper month sorting (January → December)
- **Relationship Modeling** - Orders ↔ US States (lat/lon) ↔ Calendar
- **Flexible Data Sources** - Support for both SQL Server and direct CSV imports

### Advanced Analytics
- **Dynamic Rankings** - Top/Bottom product analysis
- **Geographic Visualization** - Interactive state-level mapping
- **Performance Metrics** - Comprehensive KPI tracking

## 🛠️ Technology Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| **Power BI Desktop** | 2.116.9660+ | Dashboard development |
| **SQL Server** | 19.0.2+ 
| **Excel** | Office 2021+ 



## 📥 Data Sources

### Orders Dataset
- **Records:** ~113,000 rows
- **Fields:** Customer info, Category, Product details, Geographic data, Order/Ship dates, Financial metrics, Delivery status
- **Key Metrics:** Sales, Profit, Quantity, Discount percentages


## 📅 Calendar & Data Modeling

### Create Calendar Table

```dax
Calendar = 
CALENDAR(
    MIN(Orders[Order Date]),
    MAX(Orders[Order Date])
)
```

### Add Helper Columns

| Column | DAX Formula |
|--------|-------------|
| **Year** | `YEAR(Calendar[Date])` |
| **MonthName** | `FORMAT(Calendar[Date], "MMMM")` |
| **MonthNumber** | `MONTH(Calendar[Date])` |

### Configure Sorting
1. Select `Calendar[MonthName]`
2. **Modeling** → **Sort by column** → `Calendar[MonthNumber]`

### Establish Relationships
- `Calendar[Date]` → `Orders[Order Date]` (1 → *)
- `USStates[Name]` → `Orders[Customer State]` (1 → *)

## 🧮 DAX Measures

### Core Time Intelligence Measures

```dax
-- Year-to-Date Sales
YTD Sales = 
TOTALYTD(SUM(Orders[Sales]), Calendar[Date])

-- Previous Year-to-Date Sales
PYTD Sales = 
CALCULATE([YTD Sales], SAMEPERIODLASTYEAR(Calendar[Date]))

-- Year-over-Year Percentage
YoY % = 
VAR py = [PYTD Sales]
RETURN IF(py = 0, BLANK(), DIVIDE([YTD Sales] - py, py))
```

### Additional KPI Measures

```dax
-- Profit and Quantity Metrics
YTD Profit = TOTALYTD(SUM(Orders[Profit]), Calendar[Date])
YTD Quantity = TOTALYTD(SUM(Orders[Quantity]), Calendar[Date])

-- Margin Calculation
YTD Margin % = 
VAR s = [YTD Sales]
RETURN IF(s = 0, BLANK(), DIVIDE([YTD Profit], s))
```

### Ranking Measures

```dax
-- Top Products Ranking
Sales Rank Desc = 
RANKX(ALL(Orders[Product Name]), [YTD Sales], , DESC, DENSE)

-- Bottom Products Ranking
Sales Rank Asc = 
RANKX(ALL(Orders[Product Name]), [YTD Sales], , ASC, DENSE)
```

### Formatting Guidelines
- **Currency:** USD format for Sales/Profit measures
- **Percentage:** % format for YoY% and Margin% measures

## 📊 Visual Components

### KPI Banner (Card Visuals)
- **Values:** `[YTD Sales]`, `[YTD Profit]`, `[YTD Quantity]`, `[YTD Margin %]`
- **Styling:** Font size 24-28, bold, white text
- **Background:** Semi-transparent rounded rectangles

### Trend Analysis
- **Sparklines:** Area/Line charts
- **X-Axis:** `Calendar[MonthName]` (sorted)
- **Y-Axis:** `SUM(Orders[Sales])`
- **Filter:** Current year only

### Geographic Visualization
- **Map Type:** Bubble chart
- **Location:** `USStates[Name]` or lat/lon coordinates
- **Size:** `[YTD Sales]`
- **Legend:** Region categorization

### Product Analysis
- **Top 5 Products:** Filter `Sales Rank Desc <= 5`
- **Bottom 5 Products:** Filter `Sales Rank Asc <= 5`

### Additional Visuals
- **Sales by Category:** Table/Matrix with YTD, PYTD, YoY% columns
- **Region Analysis:** Clustered bar chart
- **Ship Mode Distribution:** Donut/Pie chart
- **Interactive Slicers:** Segment, Category, Region, State

## ✅ Conditional Formatting

### Color Coding Rules
- **Growth (Positive YoY):** Green (`#1E8E3E`)
- **Decline (Negative YoY):** Red (`#D93025`)

### Icon Indicators
- **Positive Growth:** Up arrow (↑)
- **Negative Growth:** Down arrow (↓)

### Implementation
- Use **Data bars** or **Background color** rules
- Apply to table visualizations for enhanced readability

## 🎨 Design & Styling

### Canvas Design
- **Background:** `assets/background.png` (0-10% transparency)
- **Title Bar:** Rounded rectangle (`#223248`, 50% opacity, white text)
- **Cards/Tiles:** Subtle shadows, 6-8px border radius
- **Layout:** Consistent spacing grid system

### Visual Hierarchy
- **Primary Metrics:** Large, prominent display
- **Secondary Data:** Supporting visualizations
- **Interactive Elements:** Clear, accessible slicers

## 🎥 Video Demonstration

Watch the complete dashboard walkthrough to see all features in action:

```markdown
[![Watch Demo Video](https://vimeo.com/1102705139)]
```
## ⚙️ Performance Optimization

### Data Management
- ✅ **Correct Data Types:** Date/Text/Whole/Decimal
- ✅ **Month Sorting:** Always use MonthNumber for proper ordering
- ✅ **Measures Over Columns:** Prefer measures for better performance
- ✅ **Visual Optimization:** Limit fields, remove unnecessary columns
- ✅ **Relationship Direction:** Single-direction unless bidirectional needed

### Best Practices
- Use **measures** instead of calculated columns for aggregations
- Keep **visual count** reasonable for optimal performance
- Implement **proper data modeling** for efficient queries

