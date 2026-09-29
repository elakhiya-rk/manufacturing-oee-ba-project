# Manufacturing Downtime & OEE Analysis — Business Analysis Portfolio Project

**Author:** Elakhiya Ramakrishnan Karthikeyan · Business Analyst | Power BI & SQL · Fremont, CA
**Connect:** [LinkedIn](https://www.linkedin.com/in/YOUR-LINKEDIN) · [3-min video walkthrough](YOUR-LOOM-LINK)

> **Note:** *Apex Precision Components* is a fictional company, and all data in this repo is synthetic. It was generated to simulate a Manufacturing Execution System (MES) export. The business analysis methods, documents and deliverables reflect real BA practice.

---

## The business problem

Apex runs **9 machines on 3 production lines, 3 shifts a day**. Leadership knows the plant is losing capacity but cannot say where, why, or how much:

- The weekly downtime report takes **6 hours** to build by hand in Excel and is **7 days old** when managers see it.
- **14% of downtime** has no usable reason code, so it can't be explained.
- Different people calculate OEE (Overall Equipment Effectiveness) in different ways.

**Goal:** Give leaders a daily, trusted view of equipment losses and turn it into prioritized improvement actions.

---

## Dashboard

![Executive Overview](04_powerbi/screenshots/01_executive_overview.png)

| Downtime Deep-Dive | Quality & Scrap |
|---|---|
| ![Downtime](04_powerbi/screenshots/02_downtime.png) | ![Quality](04_powerbi/screenshots/03_quality.png) |

<!-- Replace the image paths above with your own screenshot file names after uploading them. -->

---

## Key findings (Q3 2026 baseline)

**Plant OEE: 71.2%** (Availability 85.5% × Performance 85.4% × Quality 97.5%) against an 85% world-class benchmark.

| # | Finding | Evidence | Recommendation |
|---|---|---|---|
| 1 | Line 3 changeovers take twice the target | 51.8 min average vs 25 min target, about **205 excess hours per quarter** | SMED (quick changeover) workshop and standard changeover kits |
| 2 | CNC-05 is unreliable on the night shift | Shift C availability **71%** vs 86% on day shifts | Root-cause analysis and a preventive maintenance slot before Shift C |
| 3 | One product drives scrap on one machine | P-104 on CNC-04: **8.3%** scrap vs 2.1% plant average, **$61.5K per quarter** | Statistical process control on critical dimensions, tool-wear check |
| 4 | Material shortages cluster on Monday mornings | Monday Shift A has **3×** the shortage downtime of any other shift | Move the replenishment cut-off to Friday |
| 5 | Downtime data is incomplete | **312 hours** (14.3%) of downtime unclassified | Make reason codes mandatory in the MES |

---

## What I did

| Phase | BA activities | Deliverable |
|---|---|---|
| 1. Discovery | Problem statement, SMART objectives, stakeholder analysis (Power/Interest grid, RACI), elicitation plan | [Stakeholder RACI & elicitation plan](05_delivery/BA_Artifacts_Apex_OEE.xlsx) |
| 2. Process analysis | AS-IS and TO-BE swimlane process maps, pain-point analysis | [Process maps](01_business_analysis/) |
| 3. Requirements | BRD with 14 functional and 6 non-functional requirements (MoSCoW), business rules, KPI definitions | [BRD (PDF)](01_business_analysis/BRD_Apex_Downtime_OEE.pdf) |
| 4. Data profiling | SQL checks that found 6 data-quality issues (inconsistent IDs, missing reasons, duplicates, invalid timestamps) | [01_data_quality_profile.sql](03_sql/01_data_quality_profile.sql) |
| 5. Data cleaning | SQL transformation using CTEs, `ROW_NUMBER()` de-duplication and correlated subqueries | [02_clean_transform.sql](03_sql/02_clean_transform.sql) |
| 6. Analysis | KPI queries answering 8 business questions using window functions (`RANK`, running totals) | [03_kpi_analysis.sql](03_sql/03_kpi_analysis.sql) |
| 7. Reporting | 3-page Power BI dashboard with star schema and DAX OEE measures | [Dashboard + DAX spec](04_powerbi/) |
| 8. Agile delivery | 5 epics and 12 user stories with Gherkin acceptance criteria in Jira, 2 sprints planned | [User stories](05_delivery/BA_Artifacts_Apex_OEE.xlsx) |
| 9. Testing | 12 UAT test cases (including negative tests) and a requirements traceability matrix | [UAT & RTM](05_delivery/BA_Artifacts_Apex_OEE.xlsx) |

**AI-assisted BA:** I used Claude to challenge my business objectives from a plant manager's point of view and to review the BRD for ambiguous or untestable requirements. <!-- Add one specific change you made because of the review. -->

---

## Tools & skills

`SQL (SQLite)` `Power BI` `DAX` `Excel` `Jira` `draw.io` `Claude`
Requirements elicitation · BRD · User stories · UAT · Traceability (RTM) · Process mapping · Data quality · KPI design · Stakeholder management

---

## Repository structure

```
manufacturing-oee-ba-project/
├── 01_business_analysis/   BRD, process maps
├── 02_data/
│   ├── raw/                5 raw CSVs from the simulated MES export
│   └── clean/              cleaned tables used by Power BI
├── 03_sql/                 profiling, cleaning and KPI analysis scripts
├── 04_powerbi/             .pbix file, screenshots, DAX measures
└── 05_delivery/            user stories, UAT test cases, RTM, executive summary
```

## How to reproduce

1. Open [DB Browser for SQLite](https://sqlitebrowser.org) and create a new database.
2. Import the 5 CSVs from `02_data/raw/` as tables (keep the file names as table names).
3. Run the scripts in `03_sql/` in order: 01 → 02 → 03.
4. Open `04_powerbi/Apex_OEE_Dashboard.pbix` in Power BI Desktop. It reads the CSVs in `02_data/clean/`.

---

## Data model

| Table | Grain | Rows |
|---|---|---|
| machines | 1 per machine | 9 |
| products | 1 per product | 6 |
| production_log | 1 per machine per shift | 2,016 |
| downtime_events | 1 per stoppage | 3,286 |
| quality_checks | 1 per production log | 2,016 |

**OEE formula:** Availability (run time ÷ planned time) × Performance (ideal cycle time × units ÷ run time) × Quality (good units ÷ total units)
