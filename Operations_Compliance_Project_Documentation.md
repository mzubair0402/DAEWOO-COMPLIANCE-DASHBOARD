# Operations Compliance Analytics — Power BI Project

> **Project:** Operations Compliance Final  
> **Platform:** Microsoft Power BI  
> **Business Domain:** Operations / Customer Complaints / Compliance Monitoring  
> **Primary Analytical Areas:** Complaint volume, resolution performance, unresolved/in-process cases, category analysis, channel/medium analysis, suggestions, and date-wise trends.

---

## 1. Executive Summary

**Operations Compliance Final** is a Power BI business-intelligence solution designed to monitor and analyze operational compliance through two closely related information streams:

1. **Customer complaints / compliance cases**
2. **Customer suggestions**

The report is structured as a multi-page management dashboard rather than a single static report. It combines KPI cards, category analysis, medium/channel analysis, detailed tables, and time-series visualizations.

The PBIX contains **five report pages**:

| Page | Primary Purpose |
|---|---|
| **Category Wise** | Category-level compliance/complaint performance |
| **Medium & Category Wise** | Cross-analysis of complaint categories and reporting mediums |
| **Compliant Table** | Detailed complaint/compliance records and KPI breakdown |
| **Suggestion Table** | Detailed suggestion analysis |
| **Date Wise** | Time-based operational compliance analysis |

The semantic model contains six identified entities:

- `Compliants`
- `Suggestion`
- `DateTable`
- `Unique Category`
- `Calculations`
- `Mediums`

> **Note:** The PBIX model contains the table/measure metadata required to document the report structure. The internal Power BI `DataModel` is stored in Microsoft's compressed format, so exact source queries and complete DAX expressions are not reproduced here unless they can be reliably recovered. This documentation intentionally separates confirmed model/report evidence from business interpretation.

---

# 2. Business Problem

Operational compliance reporting often involves large volumes of complaints, suggestions, categories, and multiple reporting channels.

Without a centralized analytical layer, management has difficulty answering:

- How many complaints were received?
- How many have been resolved?
- How many remain unresolved/in process?
- What percentage of cases have been resolved?
- Which complaint categories create the largest workload?
- Which channels generate the most complaints?
- How does performance change over time?
- How does current performance compare with last year?
- Which categories require management attention?
- What suggestions are being submitted and in which categories?

This Power BI solution addresses these questions through an interactive analytical model.

---

# 3. Project Objectives

### Primary objective

Create a centralized operational-compliance dashboard that converts complaint and suggestion data into actionable management information.

### Specific objectives

- Monitor total complaint volume.
- Monitor resolved complaint volume.
- Monitor unresolved/in-process cases.
- Calculate resolution percentages.
- Analyze complaints by main category.
- Analyze complaints by sub-category.
- Analyze complaints by reporting medium.
- Compare current performance with previous year.
- Track complaints over time.
- Track suggestions separately from complaints.
- Provide detailed tables for operational follow-up.
- Allow users to filter the analysis by date/category/medium.

---

# 4. Report Architecture

The report follows this analytical progression:

```text
                    OPERATIONS COMPLIANCE
                             │
            ┌────────────────┴────────────────┐
            │                                 │
       COMPLAINTS                         SUGGESTIONS
            │                                 │
     ┌──────┼───────┐                         │
     │      │       │                         │
 Category  Medium  Date                    Category
     │      │       │                         │
     └──────┼───────┘                         │
            │                                 │
            ▼                                 ▼
       KPI / Trend                       Detailed Analysis
            │
            ▼
     Management Insight
```

---

# 5. Semantic Model

## 5.1 Model Entities

The model diagram identifies:

```text
┌────────────────────┐
│     Compliants     │
└─────────┬──────────┘
          │
          │
┌─────────▼──────────┐
│   Calculations     │
└────────────────────┘

┌────────────────────┐
│      Suggestion    │
└────────────────────┘

┌────────────────────┐
│     DateTable      │
└────────────────────┘

┌────────────────────┐
│   Unique Category  │
└────────────────────┘

┌────────────────────┐
│      Mediums       │
└────────────────────┘
```

The diagram layout confirms these six model nodes. The exact relationship cardinalities and storage-engine metadata are not safely recoverable from the compressed DataModel in this environment, so they should be validated in Power BI Model view before making structural changes.

---

# 6. Table / Entity Dictionary

## 6.1 `Compliants`

This is the primary complaint/compliance entity.

Fields directly referenced by report visuals include:

- `Main Category`
- `Sub-Category`

The report uses this entity to analyze complaint workload at both high-level and detailed category levels.

### Business role

`Compliants` is the core operational fact/entity behind the complaint analysis.

It supports:

- total complaints,
- resolved complaints,
- unresolved/in-process complaints,
- category analysis,
- time analysis,
- medium/channel analysis.

---

## 6.2 `Suggestion`

`Suggestion` is the dedicated entity for customer suggestions.

Fields referenced by report visuals include:

- `Category`
- `Main Category`

The model also contains a calculation:

- `Suggestions`

### Business role

Keeping suggestions in a dedicated entity separates **customer improvement ideas/feedback** from actual complaints or compliance cases.

This is a good conceptual separation because:

```text
Complaint ≠ Suggestion
```

They can have different:

- workflows,
- owners,
- resolution definitions,
- management KPIs.

---

## 6.3 `DateTable`

`DateTable` is the temporal dimension.

The report explicitly uses:

- `DateTable.Date`

It is used across the report's time-based analysis and date slicers.

### Business role

A dedicated date table supports:

- daily reporting,
- date filtering,
- time-series charts,
- period comparisons,
- year-over-year calculations.

---

## 6.4 `Unique Category`

`Unique Category` provides category-level filtering.

Confirmed field:

- `Main Category`

It is used as a slicer in the report.

### Business role

This table appears to provide a controlled category list separate from the complaint transaction/entity.

This can be useful for:

- consistent category filtering,
- category master-data management,
- avoiding duplicated category selections.

---

## 6.5 `Mediums`

`Mediums` provides the reporting/contact channel dimension.

Confirmed field:

- `Mediums`

The report uses this field as a filter and analytical dimension.

Examples of medium-specific measures visible in the model include:

- Telephone
- Mobile_App
- Complaint-Book
- E-Mail

This indicates that the dashboard is designed to analyze **how customers submit complaints**.

---

## 6.6 `Calculations`

`Calculations` is the report's central measure layer.

Confirmed measures include:

### Complaint volume

- `Total Compliants`
- `Total Resolved`
- `Unsolved Compliants`
- `In Process`

### Resolution metrics

- `Resolved %`
- `In Process %`
- `IN_PROCESS %`

### Medium/channel measures

- `Telephone`
- `Mobile_App`
- `Complaint-Book`
- `E-Mail`

### Suggestion

- `Suggestions`

### Previous-year measures

- `LY Total Compliants`
- `LY Resolved Compliants`
- `LY Resolved %`
- `LY In Process`
- `LY In Process %`

This is an important architectural feature because it centralizes business logic instead of embedding calculations independently into every visual.

---

# 7. KPI Dictionary

## 7.1 Total Compliants

**Measure:** `Calculations[Total Compliants]`

### Business definition

Total number of complaint/compliance cases within the current filter context.

### Used for

- category analysis,
- medium analysis,
- KPI cards,
- detailed tables,
- time trends.

### Typical grain

The value changes according to:

- date,
- main category,
- sub-category,
- medium.

---

# 8. Total Resolved

**Measure:** `Calculations[Total Resolved]`

### Business definition

Number of complaint/compliance cases classified as resolved.

### Business use

Measures the completed workload handled by the operations/compliance process.

---

# 9. Unsolved Compliants

**Measure:** `Calculations[Unsolved Compliants]`

The report also references the calculation property:

`In Process`

### Business interpretation

This KPI represents the current unresolved/in-process workload.

It is particularly important for operational management because unresolved cases are the outstanding workload that may require escalation or follow-up.

---

# 10. Resolved %

**Measure:** `Calculations[Resolved %]`

### Business definition

Percentage of complaint cases that have been resolved.

Conceptually:

```text
Resolved % =
Resolved Complaints / Total Complaints
```

### Management significance

This is one of the most important KPIs in the dashboard because it converts raw complaint counts into a performance indicator.

---

# 11. In Process %

**Measure:** `Calculations[In Process %]`

### Business definition

Percentage of complaint cases that remain unresolved/in process.

Conceptually:

```text
In Process % =
In-Process Complaints / Total Complaints
```

### Management significance

This KPI provides the inverse operational perspective to resolution rate.

---

# 12. Medium / Channel KPIs

The model contains measures for:

- `Telephone`
- `Mobile_App`
- `Complaint-Book`
- `E-Mail`

These measures indicate that complaint intake is being monitored across multiple channels.

### Analytical framework

```text
Complaint Volume
       │
       ├── Telephone
       ├── Mobile App
       ├── Complaint Book
       └── E-Mail
```

This enables management to identify the dominant complaint channels.

---

# 13. Previous-Year KPI Layer

The model contains:

- `LY Total Compliants`
- `LY Resolved Compliants`
- `LY Resolved %`
- `LY In Process`
- `LY In Process %`

`LY` is interpreted as **Last Year** based on the measure naming and its use in KPI visuals.

### Purpose

The previous-year layer allows performance benchmarking.

Example:

```text
Current Year
      vs.
Last Year
```

This makes the dashboard more useful than a simple operational count report.

---

# 14. Category Hierarchy

The complaint analysis uses two category levels:

```text
Main Category
      │
      └── Sub-Category
```

### Main Category

High-level classification used for management analysis.

### Sub-Category

Detailed classification used to identify the specific nature of complaints.

This hierarchy enables:

- management-level grouping,
- detailed root-cause analysis,
- category ranking,
- drill-down analysis.

---

# 15. Report Page 1 — Category Wise

## Purpose

The **Category Wise** page is designed to provide a category-level management overview.

It contains:

- date filtering,
- category filtering,
- medium filtering,
- complaint category visuals,
- KPI cards,
- category-level comparisons.

### Confirmed visual types

The page contains **19 visual containers**, including:

- shapes,
- slicers,
- list slicers,
- textboxes,
- image,
- clustered bar chart,
- clustered column chart,
- KPI/card visual,
- action button.

---

## Category analysis

The page references:

- `Compliants.Main Category`
- `Compliants.Sub-Category`

and measures including:

- Total Compliants
- Total Resolved
- Unsolved Compliants
- Resolved %
- In Process %
- Telephone
- Mobile_App

### Business question

> Which complaint categories create the highest operational workload and how effectively are they being resolved?

---

# 16. Report Page 2 — Medium & Category Wise

## Purpose

This is the most explicitly analytical page in the report because it combines:

- medium/channel,
- main category,
- sub-category,
- complaint volume,
- resolution,
- previous-year KPIs.

### Confirmed visual types

The page contains **19 visual containers**:

- slicers,
- list slicers,
- clustered bar charts,
- clustered column charts,
- KPI/card visual,
- image,
- textboxes,
- action button,
- shapes.

---

## 16.1 Complaint by Sub-Category

Confirmed visual:

`clusteredBarChart`

Dimension:

`Compliants.Sub-Category`

Measures:

- Total Compliants
- Total Resolved
- Telephone
- Mobile_App

### Business purpose

Allows detailed identification of complaint areas with high workload and their channel composition.

---

## 16.2 Complaint by Main Category

Confirmed visual:

`clusteredColumnChart`

Dimension:

`Compliants.Main Category`

Measures:

- Total Compliants
- Total Resolved
- Telephone
- Mobile_App

### Business purpose

Provides management-level category comparison.

---

## 16.3 Medium Filter

The page includes:

`Mediums[Mediums]`

as a list slicer.

This enables users to isolate a particular complaint channel.

---

## 16.4 KPI Card

The KPI visual contains:

### Current performance

- Total Compliants
- Total Resolved
- Unsolved Compliants
- Resolved %
- In Process %

### Previous year

- LY Total Compliants
- LY Resolved Compliants
- LY Resolved %
- LY In Process
- LY In Process %

This is the strongest executive KPI layer in the report.

---

# 17. Report Page 3 — Compliant Table

## Purpose

The **Compliant Table** page is a detailed operational view.

Confirmed visual type:

`pivotTable`

The page also contains:

- slicers,
- category filters,
- date filtering,
- image,
- text,
- action button.

### Confirmed fields/metrics associated with the page

- `Compliants.Main Category`
- `Compliants.Sub-Category`
- `Unique Category.Main Category`
- Total Compliants
- Total Resolved
- Unsolved Compliants
- Resolved %
- IN_PROCESS %
- Telephone
- Mobile_App
- Complaint-Book
- E-Mail

### Business purpose

This page is better suited for operational follow-up than the executive pages.

---

# 18. Report Page 4 — Suggestion Table

## Purpose

The **Suggestion Table** page focuses specifically on customer suggestions.

Confirmed model fields:

- `Suggestion.Category`
- `Suggestion.Main Category`

Confirmed calculation:

- `Calculations.Suggestions`

### Confirmed visual types

The page contains:

- pivot tables,
- bar chart,
- line chart,
- slicers,
- category filters,
- date filters,
- image,
- text,
- action button.

### Business purpose

Suggestions should be treated as a separate improvement-feedback stream.

The page supports analysis of:

```text
Suggestion Volume
       ↓
Main Category
       ↓
Sub/Category
       ↓
Date Trend
```

---

# 19. Report Page 5 — Date Wise

## Purpose

The **Date Wise** page provides time-based analysis of operational compliance.

Confirmed visual types include:

- pivot table,
- area chart,
- clustered column chart,
- clustered bar chart,
- slicers,
- image,
- action button.

### Confirmed measures

- Total Compliants
- Total Resolved
- Unsolved Compliants
- Resolved %
- IN_PROCESS %
- Telephone
- Mobile_App
- Complaint-Book
- E-Mail

### Confirmed time field

`DateTable[Date]`

---

# 20. Time-Series Analytical Framework

The Date Wise page provides the foundation for:

```text
Date
 │
 ├── Complaint Volume
 ├── Resolved Volume
 ├── In-Process Volume
 ├── Resolution %
 ├── In-Process %
 └── Medium/Channel Volume
```

This is useful for identifying:

- complaint spikes,
- seasonal patterns,
- periods of weak resolution performance,
- unusual increases in specific channels,
- operational workload trends.

---

# 21. Filter Architecture

The report contains several filter mechanisms.

### Date

`DateTable[Date]`

Used for time-based filtering.

### Main Category

`Unique Category[Main Category]`

Used as a controlled category filter.

### Sub-Category

`Compliants[Sub-Category]`

Used for detailed complaint filtering.

### Medium

`Mediums[Mediums]`

Used to isolate complaint intake channels.

This creates a multidimensional filtering experience:

```text
Date
 +
Main Category
 +
Sub-Category
 +
Medium
      ↓
Complaint KPIs
```

---

# 22. Management Questions Answered

The dashboard supports the following management questions.

## Complaint workload

- How many complaints have been received?
- Which categories generate the most complaints?
- Which sub-categories generate the most complaints?

## Resolution

- How many complaints have been resolved?
- How many remain in process?
- What is the resolution percentage?
- What percentage remains in process?

## Channel

- Are complaints primarily coming through telephone?
- How significant is the mobile-app channel?
- How many complaints come through the complaint book?
- How many are received through e-mail?

## Trend

- When are complaint volumes increasing?
- When are complaints decreasing?
- Are resolution rates improving over time?

## Benchmarking

- How does current complaint volume compare with last year?
- How does current resolution performance compare with last year?
- Is the in-process percentage improving?

## Suggestions

- What categories generate the most suggestions?
- How does suggestion volume change over time?
- Which areas are generating recurring customer improvement ideas?

---

# 23. End-to-End Analytical Process

The report's analytical process can be summarized as:

```text
Operational Complaint / Suggestion Data
                 │
                 ▼
        Data Model / Dimensions
                 │
       ┌─────────┼──────────┐
       │         │          │
       ▼         ▼          ▼
     Date     Category    Medium
       │         │          │
       └─────────┼──────────┘
                 ▼
            DAX Measures
                 │
       ┌─────────┼──────────┐
       │         │          │
       ▼         ▼          ▼
     Volume   Resolution   Channel
       │         │          │
       └─────────┼──────────┘
                 ▼
        Dashboard Visuals
                 │
                 ▼
        Management Decisions
```

---

# 24. KPI Dependency Structure

A useful conceptual KPI hierarchy is:

```text
                         Total Complaints
                                │
                  ┌─────────────┴─────────────┐
                  ▼                           ▼
           Total Resolved               In Process
                  │                           │
                  ▼                           ▼
             Resolved %                 In Process %
```

This makes the report a performance-management tool rather than merely a complaint-count report.

---

# 25. Channel Analysis Framework

The model supports channel-level analysis:

```text
                         COMPLAINTS
                              │
          ┌───────────┬───────┼────────┬───────────┐
          ▼           ▼       ▼        ▼
      Telephone   Mobile App  E-Mail  Complaint Book
```

### Why this matters

Channel analysis can identify:

- customer behavior,
- preferred complaint channels,
- channel-specific workload,
- digital adoption,
- opportunities for process improvement.

---

# 26. Suggested Operational Insights

The existing model can support several high-value analyses.

## 26.1 High-Volume Categories

Rank categories by:

```text
Total Complaints
```

Then focus management attention on the highest-volume areas.

---

## 26.2 Resolution Performance

Rank categories by:

```text
Resolved %
```

A category with:

- high complaint volume
- low resolution %

should receive higher operational priority.

---

## 26.3 Backlog Risk

Rank categories by:

```text
In Process
```

High unresolved workload can indicate:

- delayed ownership,
- escalation requirements,
- process bottlenecks,
- insufficient resources.

---

## 26.4 Channel Risk

Compare:

```text
Telephone
Mobile App
Complaint Book
E-Mail
```

with total complaint volume.

This can identify channels requiring:

- staffing,
- automation,
- process redesign,
- monitoring.

---

# 27. Advanced KPI Recommendations

The current dashboard already contains a strong KPI foundation. The following additions would significantly improve it.

## Complaint KPIs

### YoY Complaint Growth %

```text
(Current Complaints - LY Complaints)
/
LY Complaints
```

### YoY Resolution Growth %

```text
(Current Resolved - LY Resolved)
/
LY Resolved
```

### Backlog Rate

```text
In Process / Total Complaints
```

### Resolution Gap

```text
100% - Resolved %
```

---

# 28. Recommended SLA Analytics

If complaint-open and complaint-close timestamps are available, the next major improvement should be SLA monitoring.

Recommended measures:

- Average Resolution Time
- Median Resolution Time
- SLA Compliance %
- Cases Over SLA
- Cases Due Today
- Cases Due in 24 Hours
- Cases Over 7 Days
- Aging 0–1 Days
- Aging 2–3 Days
- Aging 4–7 Days
- Aging 8–14 Days
- Aging 15+ Days

Example structure:

```text
Open Case
    │
    ▼
Calculate Aging
    │
    ├── 0–1 Days
    ├── 2–3 Days
    ├── 4–7 Days
    ├── 8–14 Days
    └── 15+ Days
```

This would transform the report from **performance reporting** into an **operational compliance management system**.

---

# 29. Recommended Root-Cause Analysis

The existing Main Category / Sub-Category hierarchy is ideal for root-cause analysis.

Recommended analytical framework:

```text
Main Category
      │
      ▼
Sub-Category
      │
      ▼
Complaint Volume
      │
      ▼
Resolution %
      │
      ▼
Root Cause
      │
      ▼
Corrective Action
```

If owner/terminal/department information is available, add:

- Terminal
- Region
- Department
- Responsible employee/team
- Complaint owner
- Resolution status
- Root cause
- Corrective action
- Closure date

---

# 30. Data Quality Recommendations

A production version should include a dedicated Data Quality page.

Recommended checks:

### Completeness

- Missing complaint date
- Missing category
- Missing sub-category
- Missing medium
- Missing status

### Validity

- Invalid category
- Invalid medium
- Invalid date
- Duplicate complaint ID

### Logical consistency

Examples:

```text
Resolved case with blank resolution date
Resolved % > 100%
Negative complaint counts
In Process > Total Complaints
Duplicate complaint IDs
```

---

# 31. Recommended Dashboard Enhancement

A future executive page could be structured as:

```text
┌──────────────────────────────────────────────────────┐
│           OPERATIONS COMPLIANCE OVERVIEW              │
├──────────┬──────────┬──────────┬──────────┬──────────┤
│ Total    │ Resolved │ In       │ Resolved │ YoY      │
│ Complaints│         │ Process  │ %        │ Growth   │
├──────────┴──────────┴──────────┴──────────┴──────────┤
│                                                      │
│ Complaint Trend              Resolution Trend        │
│                                                      │
├──────────────────────────────┬───────────────────────┤
│ Category Ranking             │ Medium Analysis       │
│                              │                       │
├──────────────────────────────┴───────────────────────┤
│ Top Problem Categories / Aging / SLA Exceptions       │
└──────────────────────────────────────────────────────┘
```

---

# 32. Technical Power BI Components

The PBIX contains:

- Power BI semantic model
- DAX calculation layer
- Date dimension
- Category dimensions
- Medium dimension
- Complaint entity
- Suggestion entity
- KPI cards
- Slicers
- List slicers
- Pivot tables
- Bar charts
- Column charts
- Area chart
- Line chart
- Images
- Action buttons
- Shapes
- Report navigation/layout elements

---

# 33. Visualization Inventory

| Page | Visual Types |
|---|---|
| Category Wise | Slicers, List Slicers, Bar Chart, Column Chart, Card, Image, Textbox, Action Button |
| Medium & Category Wise | Slicers, List Slicers, Bar Charts, Column Chart, Card, Image, Textbox, Action Button |
| Compliant Table | Slicers, List Slicers, Pivot Table, Image, Textbox, Action Button |
| Suggestion Table | Slicers, List Slicers, Pivot Tables, Bar Chart, Line Chart, Image, Textbox, Action Button |
| Date Wise | Slicers, List Slicers, Pivot Table, Area Chart, Column Chart, Bar Chart, Image, Textbox, Action Button |

---

# 34. Dashboard Design Assessment

The PBIX uses a consistent corporate reporting layout.

The model references:

- `CityPark` as the report theme.
- `CY18SU07` as a shared base theme resource.
- A registered LinkedIn image resource.

The repeated use of:

- header elements,
- filters,
- KPI areas,
- analytical charts,
- detailed tables,
- navigation/action buttons

creates a consistent multi-page reporting experience.

---

# 35. Strengths of the Current Project

## 35.1 Strong KPI Layer

The centralized `Calculations` table is a major strength.

It keeps the analytical logic conceptually separate from raw business entities.

---

## 35.2 Good Complaint/Suggestion Separation

The model distinguishes:

```text
Compliants
Suggestion
```

This avoids mixing fundamentally different customer-feedback processes.

---

## 35.3 Category Hierarchy

Using:

```text
Main Category
      ↓
Sub-Category
```

provides both executive and operational analysis.

---

## 35.4 Multi-Channel Analysis

The presence of:

- Telephone
- Mobile App
- Complaint Book
- E-Mail

creates useful customer-channel analytics.

---

## 35.5 Previous-Year Benchmarking

The LY measures provide historical context.

This is an important step above basic operational reporting.

---

## 35.6 Dedicated Date Table

The `DateTable` provides a proper foundation for time-based analytics.

---

# 36. Potential Weaknesses / Improvements

## 36.1 Naming Standardization

Some names could be standardized.

Examples:

- `Compliants` → `Complaints`
- `Sub-Category` → `Sub Category`
- `IN_PROCESS %` vs `In Process %`
- `Mobile_App` → `Mobile App`
- `Complaint-Book` → `Complaint Book`

Consistent naming improves:

- maintainability,
- documentation,
- user understanding,
- DAX readability.

---

## 36.2 Measure Naming

A recommended convention:

```text
[Total Complaints]
[Resolved Complaints]
[In Process Complaints]
[Resolution %]
[In Process %]
[LY Total Complaints]
[LY Resolved Complaints]
```

This is cleaner than mixing singular/plural and abbreviation conventions.

---

## 36.3 Add Explicit KPI Definitions

Every measure should have:

- Business Definition
- DAX Formula
- Source
- Owner
- Refresh Frequency
- Expected Range

---

## 36.4 Add Data Lineage

A production repository should document:

```text
Source System
     ↓
Power Query
     ↓
Transformation
     ↓
Semantic Model
     ↓
DAX Measures
     ↓
Power BI Visuals
```

---

# 37. Recommended GitHub Repository Structure

```text
operations-compliance-powerbi/
│
├── README.md
│
├── PowerBI/
│   └── Operations Compliance Final.pbix
│
├── Documentation/
│   ├── Project_Documentation.md
│   ├── Data_Model.md
│   ├── KPI_Dictionary.md
│   ├── Business_Logic.md
│   └── Dashboard_Guide.md
│
├── DAX/
│   ├── Complaint_Measures.md
│   ├── Resolution_Measures.md
│   ├── Medium_Measures.md
│   ├── Suggestion_Measures.md
│   └── LY_Measures.md
│
├── PowerQuery/
│   └── Queries.md
│
├── Screenshots/
│   ├── category-wise.png
│   ├── medium-category-wise.png
│   ├── compliant-table.png
│   ├── suggestion-table.png
│   └── date-wise.png
│
└── DataModel/
    └── Model_Diagram.png
```

---

# 38. GitHub README — Suggested Project Description

```text
# Operations Compliance Analytics — Power BI

An interactive Power BI business intelligence solution designed to monitor
operational compliance, customer complaints and customer suggestions.

## Key Capabilities

- Complaint volume monitoring
- Resolution performance
- In-process/backlog monitoring
- Resolution percentage
- Complaint category analysis
- Sub-category analysis
- Complaint-channel analysis
- Previous-year comparison
- Date-wise trend analysis
- Customer suggestion analysis

## Tools

- Microsoft Power BI
- DAX
- Data Modeling
- Power Query
- Time Intelligence
- Data Visualization

## Business Domain

Operations / Customer Experience / Compliance / Transportation
```

---

# 39. Skills Demonstrated

This project demonstrates practical capability in:

## Power BI

- Multi-page dashboard development
- Interactive reporting
- Slicers
- KPI cards
- Drill-down style analysis
- Management reporting
- Table/pivot visualization
- Time-series visualization

## DAX

- KPI measure design
- Resolution percentage
- In-process percentage
- Previous-year measures
- Channel-specific measures
- Centralized calculation architecture

## Data Modeling

- Fact/business entities
- Date dimension
- Category dimension
- Medium dimension
- Measure table
- Separate suggestion entity

## Business Analysis

- Complaint monitoring
- Compliance performance
- Resolution management
- Channel analysis
- Category analysis
- Historical benchmarking
- Customer feedback analysis

---

# 40. Business Impact

The solution provides a centralized view of operational compliance performance.

The intended decision-making flow is:

```text
How many complaints?
          ↓
Where are they coming from?
          ↓
Which categories are affected?
          ↓
How many are resolved?
          ↓
What remains in process?
          ↓
Is resolution performance improving?
          ↓
How does it compare with last year?
          ↓
What corrective action is required?
```

This converts raw complaint records into management-level operational intelligence.

---

# 41. Project Maturity Assessment

| Area | Assessment |
|---|---|
| Power BI Reporting | Strong |
| KPI Layer | Strong |
| Category Analysis | Strong |
| Medium/Channel Analysis | Strong |
| Time Analysis | Good |
| YoY Analysis | Good |
| Suggestion Analysis | Good |
| Data Quality Monitoring | Not evident |
| SLA Monitoring | Not evident |
| Complaint Aging | Not evident |
| Root Cause Analysis | Potential |
| Corrective Action Tracking | Potential |
| Predictive Analytics | Not evident |
| Automated Alerts | Not evident |
| Overall Level | **Intermediate / Advanced Reporting Foundation** |

---

# 42. Recommended Future-State Version

The next version should evolve from:

> **Operations Compliance Reporting**

into:

> **Operations Compliance Performance Management**

### Recommended additions

1. Complaint aging
2. SLA compliance
3. Average resolution time
4. Median resolution time
5. Overdue cases
6. Owner/department analysis
7. Terminal/region analysis
8. Root-cause analysis
9. Corrective-action tracking
10. YoY growth %
11. MTD/YTD KPIs
12. Automated alerts
13. Data-quality monitoring
14. Trend/anomaly detection
15. Executive action dashboard

---

# 43. Portfolio Description

For a professional portfolio or CV, this project can be described as:

> **Operations Compliance Analytics Dashboard — Power BI:** Developed a multi-page Power BI analytics solution for monitoring customer complaints and operational compliance, integrating category, sub-category, reporting-medium, date-wise and previous-year analysis. Built a centralized DAX KPI layer covering complaint volume, resolution performance, in-process backlog, resolution percentages and channel-level metrics, with dedicated analytical pages for complaint categories, mediums, detailed compliance records, suggestions and time-series performance.

### Short CV Version

> Developed a Power BI Operations Compliance Dashboard covering complaint volume, resolution %, in-process backlog, category/sub-category performance, reporting channels, customer suggestions and YoY performance using DAX, dimensional modeling and interactive visual analytics.

---

# 44. Final Assessment

**Operations Compliance Final** is a strong operational BI project because it goes beyond simple complaint counting.

Its strongest architectural features are:

- centralized `Calculations` measure layer,
- dedicated `DateTable`,
- separate complaint and suggestion entities,
- main/sub-category hierarchy,
- multiple complaint channels,
- resolution KPIs,
- previous-year benchmarking,
- dedicated summary and detail pages.

The project is particularly suitable for demonstrating **Power BI + DAX + Data Modeling + Business Analysis** skills in a GitHub portfolio.

The highest-value next step is to add **SLA, aging, ownership, root-cause and corrective-action analytics**. Those additions would turn the existing dashboard into a complete operational compliance management solution.

---

# 45. Technical Documentation Note

This documentation is based on the report/layout/model metadata available inside the uploaded PBIX.

Confirmed from the PBIX:

- 5 report pages
- 6 model entities
- report visual types
- fields used by visuals
- KPI/measure names
- category and medium dimensions
- previous-year measures
- date field
- dashboard structure
- theme/resource metadata

Not safely reconstructed:

- complete Power Query M scripts
- complete source connection definitions
- exact DAX expressions for every measure
- complete physical column metadata
- row-level transactional data

The documentation deliberately avoids fabricating unavailable technical details.

---

## Project Classification

**Business Intelligence / Operations Compliance / Customer Experience Analytics**

**Technology Stack**

```text
Power BI
   │
   ├── DAX
   ├── Power Query
   ├── Data Modeling
   ├── Time Intelligence
   └── Data Visualization
```

**Primary Outcome**

```text
Operational Data
      ↓
Structured Semantic Model
      ↓
DAX KPI Layer
      ↓
Interactive Dashboard
      ↓
Compliance Performance Insight
      ↓
Management Decision
```
