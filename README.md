# Power BI Alt Text UDF Library

<p align="left">
  <img alt="Status: stable" src="https://img.shields.io/badge/status-stable-brightgreen" />
  <img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue" />
  <img alt="Version: v1.0.0" src="https://img.shields.io/badge/version-v1.0.0-orange" />
  <img alt="Power BI: DAX UDFs" src="https://img.shields.io/badge/powerbi-DAX%20UDFs-purple" />
</p>

Status: stable License: MIT Version: v1.0.0 Power BI: DAX UDFs

A reusable library of Power BI DAX User‑Defined Functions (UDFs) designed to generate accessible Alt Text for visuals.
The goal is to support screen‑reader users and improve WCAG compliance across Power BI reports.

This library contains several core UDFs:

- **AltText_Context** – builds a consistent context summary
- **AltText_ChangeNarrative** – generates a narrative KPI explanation with optional comparison to a previous period
- **AltText_LineChart** – generates descriptive alt text for line charts showing Revenue and Cost trends
- **AltText_VisualPatterns** – a family of six functions covering common KPI visual patterns (progress, target vs actual, trend, rating, status, variance)

All functions are fully generic, meaning they work with any model, any dataset, and any business domain.

## Included Functions

### 1) AltText_Context

Creates a standardised context string for alt text using your own label/value pairs (e.g., Category, Department, Segment, Region, Division, etc.) and a date range.

**Example output:** Context: Category = Bikes, Department = Online. Period between 01 Jan 2024 and 31 Mar 2024.

### 2) AltText_ChangeNarrative

Generates an accessible narrative describing:

- The current metric value
- How it compares to a previous period
- Whether it increased, decreased, or remained flat
- Optional contextual text from AltText_Context

**Example output:** Total Sales is £125K. It has increased by 8.4% versus the previous period. Context: Category = Bikes, Department = Online.

### 3) AltText_LineChart

Generates descriptive, accessible alt text for line charts showing Revenue and Cost trends. Includes:

- Month over month trend detection
- Current vs previous month comparisons
- YTD revenue and cost summary
- Automatic wording for strong/slight/stable trends
- Fully formatted narrative for screen readers

File: `/src/AltText_LineCharts.dax`

```
AltText_LineChart_Final =
AltText_LineChart(
    /* Text parameters */
    [Trend Period Text],     -- e.g., "Jan–Dec 2024"
    [Measures Description],  -- e.g., "Revenue, Cost"
    [Category Description],  -- optional text or BLANK()
    [Filters Description],   -- e.g., region, segment
    [Units Text],            -- e.g., "GBP"

    /* MoM % numeric inputs */
    [Revenue MoM % Change],
    [Cost MoM % Change],

    /* Current and previous month numeric inputs */
    [Revenue Current Month],
    [Revenue Previous Month],
    [Cost Current Month],
    [Cost Previous Month],

    /* YTD numeric inputs */
    [Revenue YTD],
    [Cost YTD]
)
```

### 4) AltText_VisualPatterns

A family of six generic alt text builders for common KPI visual patterns. Each takes a `context` string (describing what the visual represents) plus the underlying data values, and returns a plain-language narrative — no chart type names, no colors.

File: `/src/examples/AltText_VisualPatterns.dax`
Demo: `/src/examples/AltText_VisualPatterns_Demo.dax`

- **ProgressBarAltText(context, percentComplete)** — describes completion progress
  Example output: `"Q3 project completion - underway at 72%."`

- **BulletChartAltText(context, actual, target, comparative)** — describes an actual value against a target and a comparative benchmark
  Example output: `"Monthly sales vs target - 180 falls short of the target of 250 (72% of target), compared to a benchmark of 280."`

- **SparkBarsAltText(context, firstValue, lastValue, minValue, maxValue, pointCount)** — describes a trend across a series of points
  Example output: `"12-month revenue trend - an upward trend from 3 to 9 over 8 periods (ranging from 1 to 9)."`

- **RatingDotsAltText(context, rating, maxRating)** — describes a rating out of a maximum
  Example output: `"Customer satisfaction rating - 4.0 out of 5."`

- **StatusPillAltText(context, status)** — describes a status label
  Example output: `"Project status - On track."`

- **VarianceChipAltText(context, variance)** — describes a signed percentage variance
  Example output: `"Revenue variance vs prior period - an increase of 14.9%."`

## How to Use These Functions

### A) Import into Power BI

1. Open Power BI Desktop.
2. Go to Modeling → New Measure.
3. Copy/paste each UDF from `/src` into a new DAX measure.
4. Name the functions exactly:
   - AltText_Context
   - AltText_ChangeNarrative
   - AltText_LineChart

Power BI will treat each definition as a reusable pseudo‑function.

### B) Call the UDFs in a visual's Alt Text

Context example:

```
AltText_Context(
    "Category", SELECTEDVALUE(Category[Category]),
    "Department", SELECTEDVALUE(Department[Department]),
    MIN(Date[Date]),
    MAX(Date[Date])
)
```

```
AltText_ChangeNarrative(
    "Total Sales",
    [Sales Amount],
    [Sales Amount Previous Period],
    FORMAT([Sales Amount], "£#,0.0"),
    [AltText_Context Measure]
)
```

```
AltText_LineChart_Final =
AltText_LineChart(
    /* Text parameters */
    [Trend Period Text],     -- e.g., "Jan–Dec 2024"
    [Measures Description],  -- e.g., "Revenue, Cost"
    [Category Description],  -- optional text or BLANK()
    [Filters Description],   -- e.g., region, segment
    [Units Text],            -- e.g., "GBP"

    /* MoM % numeric inputs */
    [Revenue MoM % Change],
    [Cost MoM % Change],

    /* Current and previous month numeric inputs */
    [Revenue Current Month],
    [Revenue Previous Month],
    [Cost Current Month],
    [Cost Previous Month],

    /* YTD numeric inputs */
    [Revenue YTD],
    [Cost YTD]
)
```

### C) Creating AltText_VisualPatterns (true DAX UDFs)

Unlike AltText_Context/AltText_ChangeNarrative/AltText_LineChart, the functions in `AltText_VisualPatterns.dax` use DAX's native `FUNCTION` syntax and must be added through one of:

- **DAX Query View** in Power BI Desktop — paste the `DEFINE ... FUNCTION ...` block, run it, then click "Update model"; or
- **TMDL View** — paste each `function Name = (...) => ...` definition and Apply.

They cannot be created via Modeling → New Measure, since New Measure doesn't accept the `FUNCTION` keyword. Requires model compatibility level 1702 or higher.

### D) Call the AltText_VisualPatterns functions

Each function pairs a context measure with the values that make up the visual, for example:

```
Progress Bar Alt Text = ProgressBarAltText([Progress Bar Context], [Progress %])

Bullet Chart Alt Text = BulletChartAltText([Bullet Chart Context], [Bullet Actual], [Bullet Target], [Bullet Comparative])

Status Pill Alt Text = StatusPillAltText([Status Pill Context], [Status Text])

Variance Chip Alt Text = VarianceChipAltText([Variance Chip Context], [Variance %])
```

See `AltText_VisualPatterns_Demo.dax` for the full set of placeholder measures and calls acros
