**README – Supplier Spend Analysis Report**

---

Prepared by: Ryan Nguyen

Version: v1.0

Submission: NSW Health Performance Analyst Assessment

Date: 27 July 2025



##### **Purpose of the Report**

This report was developed to help NSW Health analyze supplier spending across various dimensions such as currency, supplier category, item classification, geography, and time. The report supports performance monitoring, spend optimization, and risk reduction through transparency of trends, discounts, and cost drivers.





##### **Report Contents \& Navigation**

Page | Title                             | Purpose

-----|-----------------------------------|-----------------------------------------------------------

1    | Overview Dashboard                | High-level KPIs (Spend, Refunds, Invoices) with filters for date, location, and supplier

2    | Spend by Supplier \& Item          | Insights into top suppliers and spend patterns by item category and subcategory

3    | Spend by Currency \& Location      | FX impact, exchange rate behavior, and supplier distribution by region

4    | Discounts \& Payment Terms         | Identify unused discounts, payment terms impact, and opportunities for savings

5    | Trend Analysis                    | Spot seasonal and weekly spend behavior with interactive trend breakdowns

6    | Tooltips               		 | 



##### **Data Model: Star Schema**

Your model follows a clean star schema centered on InvoiceLineItem (Fact Table), with surrounding dimension tables:



**Fact Table**

\- InvoiceLineItem: Contains transaction-level details including spend, refunds, item ID, supplier info, and currency



**Dimension Tables**

\- Invoice: Invoice ID, purchasing location, discounts

\- Item: Item ID, category, sub-category

\- Location: City, country/region, state

\- DateTable: Full calendar with Year, Month, Weekday

\- Currency: Currency abbreviation and ID

\- ExchangeRate: Historical FX rates by date and currency (linked indirectly via calculated column)



Note: ExchangeRate table is not directly related to the model — it's referenced via DAX inside a calculated column (ExchangeRateAUD) that uses TOPN and FILTER to get the most recent FX rate ≤ PaidDate.



**Key Calculated Measures**

Measure                  | Description

------------------------|----------------------------------------------------

Gross Amount (AUD)      | Total spend before discount or refund

Net Amount (AUD)        | Spend after applying RefundPercent

YTD Amount              | Net spend calculated via DATESYTD(DateTable\[Date])

AvgMonthlySpend         | Divides net spend by unique months in selection

AvgSpendPerSupplier     | Gross spend divided by distinct InvoiceID values

Effective Exchange Rate | Average FX rate applied to NZD transactions



**Assumptions**

\- Currency Conversion: NZD to AUD based on most recent rate on or before PaidDate; AUD transactions use rate of 1.

\- Discount Logic: Calculated from DiscountPercent and used to derive Refunds at the line item level.

\- Data Exclusion:

&nbsp; - Transactions from 2012 (3 total rows) were excluded due to irrelevance and outlier behavior.

&nbsp; - Exchange rate rows with null or unmatched dates were omitted in calculations.

\- YTD Calculation: Uses the filtered date context; defaults to the current year if no slicer applied.



**Visual Design Choices**

\- Color Consistency: Blue for Direct, Purple for Indirect, Orange for Other, etc.

\- Font Consistency: Uniform font size and weight for headers and KPI cards

\- Theming: Dark blue background applied for contrast and NSW identity

\- Watermark/Footer:

&nbsp; "Report prepared by Ryan Nguyen | v1.0 | NSW Health Performance Analyst Assessment"



**Usage Instructions**

\- Use date slicers at the top of each page to adjust reporting periods.

\- Click on supplier/category names in visual legends or tables to filter linked visuals.

\- Hover on maps and matrix visuals to reveal tooltips.

\- Drill-through enabled on supplier name and item category for detail investigation.



**Future Enhancements** 

\- Add a forecasting trend line using exponential smoothing

\- Score suppliers on reliability, delivery, and discount usage

\- Add alert cards for anomalies (e.g., over-budget months)



**Additional Note:**

A new table called `DateTable` was created using DAX to support time intelligence functions. This table includes fields such as Date, Year, Month, Month Number, and Weekday, enabling dynamic filtering, aggregation, and temporal trend analysis across all report pages.
