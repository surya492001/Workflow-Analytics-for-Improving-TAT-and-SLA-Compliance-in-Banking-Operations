<img width="1020" height="561" alt="image" src="https://github.com/user-attachments/assets/75a932af-1639-4fdf-a6cb-3c06b68df11f" /># Workflow Analytics for Improving Turnaround Time and SLA Compliance in Banking Operations

> **MBA Capstone Project** | BITS Pilani – Business Analytics (CGPA: 9.0/10)  
> **Author:** Suryanarayan Satheesh Pillai | **ID:** 2024MB21248  


---

## Overview

Budget approval workflows in banking development environments suffer from multi-stakeholder dependencies, hierarchical authorisation structures, and recurring rework cycles that cause chronic processing delays and SLA non-compliance. Existing monitoring tools provide only aggregate-level reporting — offering no stage-level diagnostic capability or predictive intelligence for proactive governance.

This project addresses that gap through a structured, data-driven analytical study using **process mining, synthetic data generation, Decision Tree classification, and interactive dashboard development**.

---

## Key Results

| Metric | Value |
|--------|-------|
| Within SLA Rate | **84.99%** |
| SLA Violation Rate | **7.96%** |
| Average Processing Time | **72.16 days** |
| Decision Tree Accuracy | **97.5%** |
| Recall (SLA Violation class) | **100%** — zero missed breaches |
| F1 Score (Violation class) | **0.88** |
| Process Dispersion Index | **0.87** (high-variability spaghetti process) |

---

## Project Architecture

```
Workflow Analytics Pipeline
│
├── Phase 1 — Data Preparation
│   ├── Event log extraction & cleaning
│   └── Structuring into Case ID / Activity / Timestamp format
│
├── Phase 2 — Synthetic Data Augmentation
│   ├── SDV PARSynthesizer (CPAR model)
│   └── 15 real instances → 70 statistically consistent instances
│
├── Phase 3 — Descriptive Analytics
│   ├── Stage-level TAT computation
│   ├── SLA compliance analysis by category, sub-category, request type
│   ├── Bottleneck detection (Dispersion Index: 0.87)
│   └── Send-back frequency & workload distribution
│
├── Phase 4 — Predictive Analytics
│   ├── Decision Tree classifier (R / rpart)
│   ├── Inverse-frequency class weighting for imbalance handling
│   ├── Feature importance analysis
│   └── Shiny web app — SLA Violation Prediction System
│
└── Phase 5 — Dashboard & Deployment
    ├── Power BI interactive dashboard (3 views)
    └── Shiny R application for non-technical stakeholders
```

---

## Dataset

- **Source:** Anonymised budget approval event log from an IT banking development project
- **Audit Period:** Q1 2025 (January – March 2025)
- **Real Instances:** 15 process instances, 13 unique process variants
- **Augmented Dataset:** 70 instances (via SDV PARSynthesizer)
- **Total Records (post-augmentation):** 993

### Event Log Schema

| Column | Description |
|--------|-------------|
| `Request_ID` | Unique case identifier |
| `Request_Created_Date` | Workflow start timestamp |
| `Request_Type` | Multiyear Budget / Multiyear PO / Only for One FY |
| `Category` | Capex / Cloud Hosting / Revex |
| `Sub_Category` | AMC / Capex / Cloud Hosting / Licensing-Subscription / Professional |
| `StatusName` | Workflow stage at each event |
| `EventDateTime` | Timestamp of each stage transition |
| `Action_By` | Anonymised approver identifier |
| `TotalBudgetValue` | Total monetary value of the request |

---

## Technologies Used

| Tool | Purpose |
|------|---------|
| **Python** | Data preprocessing, event log structuring, synthetic data generation (SDV) |
| **R** | Decision Tree model (rpart), class weighting, evaluation, Shiny application |
| **Power BI** | Interactive workflow monitoring dashboard |
| **Microsoft Excel** | Initial data inspection and validation |
| **SDV PARSynthesizer** | Sequential synthetic event log generation (CPAR model) |

---

## Key Findings

### Bottleneck Analysis
- **Submitted to Functional Head** — primary bottleneck at ~18 days average delay
- **Submitted to TMAC Reviewer** — second bottleneck at ~11–12 days
- Workflow delay is highly concentrated at a small number of stages (not evenly distributed)

### SLA Compliance by Segment

| Segment | Finding |
|---------|---------|
| **AMC** (sub-category) | Best performer — zero violations throughout the study period |
| **Cloud Hosting** | Highest violation rate; weakest SLA compliance |
| **Only for One FY** | Most problematic request type; highest breach concentration |
| **Multiyear PO** | Best-performing request type |
| **Capex** | Stable and predictable performance |

### Decision Tree — High-Risk Profile
Requests most likely to breach SLA share this combination:
- Approval Level ≥ 2
- Request Type: Only for One FY or Multiyear Budget
- Category: Cloud Hosting or Revex
- Total Budget Value < ₹17 million

### Feature Importance

| Feature | Importance Score |
|---------|-----------------|
| Total Budget Value | 979.8 |
| Level Number | 654.8 |
| Status Name | 516.3 |
| Request Type | 323.0 |
| Sub-Category | 125.9 |
| Category | 59.2 |

---

## Model Performance

```
Confusion Matrix (Test Set — 80/20 split):

                 Predicted
                 Not Applicable  Violation  Within SLA
Actual  Not Appl       16             0           0
        Violation        0            19           5
        Within SLA       0             0         159

Accuracy:  97.49%
Kappa:     0.9223
Recall (Violation): 100% — no actual breach missed
F1 (Violation):     0.88
```

Class imbalance handled via **inverse-frequency weighting**:
- Violation class weight: 10.0
- Not Applicable weight: 11.0
- Within SLA weight: 1.0

---

## Power BI Dashboard

Three interconnected views:

1. **SLA Monitoring Overview** — KPI cards (84.99% within SLA, 7.96% violation, 72.16 day avg TAT), trend line, stage-level TAT bar chart, SLA vs Approval Level breakdown
2. **Request Type & Budget Value Analysis** — SLA compliance by request type and budget band (<17M, 17–36M, 36–220M, >220M)
3. **Decomposition Tree** — interactive drill-down: Violation % → Level Group → Request Type → Category → Budget Range

<img width="1020" height="561" alt="image" src="https://github.com/user-attachments/assets/7e4c928a-5272-4a42-b350-9edae17e3a0b" />


---

## Shiny Web Application — SLA Violation Prediction System

An interactive R Shiny application that enables non-technical stakeholders to obtain real-time SLA predictions by entering workflow parameters:

**Inputs:** Level Number, Request Type, Category, Sub-Category, Total Budget Value  
**Output:** Predicted SLA Status (Within SLA / Violation / Not Applicable) with full Decision Tree visualisation and highlighted classification path

The application is locally deployable via R Shiny and publishable to a web server for organisation-wide access.

---

## Recommendations

Based on analytical findings, five targeted interventions are proposed:

1. **Functional Head bottleneck** — implement queue monitoring, workload balancing, and escalation triggers at the 18-day primary bottleneck stage
2. **TMAC Reviewer capacity** — structured review scheduling and capacity planning to prevent queue accumulation
3. **Only for One FY fast-track** — dedicated pre-submission checklist and expedited pathway for the highest-risk request type
4. **Send-back reduction** — submission quality controls at Unit Head level to reduce rework cycles before requests enter the formal approval chain
5. **Shiny prediction integration** — embed the SLA Violation Prediction System at the request submission interface for automated early-warning flagging

---

## References

- Van der Aalst, W.M.P. *Process Mining: Data Science in Action*. Springer.
- Lashkevich et al. (2024). Unveiling the causes of waiting time in business processes from event logs. *Information Systems*, 126, 102434.
- Singh, A., Bettouche, Z., & Fischer, A. (2024). Synthetic training-data generation for ML-based process mining tools. *ICPM 2024*. IEEE.
- Márquez-Chamorro et al. (2017). Run-time prediction of business process indicators using evolutionary decision rules. *Expert Systems With Applications*, 87, 1–14.
- Camargo et al. (2025). Enhancing predictive process monitoring on small-scale event logs using large language models. *LNCS*. Springer.
- Marin-Castro et al. (2024). A novel trace-based sampling method for conformance checking. *PeerJ Computer Science*, 10, e2601.
- Tax et al. (2025). Process discovery for event logs with multi-occurrence event types. *Algorithms*, 18(2), 83.

---
## License

This project is for academic and portfolio purposes. The dataset used is fully anonymised and contains no personally identifiable information or confidential organisational records.
