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

This process is time-consuming and error-prone.

👉 This system automates and improves that decision-making.

---

## ⚙️ System Architecture

### 1. Data Preparation

- Merged 835 and 837 datasets
- Created unified claim-level dataset
- Generated synthetic dataset (30 claims)

---

### 2. Rule-Based Engine

Each claim is analyzed using CARC codes:

| CARC | Meaning           | Logic                            |
| ---- | ----------------- | -------------------------------- |
| 29   | Late Filing       | Compare service vs received date |
| 16   | Missing Info      | Check prior authorization        |
| 18   | Duplicate         | Compare similar claims           |
| 50   | Medical Necessity | Flag for review                  |

🔍 **Key Insight:**
The system does not blindly trust CARC codes — it validates them using logic.

---

### 3. Structured Output

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

## 📊 Pattern Analysis

### ❗ Issue Identified

Initial grouping using:

```
payer + procedure + CARC
```

resulted in:

```
denial_rate = 1.0 (always)
```

### 🔥 Fix Applied

Changed grouping to:

```
payer + procedure
```

### ✅ Final Formula

```
denial_rate = denied / total claims
```

---

### 💡 Interpretation

| Denial Rate | Meaning                           |
| ----------- | --------------------------------- |
| < 0.3       | Rare denial → Recoverable         |
| 0.3–0.7     | Uncertain → Needs Review          |
| > 0.7       | Frequent denial → Not Recoverable |

---

### 🚀 Impact

- Reduced "Needs Review"
- Added data-driven reasoning
- Improved decision quality

---

## 🔬 Clustering (Prioritization)

Used KMeans on:

- Adjustment Amount
- Denial Rate

### 🎯 Purpose

Group claims to prioritize recovery efforts.

---

### 📊 Cluster Insights

| Cluster Type               | Meaning                  |
| -------------------------- | ------------------------ |
| High denial rate           | Likely not recoverable   |
| Low value claims           | Low priority             |
| High value + medium denial | 🔥 Best recovery targets |

---

### 🔍 Key Insight

Clustering does not assign business meaning automatically.
We interpret clusters using domain logic.

---

## 📈 Evaluation

- Verified denial rate variation after fixing grouping issue
- Compared outputs before and after pattern analysis
- Validated decisions logically using sample claims

---

## 🔁 Iteration

Initial Issue:

- Denial rate always 1.0 due to incorrect grouping

Fix:

- Removed CARC from grouping

Impact:

- Enabled meaningful pattern insights
- Improved recoverability decisions

---

## ⚠️ Limitations

- Synthetic dataset (limited size)
- Medical necessity logic simplified
- Duplicate detection is heuristic-based
- No ML model used (can be added in future)

---

## 🚀 Future Improvements

- Add ML model for recoverability prediction
- Use NLP for diagnosis/procedure analysis
- Build dashboard (Power BI / Streamlit)
- Integrate real-world datasets

---

## 🧰 Tech Stack

- Python
- Pandas
- Scikit-learn (KMeans)
- Jupyter Notebook

---

## 🏁 Conclusion

This project demonstrates a hybrid system combining:

- Rule-based validation
- Pattern-based reasoning
- Cluster-based prioritization

👉 Designed for real-world healthcare claim processing.

---

## 👤 Author

Yashika Sharma
(Data Science / Analytics)
