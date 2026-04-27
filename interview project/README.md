# 🏥 Healthcare Claims Recovery Analysis System

## 📌 Overview

This project builds an intelligent system to analyze healthcare insurance claims and determine whether denied claims are **recoverable, not recoverable, or need review**.

The system combines:

- Rule-based logic (business rules)
- Pattern analysis (historical trends)
- Clustering (prioritization)

---

## 🎯 Problem Statement

Hospitals submit claims (837) to insurance companies, which respond with payment/denial details (835).

The billing team manually:

- Reviews denied claims
- Identifies root causes
- Decides whether to appeal

This process is:

- Time-consuming
- Error-prone
- Inefficient

👉 This project automates that decision-making process.

---

## 📂 Dataset

### 🔹 837 (Hospital side)

Contains:

- Claim details
- Diagnosis
- Procedure
- Prior authorization

### 🔹 835 (Insurance side)

Contains:

- Claim status (paid/denied)
- CARC codes (denial reasons)
- Adjustment amount

---

## ⚙️ System Architecture

### 1️⃣ Data Preparation

- Merged 837 and 835 datasets
- Created unified claim-level dataset
- Handled missing values and formatting issues

---

### 2️⃣ Rule-Based Engine (Core Logic)

Each claim is analyzed based on CARC codes:

| CARC | Meaning           | Logic                            |
| ---- | ----------------- | -------------------------------- |
| 29   | Late Filing       | Compare service vs received date |
| 16   | Missing Info      | Check prior authorization        |
| 18   | Duplicate         | Compare similar claims           |
| 50   | Medical Necessity | Flag for review                  |

---

### 🔍 Key Insight

> The system does NOT blindly trust CARC codes — it validates them.

Example:

- If claim is within filing limit but marked late → **Recoverable**

---

### 3️⃣ Structured Output

Each claim produces:

```json
{
  "claim_id": "...",
  "root_cause": "...",
  "recoverability": "...",
  "is_denial_valid": true/false/null,
  "evidence": {...},
  "confidence": 0.9
}
```

---

## 📊 Pattern Analysis (Day 2)

### ❗ Initial Issue Discovered

Grouping by:

```
payer + procedure + CARC
```

resulted in:

```
denial_rate = 1.0 (always)
```

### 🔥 Fix

Removed CARC from grouping:

```python
groupby(['cp_PayerName', 'cd_ProcedureCode'])
```

### ✅ Final Denial Rate

```
denial_rate = denied / total claims
```

---

### 💡 How Patterns Help

| Denial Rate | Interpretation                   |
| ----------- | -------------------------------- |
| < 0.3       | Rare denial → likely recoverable |
| 0.3–0.7     | Uncertain → review               |
| > 0.7       | Common denial → likely valid     |

---

### 🚀 Impact

- Reduced "Needs Review"
- Added data-driven decisions
- Improved system intelligence

---

## 🔬 Clustering (Day 3)

Used KMeans on:

- Adjustment Amount
- Denial Rate

### 📌 Purpose

Group claims for prioritization

---

### 📊 Cluster Insights

| Cluster                     | Meaning                  |
| --------------------------- | ------------------------ |
| High denial + medium amount | Likely not recoverable   |
| Low amount + low denial     | Low priority             |
| High amount + medium denial | 🔥 Best recovery targets |

---

### ⚠️ Important Learning

> Clustering does not give business meaning — we must interpret it.

---

### ✅ Final Labeling

```python
High Value + Medium Risk → Focus
High Risk → Avoid
Low Value → Low Priority
```

---

## 🤖 Why Not Pure ML?

### ❌ Not used initially because:

- Small dataset
- Need for explainability
- Strong rule-based nature

### ✅ Future Extension:

- Predict recoverability using ML
- Add NLP for diagnosis/procedure
- Improve confidence scoring

---

## 🧠 Key Learnings

- Never trust labels blindly (CARC validation)
- Avoid over-granular grouping in patterns
- Separate logic, scoring, and output
- Combine rules + patterns for best results
- Clustering needs interpretation

---

## 🚀 Business Value

- Automates claim review
- Reduces manual effort
- Improves recovery rate
- Prioritizes high-value claims

---

## 📌 Tech Stack

- Python
- Pandas
- Scikit-learn (KMeans)
- Jupyter Notebook

---

## 🏁 Conclusion

This project demonstrates a **hybrid decision system** combining:

- Rule-based validation
- Data-driven insights
- Intelligent prioritization

👉 Designed for real-world healthcare claim processing scenarios.

---

## 📎 Future Improvements

- Add ML prediction model
- Integrate real claim datasets
- Build dashboard (Power BI / Streamlit)
- Add API layer

---

## 👤 Author

Yashika Sharma
(Data Science / Analytics)
