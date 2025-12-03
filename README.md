# RCA_Tools_And_Methods
Capstone Project for Tools &amp; Methods

# Income Inequality, Homicide & Suicide: A Multi-Source Data Integration Project

Capstone project for the course **Tools and Methods: Data Analysis**  
Team: *[Add Team Name / Members]*  

---

## 1. Project Overview

The goal of this capstone project is to perform a **complete, end-to-end data analysis** that goes beyond a single source of truth. We combine **income inequality data** (Gini index) with **homicide** and **suicide** rates from independent sources to uncover relationships that would not be visible in any one dataset alone.

Our work follows the full data analysis lifecycle:

- Problem definition & data acquisition  
- Data integration, wrangling & cleaning  
- Exploratory data analysis (EDA) & feature engineering  
- Modeling (with scikit-learn)  
- Results & interpretation  
- Conclusion, limitations & future work  

The final result is a **reproducible Jupyter Notebook**, a **documented Git repository**, and a **presentation** summarizing our integration strategy and key findings.

---

## 2. Central Question & Research Hypotheses

### Central Question

> **To what extent does income inequality (measured by the Gini index) explain cross-country variation in homicide and suicide death rates, and how does this relationship evolve over time?**

### Sub-Questions

1. **Cross-sectional perspective:**  
   In a given year, are more unequal countries (higher Gini) associated with higher homicide and suicide rates?

2. **Temporal perspective:**  
   When inequality rises within a country over time, do homicide and/or suicide rates tend to rise as well?

### Hypotheses

- **H1 – Homicide:**  
  Countries with a **higher Gini index** tend to have **higher homicide rates** (per 100,000 population).

- **H2 – Suicide:**  
  Countries with a **higher Gini index** tend to have **higher suicide death rates**, although this relationship may be weaker and more heterogeneous than for homicide.

- **H3 – Combined violent/self-inflicted deaths:**  
  A combined measure of **“violent death rate”** (homicide + suicide per 100k) is positively associated with income inequality and displays **non-linear patterns** that require more flexible models than simple linear regression.

---

## 3. Data Sources

We intentionally use **three separate datasets** from different sources to simulate a realistic multi-source integration scenario.

1. **Income Inequality – Gini Index per Country**  
   - **Source:** Kaggle  
   - **Dataset:** *Gini Index per Country*  
   - **URL:** https://www.kaggle.com/datasets/ulrikthygepedersen/gini-index-per-country  
   - **Description:**  
     Country-level Gini index values (measure of income inequality) across multiple years.

2. **Homicide Rates**  
   - **Source:** Our World in Data / UNODC  
   - **Dataset:** *homicides-unodc*  
   - **URL:** https://ourworldindata.org/grapher/homicides-unodc  
   - **Description:**  
     Estimated homicide rates per 100,000 population, per country and year.

3. **Suicide Rates**  
   - **Source:** Our World in Data  
   - **Dataset:** *Suicide* (death rates from suicide)  
   - **URL:** https://ourworldindata.org/suicide  
   - **Description:**  
     Suicide death rates per 100,000 population, per country and year.

---

## 4. Data Integration Strategy

### 4.1 Common Key

The **core integration strategy** is to build a **panel dataset** (country_code × year) that combines:

- Gini index (income inequality)  
- Homicide rate per 100,000 people  
- Suicide rate per 100,000 people  

We link the datasets through:

- **Country identifier** (country code)  
- **Year**  

### 4.2 Integration Steps

1. **Standardize identifiers**
   - Harmonize country names (country_code) across all three datasets.
   - Remove aggregate entities such as “World”, “High income”, etc.

2. **Align time coverage**
   - Restrict analysis to years where **all three datasets** have overlapping data (e.g. a range such as 2000–2021).

3. **Merge operations**
   - Merge Gini with homicide data on (`country_code`, `year`).
   - Merge the resulting table with suicide data on (`country_code`, `year`).
   - Use **inner joins** to ensure that the final dataset only contains observations with all three measures present.

4. **Cleaning**
   - Convert all numeric columns to appropriate data types (e.g. `float` for rates and Gini).
   - Remove rows with missing or impossible values (e.g. negative rates, unrealistic Gini values).
   - Optionally filter to countries with a minimum number of valid years (e.g. ≥ 10) to reduce noise.

All integration and cleaning steps are explicitly documented and executed in the main Jupyter Notebook.

---

## 5. Feature Engineering & EDA

### 5.1 Engineered Features

To leverage the integrated dataset, we create new features that combine information across sources:

- **Combined intentional death rate**  
  \- `intentional_death = homicide_data + suicide_data`  

- **Lagged inequality**  
  \- `gini_lag_1`: previous-year Gini per country, to approximate delayed effects of inequality.

- **High inequality indicator (optional)**  
  \- Binary flag indicating whether a country_code-year has Gini above a chosen threshold.

- **Region indicators (optional)**  
  \- Categorical or one-hot encoded regions to capture geographic patterns.

### 5.2 EDA

We explore the integrated data with:

- Distributions (histograms / KDE plots) of Gini, homicide, suicide, and violent death rates.  
- Scatter plots:
  - Gini vs homicide_data  
  - Gini vs suicide_data  
  - Gini vs intentional_death  
- Correlation analysis (Pearson and Spearman) to investigate linear and monotonic relationships.

These visualizations help motivate the choice of models and reveal initial patterns.

---

## 6. Modeling & Evaluation

Given our central question, we frame the problem as a **regression task**:

- **Target variable:**  
  - Primary: `intentional_death` (homicide_data + suicide_data per 100k)  
  - Optionally: separate models for `homicide_data` and `suicide_data`

### 6.1 Models

We implement at least two scikit-learn models:

1. **Linear Regression**
   - Serves as a transparent baseline.
   - Estimates a linear relationship between inequality and intentional death rates.

2. **Random Forest Regressor** (or similar ensemble model)
   - Captures non-linear relationships and interactions (e.g. inequality × country × year).
   - Provides feature importance measures.

### 6.2 Evaluation

- Train/test split (e.g. time-based: train on earlier years, test on later years).  
- Metrics:
  - **R²** – proportion of variance explained on the test set.
  - **RMSE / MAE** – average error magnitude.

We compare models in terms of predictive performance and interpretability, and we inspect the **most important features** in the best model to understand the role of the Gini index and other predictors.

---

## 7. Repository Structure

The repository is organized as follows:

```text
.
├── data/
│   ├── gini_index.csv
│   ├── gini_index_data_clean.csv
│   ├── gini_index_data_dictionary.csv
│   ├── homicides.csv
│   ├── homicide_data_clean.csv
│   ├── homicide_data_dictionary.csv
│   ├── suicides.csv
│   ├── suicide_data_clean.csv
│   ├── suicide_data_dictionary.csv
│   ├── merged_dataset.csv
│   └── merged_dataset_dictionary.csv
├── docs/
│   ├── CLEANING_REPORT.md
│   └── requirements.txt
├── hypothesis_final/
│   └── final_hypothesis.ipynb
├── hypothesis_mingyang/
│   └── hypothesis_mingyang.ipynb
├── hypothesis_on_datasets/
│   ├── manaf_hypothesis_1.ipynb
│   └── manaf_hypothesis_2.ipynb
├── juilee_hypothesis/
│   └── hypothesis_juilee.ipynb
├── notebooks/
│   ├── clean_gini_index_data.ipynb
│   ├── clean_homicide_data.ipynb
│   ├── clean_suicide_data.ipynb
│   ├── corelation_analysis.ipynb        # main analysis notebook
│   └── merge_dataset.ipynb              # integration notebook
└── README.md
