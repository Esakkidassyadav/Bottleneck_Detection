# Bottleneck_Detection

# End-to-End Bottleneck Detection in Supply Chain Systems
### Process Mining · Machine Learning · Discrete-Event Simulation

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![AnyLogic](https://img.shields.io/badge/Simulation-AnyLogic-orange)
![PM4Py](https://img.shields.io/badge/Process%20Mining-PM4Py-green)
![Scikit-learn](https://img.shields.io/badge/ML-Scikit--learn-F7931E?logo=scikit-learn&logoColor=white)
![XGBoost](https://img.shields.io/badge/ML-XGBoost-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

##  Project Overview

This project builds a **three layer end-to-end pipeline** for early-stage bottleneck prediction in supply chain operations before route assignment is even made. The system combines discrete-event simulation, process mining, and classical ML to enable proactive decision-making in logistics workflows.

**Core question:** *Can we predict supply chain bottlenecks at early process stages, using only upstream event data, before the bottleneck actually occurs downstream?*

---

##  System Architecture

```
┌─────────────────────────────────────────────────────┐
│           LAYER 1 — Simulation (AnyLogic)            │
│  Discrete-event model · 50,000 events · 5,000 cases │
│  Timestamps · Activities · Routing · Agent tracking  │
└────────────────────┬────────────────────────────────┘
                     │  Event Log (CSV)
┌────────────────────▼────────────────────────────────┐
│         LAYER 2 — Process Mining (PM4Py)             │
│  Inductive Miner · Petri Net · 100% Trace Fitness   │
│  Cycle time · Waiting time · Process variant analysis│
└────────────────────┬────────────────────────────────┘
                     │  Engineered Features (20+)
┌────────────────────▼────────────────────────────────┐
│        LAYER 3 — ML Classification (Scikit-learn)    │
│  Logistic Regression · Random Forest · XGBoost      │
│  Class imbalance handling · Recall-optimized         │
└─────────────────────────────────────────────────────┘
```

---

##  Layer 1 — AnyLogic Simulation

![AnyLogic Model](Anylogic%20Simulation%20Model.png)

- Modeled a multi-stage e-commerce supply chain with order processing, warehouse handling, and route dispatch agents
- Generated **~50,000 event log records** across **~5,000 order cases**
- Each record captures: `case_id`, `activity`, `timestamp`, `resource`, `route`, and lifecycle markers


---

##  Layer 2 — Process Mining

**Tool:** `PM4Py`

| Step | Method | Result |
|------|--------|--------|
| Process Discovery | Inductive Miner | Petri Net model |
| Conformance Checking | Token Replay | **100% Trace Fitness** |
| Performance Analysis | Cycle Time / Waiting Time | Stage-level bottleneck labeling |
| Variant Analysis | Frequency-based | Bottleneck-prone path identification |

- Waiting time and cycle time at each activity stage used to define the **bottleneck label** (binary: 1 = bottleneck case, 0 = normal)
- Bottleneck class constitutes **~20% of cases** (class imbalance addressed in Layer 3)

---

##  Layer 3 — ML Pipeline

### Feature Engineering (20+ features)
- **Early-stage delays:** Processing time and wait time at upstream stages (pre-route)
- **Temporal patterns:** Hour-of-day, day-of-week, inter-event gaps
- **Stage-level waiting times:** Normalized per activity
- **Aggregated case features:** Total duration up to prediction point, cumulative delay ratio

> Features were engineered to reflect only **early-stage information**, simulating a real-time monitoring scenario where the bottleneck has not yet occurred.

### Models Compared

| Model | Accuracy | ROC-AUC | Recall (Bottleneck) |
|-------|----------|---------|----------------------|
| Logistic Regression | ~71% | ~0.58 | ~52% |
| **Random Forest** | **77%** | **0.64** | **65%** |
| XGBoost | ~75% | ~0.62 | ~60% |

### Why Recall over Accuracy?

> In a real-time supply chain monitoring context, **missing a bottleneck (False Negative) carries higher business cost** than raising a false alarm (False Positive). A missed bottleneck can cascade into SLA breaches, inventory shortfalls, and customer churn. An unnecessary alert costs only human review time.

This operational logic drove the choice to **prioritize recall** as the primary evaluation metric and use `class_weight='balanced'` across all models.

---

##  Repository Structure

```
Bottleneck_Detection/
│
├── Raw_data/                      # AnyLogic-generated event logs (CSV)
│
├── Notebooks/                     # Analysis and modeling notebooks
│   ├── 01_EDA_and_ProcessMining.ipynb
│   ├── 02_FeatureEngineering.ipynb
│   └── 03_ML_Modeling.ipynb
│
├── Anylogic Simulation Model.png  # Visual of the AnyLogic simulation setup
└── README.md
```

---

##  Getting Started

### Prerequisites

```bash
pip install pandas numpy scikit-learn xgboost pm4py matplotlib seaborn
```

### Run the Pipeline

```bash
# Step 1 — Explore event log and process mining
jupyter notebook Notebooks/01_EDA_and_ProcessMining.ipynb

# Step 2 — Feature engineering
jupyter notebook Notebooks/02_FeatureEngineering.ipynb

# Step 3 — Model training and evaluation
jupyter notebook Notebooks/03_ML_Modeling.ipynb
```

---

##  Key Results

-  **Random Forest** selected as the best model: 77% accuracy, 0.64 ROC-AUC, **65% recall**
-  **100% trace fitness** on Petri net model  simulation produces process-conformant logs
-  Early-stage prediction enabled: bottleneck flag raised **before route assignment**
-  ROC-AUC of 0.64 reflects the inherent difficulty of early-stage prediction with limited downstream information an intentional and realistic constraint

---

##  Business Impact

| Capability | Operational Benefit |
|---|---|
| Pre-route bottleneck flagging | Enables proactive rerouting and resource reallocation |
| Recall-optimized alerting | Minimizes missed bottleneck events in live monitoring |
| Process conformance (100% fitness) | Validates simulation realism for synthetic data use |
| Stage-level waiting time features | Pinpoints which activity stages drive prediction signal |

---

##  Tech Stack

| Category | Tools |
|---|---|
| Simulation | AnyLogic (Discrete-Event) |
| Process Mining | PM4Py, Inductive Miner |
| Data Processing | Python, Pandas, NumPy |
| ML Models | Scikit-learn, XGBoost |
| Visualization | Matplotlib, Seaborn |
| Environment | Jupyter Notebook |

---

