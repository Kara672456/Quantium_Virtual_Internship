<div align="center">

# 🛒 Quantium — Chips Category Analytics

### Data Preparation · Customer Segmentation · Uplift Testing

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)](https://pandas.pydata.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)](https://jupyter.org)
[![SciPy](https://img.shields.io/badge/SciPy-8CAAE6?style=for-the-badge&logo=scipy&logoColor=white)](https://scipy.org)

> **Quantium Virtual Experience Program** · Retail Analytics · March 2026
>
> A comprehensive chips category review combining deep customer segmentation with rigorous A/B store trial evaluation — built to support Julia's upcoming category planning session.

---

</div>

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Task 1 — Data Preparation & Customer Analytics](#-task-1--data-preparation--customer-analytics)
- [Task 2 — Experimentation & Uplift Testing](#-task-2--experimentation--uplift-testing)
- [Task 3 — Client Presentation](#-task-3--client-presentation)
- [Key Results at a Glance](#-key-results-at-a-glance)
- [Tech Stack & Setup](#-tech-stack--setup)
- [Repository Structure](#-repository-structure)
- [How to Run](#-how-to-run)

---

## 🗂 Project Overview

This project is broken into three tasks, each building on the last:

| # | Task | Notebook / File | Focus |
|---|------|----------------|-------|
| 1 | Data Preparation & Customer Analytics | `Task_1_.ipynb` | Cleaning, segmentation, purchasing behaviour |
| 2 | Experimentation & Uplift Testing | `task_2.ipynb` | Control store matching, trial evaluation |
| 3 | Client Presentation | `Category_Review.pdf` | Quantium slide deck for category review |

---

## 📊 Task 1 — Data Preparation & Customer Analytics

### Objective
Analyse transaction and customer data to identify chip purchasing behaviours and generate commercial recommendations for an upcoming category review.

### Datasets

| File | Description |
|------|-------------|
| `QVI_transaction_data.xlsx` | Transaction-level records — product name, quantity, total sales, date, loyalty card number |
| `QVI_purchase_behaviour.csv` | Customer segments — maps loyalty card numbers to lifestage and premium tier (Budget / Mainstream / Premium) |

### Methodology

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐    ┌──────────────────┐
│  Data Cleaning  │───▶│Feature Engineering│───▶│Segment Analysis │───▶│   Significance   │
│                 │    │                  │    │                 │    │     Testing      │
│ • Remove dupes  │    │ • Extract pack   │    │ • Sales by      │    │ • Welch's t-test │
│ • Handle nulls  │    │   sizes (grams)  │    │   segment       │    │ • Mainstream vs  │
│ • IQR outliers  │    │ • Fix brand      │    │ • Purchase freq │    │   non-Mainstream │
│ • Verify dates  │    │   names          │    │ • Avg spend     │    │ • p ≈ 0 ✓        │
└─────────────────┘    └──────────────────┘    └─────────────────┘    └──────────────────┘
```

> 📅 **Note on Dec 25:** The single missing transaction date was Christmas Day — stores were closed. Not an anomaly.

### 🔍 Key Findings

<details>
<summary><strong>💰 Revenue Segments (click to expand)</strong></summary>

<br>

The **top 3 revenue-contributing segments** together account for **25% of total chip sales**:

| Rank | Segment | Total Sales | Driver |
|------|---------|------------|--------|
| 🥇 1st | Older Families — Budget | **$156,864** | Highest purchase frequency |
| 🥈 2nd | Young Singles/Couples — Mainstream | **$147,582** | Largest customer population |
| 🥉 3rd | Retirees — Mainstream | **$145,169** | Large customer population |

> Older Families reach #1 **not** through population size, but through exceptional purchase frequency — the highest of any segment.

</details>

<details>
<summary><strong>🧾 Purchasing Behaviour (click to expand)</strong></summary>

<br>

- **Older & Young Families** buy the greatest average quantity of chips per visit — prime targets for bundle/multi-pack promotions
- **Mainstream Young & Midage Singles/Couples** spend the **most per purchase**; the difference from Budget/Premium counterparts is statistically significant (`p ≈ 0`, Welch's t-test)
- **Older customers** buy more than younger customers overall
- **Non-premium** customers purchase more than premium customers

</details>

<details>
<summary><strong>🏷️ Brand & Pack Size Preferences (click to expand)</strong></summary>

<br>

| Preference | Winner | Notes |
|-----------|--------|-------|
| Top brand (all segments) | **Kettle** | Dominant across every lifestage |
| 2nd brand (most segments) | **Smiths** | Preferred by families, older segments, retirees |
| 2nd brand (Young/Midage Mainstream) | **Doritos** | Unique to this segment only |
| Top pack size | **175g** | Universal across all segments |
| 2nd pack size | **150g** | Universal across all segments |

> 🎄 **Seasonal insight:** Sales climb steadily toward Christmas. Pre-Christmas promotional campaigns can capitalise on this predictable demand spike.

</details>

### 💡 Commercial Recommendations

#### 1. Older Families (Budget) — *Frequency & Volume Promotions*
> Deploy loyalty-based offers that reward repeat visits and multi-pack purchases. This segment visits most frequently — small increases in basket size produce outsized revenue impact.

#### 2. Young Singles/Couples (Mainstream) — *Doritos Partnership Campaign*
> This is the **only** segment where Doritos beats Smiths as the second brand. A co-branded or targeted shelf-placement campaign with Doritos could capture incremental spend.

#### 3. Retirees (Mainstream) — *Maximise Reach & Frequency*
> Their large population drives total sales volume. Prioritise broad reach — catalogue features and weekly deals — to maintain high exposure across this loyal segment.

#### 4. All Segments — *Kettle + 175g as Hero SKU*
> Kettle is #1 and 175g is the top pack size across every segment. Feature this combination in end-of-aisle displays and pre-Christmas bundles to maximise category conversion.

---

## 🧪 Task 2 — Experimentation & Uplift Testing

### Objective
Evaluate a store layout trial in stores **77**, **86**, and **88** by selecting statistically comparable control stores and testing for significant uplift during the trial period (Feb–Apr 2019).

### Dataset

| File | Description |
|------|-------------|
| `QVI_data.csv` | Pre-processed monthly store metrics for control matching and trial assessment |

### Monthly Metrics Computed Per Store

- Total sales revenue
- Number of unique customers
- Average transactions per customer
- Average chips per customer
- Average price per unit

### Control Store Selection

Each trial store was matched to a control using a **composite similarity score** over the pre-trial period:

```python
composite_score = (
    0.5 × pearson_correlation   # directional similarity of metric trends
  + 0.5 × magnitude_distance   # 1 − normalised absolute difference
)
```

**Final trial → control pairings:**

| Trial Store | Control Store |
|:-----------:|:-------------:|
| 77 | 233 |
| 86 | 155 |
| 88 | 40 |

### Statistical Significance Framework

| Step | Test | Purpose | Pass Condition |
|------|------|---------|---------------|
| 1 | Welch's t-test (control: pre vs trial) | Confirm no internal drift in control store | p > 0.05 |
| 2 | Welch's t-test (trial vs control pre-trial) | Verify stores are similar before trial starts | p > 0.05 |
| 3 | T-score vs 95% CI threshold | Check if trial-month % diff exceeds pre-trial variation | \|t\| > critical value |

### 📈 Trial Results

#### ✅ Store 77 — *Significant Uplift*
> Total sales and number of customers **exceeded the 95% confidence threshold in all three trial months**. Strongest result of the three trial stores.

#### ✅ Store 86 — *Significant Uplift*
> Consistent uplift across all three trial months on both metrics. Closely mirrors Store 77's positive performance.

#### ⚠️ Store 88 — *No Significant Uplift*
> Sales increase was within expected variation bounds. Further investigation recommended — store-specific factors may explain the divergence.

### Summary

```
Store 77  ──────  Control 233  →  ✅ Significant uplift (sales + customers)
Store 86  ──────  Control 155  →  ✅ Significant uplift (sales + customers)
Store 88  ──────  Control 40   →  ⚠️  No significant uplift detected
```

> **Overall verdict:** The trial showed positive results for **2 out of 3 stores**. The format should be considered for broader rollout to stores similar to 77 and 86, pending investigation of Store 88.

---

## 📑 Task 3 — Client Presentation

The final deliverable is a confidential Quantium slide deck synthesising all findings for Julia's category review meeting, structured into two sections:

| Section | Content |
|---------|---------|
| **01 — Category Review** | December sales seasonality, segment sales breakdown, average purchase units per customer, brand and pack size preferences |
| **02 — Trial Store Performance** | Trial vs control comparisons for all 3 stores, 95% CI visualisations for total sales and customer numbers |

> 🔒 *Classification: Confidential — Quantium Group Pty Limited*

---

## 📌 Key Results at a Glance

| Metric | Value |
|--------|-------|
| Top revenue segment | Older Families (Budget) — **$156,864** |
| Top 3 segments combined | **25%** of total chip sales |
| Most purchased brand | **Kettle** (all segments) |
| Most purchased pack size | **175g** (all segments) |
| Statistically significant spending diff | Mainstream vs non-Mainstream Young/Midage Singles (`p ≈ 0`) |
| Trial stores with significant uplift | **2 / 3** (Stores 77 and 86) |

---

## 🛠 Tech Stack & Setup

| Library | Purpose |
|---------|---------|
| `pandas` | Data wrangling & aggregation |
| `numpy` | Numerical operations |
| `matplotlib` | Chart generation |
| `seaborn` | Statistical visualisations |
| `scipy.stats` | Hypothesis testing (t-tests, t-distribution) |
| `openpyxl` | Reading `.xlsx` transaction data |
| `datetime` | Date parsing and manipulation |

**Install all dependencies:**

```bash
pip install pandas numpy matplotlib seaborn scipy openpyxl jupyter
```

---

## 📁 Repository Structure

```
Quantium-Chips-Analytics/
│
├── Task_1_.ipynb              # Data preparation & customer analytics
├── task_2.ipynb               # Experimentation & uplift testing
├── Category_Review.pdf        # Client-facing presentation (Confidential)
├── README.md                  # This file
│
└── data/
    ├── QVI_transaction_data.xlsx
    ├── QVI_purchase_behaviour.csv
    └── QVI_data.csv           # Pre-processed input for Task 2
```

---

## ▶️ How to Run

**1. Clone the repository**
```bash
git clone https://github.com/your-username/quantium-chips-analytics.git
cd quantium-chips-analytics
```

**2. Install dependencies**
```bash
pip install pandas numpy matplotlib seaborn scipy openpyxl jupyter
```

**3. Add data files**

Place the three data files inside a `data/` folder, then update the file paths in the notebooks:

```python
# Task 1 — update these paths:
df = pd.read_excel('data/QVI_transaction_data.xlsx')
pur_bvr = pd.read_csv('data/QVI_purchase_behaviour.csv')

# Task 2 — update this path:
df = pd.read_csv('data/QVI_data.csv')
```


---

<div align="center">

**Quantium — Chips Category Review** · Retail Analytics · March 2026

*Classification: Confidential*

</div>
