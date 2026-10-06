# Manufacturing Operations & Downtime Intelligence

An end-to-end Power BI project that helps management understand production performance, downtime drivers, maintenance activity, root causes, and the production capacity put at risk by downtime.

**Author:** Aakash Tiwari
**Tools:** Excel · Power Query · Power BI · DAX

---

## 📌 Project Overview

Manufacturers need to know why actual production falls short of planned targets. This project analyzes operational data across four areas:

- Production
- Downtime
- Maintenance
- Machine master information

Excel, Power Query, Power BI, and DAX turn the raw data into an interactive dashboard that answers:

- How does production compare with target?
- How large is the production gap?
- Which machines cause the most downtime?
- What are the main downtime categories and failure reasons?
- How much maintenance activity and cost does each machine need?
- Which machines carry the highest operational risk?
- How much production capacity is affected by downtime?

---

## 🎯 Business Problem

Management wants answers to these questions:

1. Are we meeting our production targets?
2. How large is the production gap?
3. Which machines are responsible for the highest downtime?
4. What are the major causes of downtime?
5. Which machines need the most maintenance attention?
6. Which machines have the most potential production capacity at risk?
7. Do some shifts experience more downtime than others?
8. Where should reliability and preventive-maintenance efforts be prioritized?

---

## 🗂️ Dataset

| Dataset | Key fields |
|---|---|
| **Production** | Production ID, Date, Plant, Production Line, Machine, Shift, Product, Target Units, Actual Units |
| **Downtime** | Downtime ID, Date, Machine, Shift, Downtime Category, Downtime Reason, Downtime Minutes |
| **Maintenance** | Maintenance ID, Date, Machine, Maintenance Type, Duration, Maintenance Cost |
| **Machine Master** | Machine, Plant, Production Line, Machine Type, Standard Production Rate |

---

## 🧹 Data Preparation

The raw data contained realistic quality issues:

- Missing values
- Inconsistent text capitalization
- Duplicate production records
- Suspicious production values
- Inconsistent structures across datasets

All cleaning was done in **Power Query**.

**Key transformations**

- Standardized column names and text values
- Handled missing values
- Removed or managed duplicate records
- Standardized date fields
- Converted downtime minutes into downtime hours
- Validated production values against machine capacity
- Created calculated production fields and potential lost production

### Calculations

```text
Production Gap      = Target Units - Actual Units
Achievement %       = Actual Units / Target Units
Potential Lost Units = Downtime Minutes × Production Rate per Minute
```

> **Note:** Potential Lost Production is a *capacity-at-risk estimate*, based on the machine's standard production rate. It is not confirmed actual production loss or lost sales.

---

## 🏗️ Data Model

The Power BI model uses a relational, star-schema-oriented structure.

**Tables:** Date · Machine Master · Production · Downtime · Maintenance

- The **Date** table provides common time filtering across the operational tables.
- The **Machine Master** table holds machine attributes and connects Production, Downtime, and Maintenance.
- All relationships are **single-direction** to keep filter behavior predictable.

```text
                    Date
                     |
          +----------+----------+
          |          |          |
     Production   Downtime   Maintenance
          |
          |
    Machine Master
```

---

## 📐 DAX Measures

**Production**

```dax
Total Target = SUM('Production cleaned'[Target_Units])

Total Actual = SUM('Production cleaned'[Actual_Units])

Production Gap = [Total Target] - [Total Actual]

Achievement % = DIVIDE([Total Actual], [Total Target], 0)
```

**Downtime**

```dax
Total Downtime Hours = SUM('Downtime cleaned'[Downtime hours])

Downtime Events = COUNTROWS('Downtime cleaned')
```

**Maintenance**

```dax
Total Maintenance Cost = SUM('Maintenance cleaned'[Maintenance_Cost_INR])

Maintenance Events = COUNTROWS('Maintenance cleaned')
```

**Potential production capacity impact**

```dax
Potential Lost Production = SUM('Downtime cleaned'[Potential Lost Units])
```

Additional machine-specific measures analyze M07's downtime, mechanical downtime, maintenance activity, and potential capacity impact.

---

## 📊 Dashboard

The dashboard has three pages.

### 1. Manufacturing Operations: Executive Overview

A high-level view of performance against plan.

- **KPIs:** Target Production, Actual Production, Target Achievement, Production Gap, Downtime Hours, Maintenance Cost
- **Visuals:** Monthly Target vs Actual, Achievement by Plant, Achievement by Production Line, Machine Downtime Ranking

### 2. Downtime & Root Cause Analysis

Goes beyond measuring downtime to find its underlying causes.

- Machine Downtime Ranking
- Downtime by Category
- Top Downtime Reasons
- M07 Mechanical Downtime Drivers
- Machine × Downtime Category Analysis
- Downtime by Shift

### 3. Machine Performance & Risk

Helps prioritize machines that need reliability and maintenance attention.

- Maintenance Cost by Machine
- Maintenance Events by Machine
- Potential Production Capacity Impact by Machine
- Downtime vs Maintenance Cost
- M07 Potential Capacity at Risk

### 📸 Dashboard Preview

**Executive Overview**

![Executive Overview](Screenshots/executive-overview.png)

**Downtime & Root Cause Analysis**

![Downtime & Root Cause Analysis](Screenshots/downtime-root-cause.png)

**Machine Performance & Risk**

![Machine Performance & Risk](Screenshots/machine-performance.png)

---

## 🔍 Key Findings

| Metric | Result |
|---|---|
| Overall target achievement | ~90.83% |
| Overall production gap | ~4 million units |
| M07 downtime | ~794 hours (highest of all machines) |
| Mechanical share of M07 downtime | ~62% |
| M07 share of all mechanical downtime | ~60% |
| M07 maintenance events | ~34 (highest in cost and activity) |
| M07 potential capacity at risk | ~906K units |

### Production performance
Actual production stayed below plan, leaving a gap of roughly 4 million units.

### M07 is the primary concern
M07 has the highest downtime in the dataset. Most of it is mechanical, and M07 alone accounts for about 60% of mechanical downtime across all machines.

The leading mechanical failure reasons for M07 are:

- Gearbox issue
- Belt failure
- Bearing failure

M07 also ranks highest in maintenance cost and maintenance activity. Combined with high downtime and recurring mechanical failures, this makes it the strongest candidate for a detailed reliability investigation.

### Potential capacity at risk
M07 has about **906K units** of potential production capacity at risk. This is calculated from downtime duration and the machine's standard production rate. It shows theoretical capacity affected by downtime, not confirmed loss.

### Shift-level observation
Downtime was higher in the Morning shift than in the Evening and Night shifts. This should not automatically be read as a staffing or performance problem. Before drawing conclusions, normalize downtime against:

- Production volume
- Operating hours
- Number of machines running
- Number of production events

---

## 💡 Business Recommendations

1. **Prioritize M07 reliability.** High downtime and maintenance burden justify immediate attention.
2. **Investigate recurring mechanical failures.** Run root-cause analysis on gearbox, belt, and bearing failures to find recurring patterns instead of repeatedly reacting to breakdowns.
3. **Strengthen preventive maintenance.** Check whether M07's maintenance intervals match its observed failure frequency.
4. **Track capacity at risk.** Use Potential Lost Production as an operational KPI to spot machines where downtime could significantly affect capacity.
5. **Investigate shift-level downtime.** Look further into Morning-shift downtime, using normalized comparisons.

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Microsoft Excel | Raw data storage and initial inspection |
| Power Query | Data cleaning and transformation |
| Power BI | Dashboard development and visualization |
| DAX | KPI calculations and analytical measures |
| GitHub | Version control and portfolio publishing |

---

## 📁 Repository Structure

```text
Manufacturing-Operations-Downtime-Intelligence/
│
├── Power BI/
│   └── Manufacturing_Operations_Dashboard.pbix
│
├── Raw data/
│   └── Manufacturing_Raw_Data.xlsx
│
├── Screenshots/
│   ├── executive-overview.png
│   ├── downtime-root-cause.png
│   └── machine-performance.png
│
└── README.md
```

---

## ▶️ How to Use

1. **Download or clone** this repository.
2. **Open** `Power BI/Manufacturing_Operations_Dashboard.pbix` in Power BI Desktop.
3. **Explore** the pages and slicers to analyze production, downtime, root causes, maintenance, and machine risk.

**Data refresh note:** The PBIX file was built from the included Excel dataset. If Power BI asks for the original file location when refreshing, update the Power Query source path to your downloaded `Raw data/Manufacturing_Raw_Data.xlsx`.

---

## 🎓 Skills Demonstrated

| Area | Skills |
|---|---|
| **Data preparation** | Data cleaning, Power Query, data transformation |
| **Modeling** | Data modeling, star-schema concepts, relationships |
| **Analytics** | DAX measures, KPI development, root-cause analysis |
| **Domain** | Manufacturing, downtime, maintenance, and capacity-risk analysis |
| **Communication** | Interactive dashboards, business recommendations, data storytelling |

---

## 📌 Project Outcome

The project turns raw manufacturing data into an interactive decision-support tool. It identifies **M07** as the primary operational risk, driven by high downtime, recurring mechanical failures, heavy maintenance activity, and substantial potential capacity at risk.

The dashboard guides management through one path:

**Production Performance → Downtime → Root Cause → Machine Risk → Business Action**

---

## 📄 License

Created for educational and portfolio purposes.

*Developed by Aakash Tiwari to demonstrate practical skills in Excel, Power Query, Power BI, DAX, and business intelligence.*
