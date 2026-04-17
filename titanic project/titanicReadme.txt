
# 🚢 Titanic Survival Prediction – Machine Learning Project

## 📌 Project Overview

This project focuses on predicting whether a passenger survived the Titanic disaster using Machine Learning. The goal is to build an end-to-end ML pipeline including data cleaning, exploratory data analysis, feature engineering, model training, and evaluation.

This project was developed as part of my learning journey in Data Science and Machine Learning.

---

## 🎯 Problem Statement

Given passenger information such as age, gender, ticket class, fare, and family details, the objective is to predict:

```
Survived → 1 (Yes) or 0 (No)
```

This is a binary classification problem.

---

## 📂 Dataset

Source: Kaggle – Titanic: Machine Learning from Disaster

Files used:

* train.csv (891 rows) → for training and validation
* test.csv (418 rows) → for final prediction (not used for training)

Target Variable:

* Survived

---

## 🛠️ Tools & Libraries Used

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* Scikit-learn

---

## 🔄 Project Workflow

### 1️⃣ Data Understanding

* Studied column meanings and business context
* Identified target variable
* Understood relationships between demographic and survival

---

### 2️⃣ Data Cleaning

Handled missing values:

| Column   | Method Used                      |
| -------- | -------------------------------- |
| Age      | Group-wise median (Sex + Pclass) |
| Fare     | Median                           |
| Embarked | Mode                             |
| Cabin    | Converted to binary (has_cabin)  |

Removed:

* PassengerId
* Name (after feature extraction)
* Ticket

Fixed data types and ensured no null values remained.

---

Perfect, Yashu 👍 — this is exactly what will make your README and interview answers **stand out**.

Most people just write:

> “Handled missing values.”

You want to explain **how and why**.

That’s professional.

Below is a detailed, polished section you can directly add to your README under **Data Cleaning & Feature Engineering**.

---

## 🔍 Handling Missing Values & Text Features (Detailed Explanation)

One of the most critical parts of this project was handling missing and inconsistent data. Instead of applying generic filling methods, domain logic and exploratory analysis were used to make informed decisions.

---

### ✅ 1️⃣ Handling Missing Values in `Fare`

#### 🔹 Problem:

The `Fare` column contained very few missing values and showed a highly skewed distribution, with some passengers paying extremely high prices.

#### 🔹 Analysis:

* Fare values depended on passenger class.
* Higher-class passengers paid significantly more.
* Outliers distorted the mean.

#### 🔹 Solution:

Used **median imputation** instead of mean.

```python
df['Fare'].fillna(df['Fare'].median(), inplace=True)
```

#### 🔹 Reason:

Median is robust to outliers and represents typical ticket prices better than mean.

---

### ✅ 2️⃣ Handling Missing Values in `Age`

#### 🔹 Problem:

About 20% of `Age` values were missing, making simple deletion impractical.

#### 🔹 Analysis:

EDA showed that age varied significantly across:

* Passenger Class
* Gender

For example:

* 1st class passengers were generally older
* 3rd class passengers were younger
* Females and children had different distributions

#### 🔹 Solution:

Applied **group-wise median imputation** based on `Sex` and `Pclass`.

```python
df['Age'] = df.groupby(['Sex','Pclass'])['Age']\
              .transform(lambda x: x.fillna(x.median()))
```

#### 🔹 Reason:

This preserves realistic age patterns and avoids bias introduced by global imputation.

---

### ✅ 3️⃣ Handling Missing Values in `Embarked`

#### 🔹 Problem:

The `Embarked` column had a very small number of missing values.

#### 🔹 Analysis:

EDA showed that embarkation port correlated with:

* Fare
* Passenger class

Cherbourg (C) passengers typically paid higher fares.

#### 🔹 Solution:

Filled missing values using the most frequent category (mode).

```python
df['Embarked'].fillna(df['Embarked'].mode()[0], inplace=True)
```

Additionally, verified missing rows to ensure consistency with fare and class patterns.

#### 🔹 Reason:

Mode preserves the original categorical distribution and avoids introducing bias.

---

### ✅ 4️⃣ Handling Missing Values in `Cabin`

#### 🔹 Problem:

More than 80% of `Cabin` values were missing, making direct imputation unreliable.

#### 🔹 Analysis:

Cabin information reflected passenger deck level and indirectly social class, which affected survival probability.

However, excessive missing values reduced its usability.

#### 🔹 Solution:

Converted `Cabin` into a binary indicator:

```python
df['has_cabin'] = df['Cabin'].notnull().astype(int)
df.drop(columns=['Cabin'], inplace=True)
```

#### 🔹 Reason:

This preserved useful information (whether cabin info exists) while avoiding sparsity and noise.

---

### ✅ 5️⃣ Handling the `Name` Column (Text Feature Engineering)

#### 🔹 Problem:

The `Name` column contained unstructured text and could not be directly used for modeling.

Example:

```
Braund, Mr. Owen Harris
```

#### 🔹 Analysis:

Names contained honorific titles (Mr, Mrs, Miss, Master, etc.) that indicated:

* Gender
* Age group
* Social status

All of which influenced survival.

#### 🔹 Solution:

##### Step 1: Extract Titles Using Regular Expression

```python
df['Title'] = df['Name'].str.extract(' ([A-Za-z]+)\.', expand=False)
```

##### Step 2: Group Rare Titles

```python
df['Title'] = df['Title'].replace(
    ['Dr','Rev','Col','Major','Lady','Countess','Sir','Don','Capt','Jonkheer'],
    'Rare'
)
```

##### Step 3: One-Hot Encode Titles

```python
df = pd.get_dummies(df, columns=['Title'], drop_first=True)
```

##### Step 4: Remove Original Name Column

```python
df.drop(columns=['Name'], inplace=True)
```

#### 🔹 Reason:

This transformed unstructured text into meaningful categorical features while reducing noise.

---

## 🧠 Why This Approach Was Important

Instead of applying generic imputation methods, each column was handled based on:

* Percentage of missing values
* Data distribution
* Domain relevance
* Correlation with target variable

This ensured:

✔ Minimal information loss
✔ Reduced bias
✔ Better generalization
✔ Improved model performance

---


> How did I handle missing values and text data?

You can say:

> "I applied feature-specific imputation strategies. Age was filled using group-wise median based on gender and class, fare using median due to skewness, embarked using mode, and cabin was converted into a binary indicator. I also extracted titles from names using regex and encoded them as categorical features."

---

### 3️⃣ Feature Engineering

Created new useful features:

#### 🔹 Cabin Feature

```python
has_cabin = 1 if cabin exists else 0
```

#### 🔹 Title Extraction from Name

Extracted titles using regex:

* Mr, Mrs, Miss, Master, Rare

Grouped rare titles into one category.

#### 🔹 One-Hot Encoding

Applied to:

* Title
* Embarked

Converted categorical variables into numeric format.

#### 🔹 Gender Encoding

* Male → 0
* Female → 1

---

### 4️⃣ Exploratory Data Analysis (EDA)

Used Matplotlib and Seaborn for visualization.

Main analyses:

* Survival vs Gender
* Survival vs Passenger Class
* Survival vs Age
* Survival vs Fare
* Survival vs Age + Gender
* Survival vs Class + Gender

Key insights:

* Females had much higher survival rate
* 1st class passengers survived more
* Children had better survival chances
* Fare positively correlated with survival
* Strong interaction between Sex and Pclass

Created Age Groups using `pd.cut()` for better interpretation.

---

### 5️⃣ Major Challenges Faced & Fixes

#### ❌ 1. Training on Wrong Dataset

Initially used 418-row dataset (test set).

Result:

* Got 100% accuracy (data leakage)

Fix:

* Switched to 891-row train dataset
* Rebuilt entire pipeline

---

#### ❌ 2. Data Leakage During Scaling

Initially scaled full dataset before splitting.

Fix:

* Applied scaling after train-test split
* Fit only on training data

---

#### ❌ 3. SettingWithCopyWarning

Got pandas warning while creating new columns.

Fix:

* Used `.loc` and `.copy()`

---

#### ❌ 4. Misinterpreting Plots

Initially confused when some categories were missing.

Fix:

* Used groupby + count plots
* Understood class imbalance and survival distribution

---

#### ❌ 5. Confusion About Feature Importance

Did not know which features were useful.

Fix:

* Used EDA + Logistic Regression coefficients
* Related model output with visual insights

---

### 6️⃣ Data Preprocessing for Modeling

Steps:

1. Removed irrelevant columns
2. Ensured all features were numeric
3. Standardized features using StandardScaler
4. Performed stratified train-test split

---

### 7️⃣ Model Building

Used Logistic Regression as baseline model.

Why Logistic Regression?

* Interpretable
* Works well for binary classification
* Preferred in finance and regulated industries

Training steps:

* Train-test split (80/20)
* Feature scaling
* Model fitting
* Evaluation

---

### 8️⃣ Model Evaluation

Final Performance:

```
Accuracy: ~84%
```

Confusion Matrix:

* True Positives: 60
* True Negatives: 90
* False Positives: 15
* False Negatives: 14

Classification Report:

* Precision (Survived): ~0.80
* Recall (Survived): ~0.81
* F1-score: ~0.81

This is a strong and realistic result for Titanic dataset.

---

## 📈 Results & Insights

* Gender is the strongest predictor
* Passenger class significantly affects survival
* Title and cabin availability improved performance
* Feature engineering was crucial
* Proper preprocessing avoided overfitting

---

## 📚 Key Learnings

Through this project, I learned:

### ✅ Technical Skills

* Data cleaning and imputation
* Feature engineering
* Encoding categorical variables
* Scaling features
* Avoiding data leakage
* Model evaluation

### ✅ Analytical Skills

* How to think before modeling
* How to use EDA for decisions
* How to validate assumptions
* How to debug ML pipelines

### ✅ Professional Skills

* Writing explainable ML solutions
* Understanding business context
* Building end-to-end pipelines
* Presenting results clearly

---

## 🚀 Future Improvements

* Hyperparameter tuning (GridSearchCV)
* Try Random Forest / XGBoost
* Use cross-validation
* Feature importance with SHAP
* Build ensemble models

---

## 📌 Conclusion

This project represents my complete learning journey from raw data to a validated machine learning model. It helped me understand not only how to build models, but how to think like a data scientist.

It strengthened my foundation in:

* Data preprocessing
* Visualization
* Feature engineering
* Model evaluation
* Debugging ML pipelines

---

## 👩‍💻 Author

Yashika 
MCA | Aspiring Data Scientist
Skills: Python, Pandas, ML, Data Analysis

---

