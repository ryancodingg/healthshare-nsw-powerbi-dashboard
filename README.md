# Supplier Spend Analysis Report – HealthShare NSW

**Prepared by:** Ryan Nguyen  
**Version:** v1.0  
**Submission:** NSW Health Performance Analyst Assessment  
**Date:** 27 July 2025  

---

## 📌 Purpose of the Report

This Power BI report was developed to help HealthShare NSW analyze supplier spending across multiple dimensions, including currency, supplier category, item classification, geography, and time. The report supports performance monitoring, spend optimization, and risk reduction by revealing trends, discount utilization, and key cost drivers.

---

## 🗂️ Report Contents & Navigation

| Page | Title                        | Purpose                                                                 |
|------|------------------------------|-------------------------------------------------------------------------|
| 1    | **Overview Dashboard**       | High-level KPIs (Spend, Refunds, Invoices) with filters for date, location, and supplier |
| 2    | **Spend by Supplier & Item** | Insights into top suppliers and spend patterns by item category/subcategory |
| 3    | **Spend by Currency & Location** | FX impact, exchange rate behavior, and supplier distribution by region |
| 4    | **Discounts & Payment Terms** | Identify unused discounts, assess payment term impact, and find saving opportunities |
| 5    | **Trend Analysis**           | Visualize seasonal and weekly spend trends using matrix heatmaps and line charts |
| 6    | **Tooltips & Drillthrough**  | Interactive details via hover and drillthrough for suppliers and categories |

---

## 🧱 Data Model – Star Schema

The model follows a clean **star schema** centered on the `InvoiceLineItem` fact table.

### 🧾 Fact Table:
- **InvoiceLineItem**: Transaction-level data including spend, refunds, item and supplier info, and currency

### 🧩 Dimension Tables:
- **Invoice**: Invoice ID, purchasing location, discounts
- **Item**: Item ID, category, subcategory
- **Location**: City, state, country/region
- **DateTable**: Calendar table with Year, Month, Weekday
- **Currency**: Currency abbreviation and ID
- **ExchangeRate**: FX rates by date and currency (referenced via DAX only)

> 💡 Note: `ExchangeRate` is **not physically related** to the model. It's accessed in DAX through `TOPN` and `FILTER` to retrieve the most recent rate on or before `PaidDate`.

---

## 🧮 Key Calculated Measures

| Measure                  | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **Gross Amount (AUD)**   | Total spend before applying refunds or discounts                           |
| **Net Amount (AUD)**     | Spend after applying `RefundPercent`                                        |
| **YTD Amount**           | Year-to-date spend via `DATESYTD(DateTable[Date])`                          |
| **AvgMonthlySpend**      | Net spend divided by number of distinct months                              |
| **AvgSpendPerSupplier**  | Gross spend divided by distinct supplier count                              |
| **Effective Exchange Rate** | Weighted average FX rate applied to NZD transactions                    |

---

## 📌 Assumptions

- **Currency Conversion**: NZD converted to AUD using the most recent FX rate ≤ `PaidDate`; AUD uses 1.0.
- **Refund Calculation**: Derived from `DiscountPercent` and applied per line item.
- **Data Exclusion**:
  - Transactions from 2012 (3 rows) removed due to outlier behavior.
  - Null or unmatched FX rates excluded from calculations.
- **YTD Calculation**: Defaults to the current year if no date filter is applied.

---

## 🎨 Visual Design Choices

- **Color Scheme**: Blue for Direct, Purple for Indirect, Orange for Other
- **Typography**: Uniform font size and weight for headers and KPIs
- **Theme**: Dark blue background aligning with NSW Health brand
- **Watermark/Footer**:  
  `"Report prepared by Ryan Nguyen | v1.0 | NSW Health Performance Analyst Assessment"`

---

## 🧭 Usage Instructions

- Use **date slicers** on each page to adjust the time period
- Click on **supplier/category names** in visuals to filter other charts
- Hover on **maps/matrix visuals** for additional tooltip insights
- Drillthrough enabled for **Supplier Name** and **Item Category**

---

## 🚀 Future Enhancements

- Add **forecasting trends** using exponential smoothing
- Develop a **supplier scoring model** based on reliability, discounts, and delivery
- Enable **anomaly detection alerts** for budget breaches and irregular spend

---

## 📅 Additional Note

A custom `DateTable` was created using DAX to support time intelligence. It includes:
- `Date`, `Year`, `Month`, `Month Number`, `Weekday`  
This enables dynamic filtering, aggregation, and visualizations throughout the report.

---
