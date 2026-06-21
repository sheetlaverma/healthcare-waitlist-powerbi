# 🏥 Healthcare Patient Wait List Dashboard

> Power BI dashboard analyzing 25M+ patient wait list records across Inpatient, Outpatient & Day Case categories from 2018 to 2021.

---

## 📋 Problem Statement

| Objective | Details |
|-----------|---------|
| **Current Status** | Track the current status of patient waiting list |
| **Historical Trend** | Analyze monthly trend in Inpatient & Outpatient categories |
| **Granular Analysis** | Detailed specialty level & age profile breakdown |
| **Data Scope** | January 2018 – March 2021 |

---

## 📊 Dashboard Pages

### Page 1 — Summary
- Total Wait List Comparison (Latest Month vs Previous Year Same Month)
- Wait List Bifurcation by Case Type (Donut Chart)
- Key Indicators – toggleable Average & Median metrics
- Time Band vs Age Profile Analysis (Clustered Column Chart)
- Monthly Trend Analysis – Day Case/Inpatient vs Outpatient (Line Chart)
- Top 5 Specialties by Wait List (Multi-Row Card)

### Page 2 — Detailed View
- Interactive Matrix with drill-down by Archive Date, Age Profile & Time Bands
- Filters: Archive Date, Case Type, Speciality, Age Profile, Time Bands

### Page 3 — Specialty Breakdown (Tooltip)
- Total Wait List: 25 Million+
- Specialty Group Breakup (Horizontal Bar Chart)
- Top Specialties: Bones (3.41M) → ENT (2.93M) → General (2.85M)

---

## 📁 Data Sources

```
Data/
├── Inpatient/
│   ├── IN_WL 2018.csv
│   ├── IN_WL 2019.csv
│   ├── IN_WL 2020.csv
│   └── IN_WL 2021.csv
├── Outpatient/
│   ├── Op_WL 2018.csv
│   ├── Op_WL 2019.csv
│   ├── Op_WL 2020.csv
│   └── Op_WL 2021.csv
└── Mapping_Specialty.csv
```

| Folder | Files | Description | Approx Size |
|--------|-------|-------------|-------------|
| `Inpatient/` | IN_WL 2018–2021.csv | Inpatient wait list data | ~12 MB |
| `Outpatient/` | Op_WL 2018–2021.csv | Outpatient wait list data | ~14 MB |
| Root | Mapping_Specialty.csv | Specialty group mapping table | 3 KB |

**Total Records:** 24.6 Million+ across all years

---

## 🔧 Tools & Technologies

| Tool | Purpose |
|------|---------|
| **Power BI Desktop** | Dashboard development & visualizations |
| **Power Query (M)** | Data transformation, cleaning & ETL |
| **DAX** | Calculated measures & KPIs |
| **PowerPoint / Canva** | Custom dashboard background design |
| **CSV Files** | Raw data source format |

---

## 📐 Data Transformation Steps (Power Query)

1. **Renamed columns** — Matched `Specialty` column names across Inpatient & Outpatient tables
2. **Rearranged columns** — Standardized column order in both tables
3. **Added custom column** — Created `Case_Type = "Outpatient"` for the Outpatient table
4. **Appended tables** — Combined Inpatient + Outpatient into a single `All_Data` table
5. **Cleaned data** — Replaced inconsistent values (e.g. `"18+ months"` vs `"18 month +"`) and trimmed trailing spaces in `Age_Profile` & `Time_Band` columns
6. **Data model** — Created relationship between `All_Data[Specialty_Name]` and `Mapping_Specialty[Specialty]`

---

## 📏 DAX Measures

```dax
-- Latest month total wait list
Latest Month Wait List =
CALCULATE(
    SUM(All_Data[Total]),
    All_Data[Archive_Date] = MAX(All_Data[Archive_Date])
) + 0

-- Previous year same month wait list
PY Latest Month Wait List =
CALCULATE(
    SUM(All_Data[Total]),
    All_Data[Archive_Date] = EDATE(MAX(All_Data[Archive_Date]), -12)
) + 0

-- Average wait list
Average Wait List = AVERAGE(All_Data[Total])

-- Median wait list
Median Wait List = MEDIAN(All_Data[Total])

-- Dynamic measure that toggles between Average and Median
Avg/Med Wait List =
SWITCH(
    VALUES('Calculation Method'[Calc Method]),
    "Average", [Average Wait List],
    "Median",  [Median Wait List]
)

-- Dynamic chart title
Dynamic Title =
SWITCH(
    VALUES('Calculation Method'[Calc Method]),
    "Average", "Key Indicators - Patient Wait List (Average)",
    "Median",  "Key Indicators - Patient Wait List (Median)"
)

-- No data messages for split line charts
NoDataLeft =
IF(
    ISBLANK(CALCULATE(SUM(All_Data[Total]), All_Data[Case_Type] <> "Outpatient")),
    "No data for selected criteria", ""
)

NoDataRight =
IF(
    ISBLANK(CALCULATE(SUM(All_Data[Total]), All_Data[Case_Type] = "Outpatient")),
    "No data for selected criteria", ""
)
```

---

## 📈 Key Insights

| Metric | Value |
|--------|-------|
| Latest Month Wait List | **709K** |
| Previous Year Wait List | **640K** |
| YoY Change | **+10.8%** |
| Total Wait List (All Years) | **25M+** |
| Outpatient Share | **72.49%** |
| Day Case Share | **16.89%** |
| Inpatient Share | **10.62%** |
| Highest Specialty | **Bones — 3.41M** |
| Most Critical Time Band | **18+ Months** |

---

## 📂 Project Structure

```
healthcare-waitlist-powerbi/
│
├── Data/
│   ├── Inpatient/
│   ├── Outpatient/
│   └── Mapping_Specialty.csv
│
├── Report/
│   └── Healthcare.pbix
│
├── Screenshots/
│
└── README.md
```

---

## 💡 Project Approach

This project followed a structured 9-step BI development lifecycle:

1. **Requirement Gathering** — Defined KPIs, metrics, scope & stakeholders
2. **Data Collection** — Used folder connector in Power BI for automated refresh support
3. **Data Transformation** — Cleaned, renamed, appended tables in Power Query
4. **Data Modelling** — Created star schema with specialty mapping table
5. **Visualization Blueprint** — Designed wireframe before development
6. **Dashboard Development** — Built visuals with DAX measures & dynamic titles
7. **Interactivity** — Added navigation buttons, tooltips & toggle slicers
8. **Testing (UAT)** — Validated all measures and visual interactions
9. **Documentation** — Created this README for reproducibility
