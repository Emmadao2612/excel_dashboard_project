# Data Jobs Salary Dashboard

---

> An interactive Excel dashboard helping data professionals and job seekers benchmark compensation across roles, countries, and employment types - built on real 2023 job market data.

![Interactive Excel dashboard showing salary data by job title, country, and schedule type](images/dashboard_preview.png)

---

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Excel Skills Used](#excel-skills-used)
- [Dashboard Build](#dashboard-build)
  - [Charts - Median Salary by Job Title and Median Salary by Schedule Type](#charts---median-salary-by-job-title-and-median-salary-by-schedule-type)
  - [Data Validation](#data-validation)
  - [Excel Functions](#excel-functions)
    - [Array Formula](#array-formula)
    - [Dropdown List Formula](#dropdown-list-formula)
    - [Chart Highlight Logic](#chart-highlight-logic)
    - [Count Formula](#count-formula)
    - [KPI Display](#kpi-display)
- [Insights](#insights)

---

## Introduction

Understanding salary ranges is one of the most powerful steps a job seeker can take before entering any salary negotiation.

The dataset contains **32,672 real-world data job postings from 2023**, spanning 111 countries and including 10 standardized job titles. 

Three dropdown controls filter the entire dashboard simultaneously - job title, country, and schedule type - and every bar chart, KPI card, and metric updates instantly without any manual refresh.

View the dataset file here: [Data_Jobs_Salary_Dataset](data_jobs_salary_all.xlsx)

View the completed dashboard file here: [Project_1_Salary_Dashboard](Project_1_Salary_Dashboard.xlsx)

This is Part 1 of a two-part series. See [Project 2 - Salary Analysis](Project_2_Salary_Analysis.xlsx) for the in-depth skills and pay analysis.

---

## Getting Started

### Requirements
- Microsoft Excel (2019 or later recommended) with support for array formulas and Power Query.

### How to Use
1. Open the `Project_1_Salary_Dashboard.xlsx` file in Excel.
2. Use the dropdown menus for Job Title, Country, and Schedule Type to filter the data.
3. The charts, KPIs, and metrics will update automatically based on your selections.
4. Compare median salaries across roles and regions to inform salary negotiations.

**Note**: Data is from 2023 and may not reflect current market changes. The median is used rather than the average to reduce the influence of salary outliers and provide a more representative view.

---

## Excel Skills Used

- **Charts** - Horizontal bar charts for visualization with conditional highlighting
- **Array Formulas** - `MEDIAN(IF(...))` - multi-criteria salary calculation
- **Filter Function** - Clean dropdown list generation
- **Data Validation** - Dropdown controls restricting inputs to valid values 
- **Named Ranges** - `title`, `country`, `type`, `median_salary`, `count`, `platform` connecting dropdown selections to formula outputs
- **Power Query** - Background data transformation and lookup table preparation
- **Conditional Chart Series** - `IF()`/`NA()` logic to highlight the selected bar in a contrasting color

---

## Dashboard Build

### Charts - Median Salary by Job Title and Median Salary by Schedule Type

Both charts are horizontal bar charts that update dynamically based on the active dropdown selections.
- Job titles are sorted in descending salary order so the highest-paying roles appear at the top for instant comparison
- Two chart series using `IF`/`NA()` logic to highlight the currently selected title and schedule type in a contrasting color, while all other bars remain muted

### Data Validation

All three dropdowns (Job Title, Country, Type) use **Data Validation → List** feature.
- User input is restricted to predefined, validated options
- Incorrect or inconsistent entries are prevented
- Improves overall dashboard usability by guiding users toward valid selections 

### Excel Functions

#### Array Formula

```
=IFERROR(
  MEDIAN(
    IF(
      (jobs[job_title_short] = title)  *
      (jobs[job_country]     = country) *
      (ISNUMBER(SEARCH(type, jobs[job_schedule_type]))) *
      (jobs[salary_year_avg] <> 0),
      jobs[salary_year_avg]
    )
  ),
  "Insufficient Data"
)
```

- Filters rows simultaneously by job title, country, and schedule type using multiplied boolean conditions
- `ISNUMBER(SEARCH(...))` allows partial matching for schedule type, so selecting "Full-time" also captures "Full-time and Part-time" rows
- `<>0` excludes null/zero values in the annual salary column
- Applies `MEDIAN()` function with a nested `IF()` statement to calculate the result across the filtered array
- Using the median rather than the average reduces the effect of salary outliers

#### Dropdown List Formula

```
=FILTER(J2#, (NOT(ISNUMBER(SEARCH("and",J2#))+ISNUMBER(SEARCH(",",J2#)))) * (J2#<>0))

```

- Takes a unique-values spill array (`J2#`) produced by a prior `UNIQUE()` formula
- Excludes combined schedule types containing "and" or "," (e.g. "Full-time and Part-time")
- Excludes zero and empty values

#### Chart Highlight Logic

```
=IF($D2<>title, $E2, NA())   -- unselected series (greyed bars)
=IF($D2=title, $E2, NA())    -- selected series (highlighted bar)

```

- Each job title row feeds two series: one returns the value when *not* selected, the other when *selected*
- `NA()` suppresses the bar for the inactive series
- Named ranges `title`, `country`, and `type` link directly to the dropdown cells on the dashboard

#### Count Formula

```
=COUNTIFS(jobs[job_via], A2, jobs[job_title_short], title, jobs[job_country], country, jobs[job_schedule_type], type)

```

- Multi-condition count filters job board volume to match the current dashboard selections
- Updates dynamically as the user changes any dropdown

#### KPI Display

```
=XLOOKUP(title, D2:D11, E2:E11, "No Results")

```

- Returns the median salary for the selected title from the background Title sheet and displays it as a KPI card on the dashboard
- Returns "No Results" if no salary data exists for the current selections

---

## Insights

- Data and Business Analyst roles sit at the lower end of the salary range, with a median of $90K in the United States.
- Data Engineer roles earn approximately $35K more than Analyst roles - a gap driven by the greater technical specialization required for engineering roles.
- Senior roles add $20-30K across every role category.
- Full-time roles have a higher median salary than Contractor or Part-time positions, suggesting that flexibility comes at a measurable financial cost.
- Indeed is the leading job platform for Data Analyst roles in the US, based on the default dashboard selection.

