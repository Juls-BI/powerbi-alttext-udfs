# Power BI Alt Text UDF Library

<p align="left">
  <img alt="Status: stable" src="https://img.shields.io/badge/status-stable-brightgreen" />
  <img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue" />
  <img alt="Version: v1.0.0" src="https://img.shields.io/badge/version-v1.0.0-orange" />
  <img alt="Power BI: DAX UDFs" src="https://img.shields.io/badge/powerbi-DAX%20UDFs-purple" />
</p>

A reusable library of **Power BI DAX User‑Defined Functions (UDFs)** designed to generate **accessible Alt Text** for visuals.  
The goal is to support screen‑reader users and improve WCAG compliance across Power BI reports.

This library contains two core UDFs:

- **AltText_Context** – builds a consistent context summary  
- **AltText_ChangeNarrative** – generates a narrative KPI explanation with optional comparison to a previous period

Both functions are fully **generic**, meaning they work with **any model**, **any dataset**, and **any business domain**.

---

##  Included Functions

### 1) AltText_Context

Creates a standardised context string for alt text using your own label/value pairs (e.g., Category, Department, Segment, Region, Division, etc.) and a date range.

**Example output: Context: Category = Bikes, Department = Online.
Period between 01 Jan 2024 and 31 Mar 2024.

### 2) AltText_ChangeNarrative

Generates an accessible narrative describing:
- The current metric value
- How it compares to a previous period
- Whether it increased, decreased, or remained flat
- Optional contextual text from `AltText_Context`

**Example output: Total Sales is £125K. It has increased by 8.4% versus the previous period.
Context: Category = Bikes, Department = Online.

### 3) ) AltText_LineChart
Generates descriptive, accessible alt text for line charts showing Revenue and Cost trends. Includes:
•	Month over month trend detection
•	Current vs previous month comparisons
•	YTD revenue and cost summary
•	Automatic wording for strong/slight/stable trends
•	Fully formatted narrative for screen readers
File: /src/AltText_LineCharts.dax
Required Measures
These measures must exist in your model (names can be changed, but then you must update the UDF):
•	MoM % Measures (numeric)
o	MoM % Revenue (Global)
o	MoM % Cost (Global)
•	Reporting Month Measures (date)
o	Latest Reporting Month
o	Previous Reporting Month
•	Current Month Values (numeric)
o	Revenue Current (Global)
o	Cost Current (Global)
•	Previous Month Values (numeric)
o	Revenue Previous (Global)
o	Cost Previous (Global)
•	YTD Values (numeric)
o	Revenue YTD (Global)
o	Cost YTD (Global)
•	Text Inputs (text)
o	Trend Period
o	Region Filters
If your model uses different measure names, update the UDF accordingly.


##  How to Use These Functions

### A) Import into Power BI
1. Open **Power BI Desktop**.  
2. Go to **Modeling → New Measure**.  
3. Copy/paste each UDF from `/src` into a new DAX measure.  
4. Name the functions exactly:
   - `AltText_Context`
   - `AltText_ChangeNarrative`

> Power BI will treat each definition as a reusable pseudo‑function.

### B) Call the UDFs in a visual’s Alt Text

**Context example**
```DAX
AltText_Context(
    "Category", SELECTEDVALUE(Category[Category]),
    "Department", SELECTEDVALUE(Department[Department]),
    MIN(Date[Date]),
    MAX(Date[Date])
)

AltText_ChangeNarrative(
    "Total Sales",
    [Sales Amount],
    [Sales Amount Previous Period],
    FORMAT([Sales Amount], "£#,0.0"),
    [AltText_Context Measure]
)


AltText_LineChart(
    [Trend Period],          -- TrendPeriod
    "Revenue and Cost",      -- MeasuresText
    BLANK(),                 -- CategoryText (or [Category List])
    [Region Filters],        -- FiltersText
    "GBP"                    -- UnitsText
)
