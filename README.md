# Power BI Alt Text UDF Library

<p align="left">
  https://img.shields.io/badge/status-stable-brightgreen
  https://img.shields.io/badge/license-MIT-blue
  https://img.shields.io/badge/version-v1.0.0-orange
  https://img.shields.io/badge/powerbi-DAX%20UDFs-purple
</p>

A reusable library of **Power BI DAX User‑Defined Functions (UDFs)** designed to generate **accessible Alt Text** for visuals.  
The goal is to support screen‑reader users and improve WCAG compliance across Power BI reports.

This library contains two core UDFs:

- **AltText_Context** – builds a consistent context summary  
- **AltText_ChangeNarrative** – generates a narrative KPI explanation with optional comparison to a previous period

Both functions are fully **generic**, meaning they work with **any model**, **any dataset**, and **any business domain**.

---

##  Included Functions

### 1. AltText_Context

Creates a standardised context string for alt text using your own label/value pairs (e.g., Category, Department, Segment, Region, Division, etc.) and a date range.

**Example output:**

