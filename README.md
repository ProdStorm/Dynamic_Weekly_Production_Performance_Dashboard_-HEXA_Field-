# Dynamic Weekly Production Performance Analysis (HEXA_Field)
**Toolstack:** Microsoft Excel (Power Query, Pivot Tables & Dynamic Visuals) 

**Industry:** Oil & Gas / Upstream Petroleum Production Analytics

## Executive Summary & Business Problem
Upstream petroleum production monitoring requires rapid identification of volume variances, deferment drivers, and production trends across hydrocarbon phases (Oil/Condensate, Gas, and Water).\
This project provides an automated executive dashboard tracking weekly performance for the HEXA field. It aggregates raw daily field reports into weekly performance metrics, tracks actual volumes against field targets (2,116 bopd oil target and 38.70 MMscfd gas target), compares current performance against previous week baselines, and categorizes operational deferments.


## Data Pipeline & ETL (Power Query Workflows)
Raw production logs spanning multiple months were ingested and transformed using Excel’s Power Query engine to build a relational data model.

### Transformations Applied
1.	**Source Connection & Data Appending:**
- Ingested 5 individual monthly raw reports (CSV/Excel)
- Built an appended master query combining all monthly logs into a continuous time-series dataset.
- Cleaned metadata headers and standardizing table boundaries across queries.
 
<img width="730" height="410" alt="image" src="https://github.com/user-attachments/assets/9c7df6ea-4807-4893-9350-a57b0822154f" />
<img width="722" height="488" alt="image" src="https://github.com/user-attachments/assets/6c991064-46a5-478d-a20d-490502350862" />


2.	**Data Model Staging (Aggregate Table):**

- Created a dedicated downstream table (Aggregate) referencing the appended query to isolate necessary analytical fields while preserving raw audit logs.
- Enforced data types (Date, Decimal Number, Text).
- Handled missing data by replacing NULL or blank values with 0 for statistical integrity.

<img width="975" height="416" alt="image" src="https://github.com/user-attachments/assets/9177789e-460c-44d9-81d0-cd2927304f26" />

 
## Data Modeling & Dynamic Calculations
To enable seamless week-on-week (WoW) rolling analyses based on user input, dynamic formulas were implemented in the core metrics model.

**Rolling 7-Day Volumetric Sum (Core Logic)**
To aggregate weekly totals dynamically based on the date selector cell (Dashboard!$U$3), the following anchor formula was used:
```
=SUM(OFFSET(INDEX(Aggregate[Net Ullage (bopd)], MATCH(Dashboard!$U$3, Aggregate[Date], 0)), 0, 0, 7, 1))
```

**Step-by-Step Logic Breakdown**
1.	MATCH(Dashboard!$U$3, Aggregate[Date], 0): Locates the exact row index of the anchor date chosen in cell Dashboard!U3.
2.	INDEX(Aggregate[Net Ullage (bopd)], ...): Returns a reference to the cell at that row position within the Net Ullage (bopd) column.
3.	OFFSET(..., 0, 0, 7, 1): Expands the reference into a continuous range spanning 7 rows down and 1 column wide starting from the anchor date.
4.	SUM(...): Aggregates the 7 daily volumetric outputs into a single weekly total.

**Previous Week Baseline:** Computed by shifting the anchor date evaluation back by 7 days (Dashboard!$U$3 - 7), enabling real-time Week-on-Week percentage variance tracking.

<img width="975" height="419" alt="image" src="https://github.com/user-attachments/assets/06f86e75-c56c-4657-8727-d21e25e20ab3" />

 
## Key Operational Insights (EDA)
•	Oil Production vs. Technical Target:

- Average daily oil production reached 1,404.46 STB/d (+4.7% WoW), operating below the field target of 2,116.00 bopd.
- Peak oil production was logged on April 29, 2026 (1,739.06 STB).

•	Gas Production vs. Technical Target:
- Average gas rate reached 39.14 MMScf/d (-0.8% WoW), outperforming the technical baseline target of 38.70 MMScfd on 5 out of 7 days.
- A volume drop occurred on May 3, 2026 (36.00 MMScf) due to operational throttling.

•	Operational Deferment Breakdown:
- Oil Deferment Split: 47% Planned (1,835.19 bpd) vs. 53% Unplanned (2,097.56 bpd).
- Primary Root Cause: Unplanned deferment was driven by the HEXA 4STSS shut-in due to Facility constraints.

## User Interface & Interactive Dashboard Mechanics
The dashboard was engineered for executive readability and interactivity:

<img width="975" height="390" alt="image" src="https://github.com/user-attachments/assets/6c69fd05-e43e-4571-bd55-f08f3c908f83" />
 
**•	Dynamic Date Selection:** Selecting a start date in cell Dashboard!U3 automatically updates all chart axes, KPI summaries, target reference lines, and WoW percentage variances across a 7-day window.

**•	Phase-Specific Color Systems:**
- 🔴 Red Containers: Oil & Condensate statistics & daily trend tracking
- 🟢 Green Containers: Natural gas statistics & baseline performance tracking.
- 🔵 Blue Containers: Produced water volumes & disposal tracking.
- 🔘 Slate Containers: Offtake shipment, truckout, and loss metrics.

**•	Target Line Overlays:** Integrated horizontal benchmark lines on time-series column charts to immediately highlight daily target deficits or over-performances.
