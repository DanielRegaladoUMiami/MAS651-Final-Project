# MAS 651 Final Project: Yelp Restaurant & Bar Analytics — Tampa Bay

**Course:** MAS 651 — University of Miami
**Authors:** Daniel Regalado Cardoso, Jeanne Hassoun, Luna Gerlic
**Date:** Spring 2025

---

## Overview

This project delivers an end-to-end analytics pipeline on the [Yelp Open Dataset](https://www.yelp.com/dataset), focusing on restaurants, bars, and coffee & tea businesses across the Tampa Bay metropolitan area (15 cities in Florida). It addresses three interconnected problems:

1. **Recommendation Systems** — Popularity baselines, SVD collaborative filtering, Neural Collaborative Filtering (NCF), and a hybrid item-to-item similarity engine.
2. **Cold-Start Mitigation** — Strategies for new users with no rating history, evaluated with beyond-accuracy metrics (coverage, diversity, novelty, popularity bias).
3. **Business Survival Prediction** — Forecasting closure risk from early operational signals using Optuna-tuned classifiers (Logistic Regression, Random Forest, XGBoost) and Kaplan-Meier / Cox Proportional Hazards survival analysis.

---

## Repository Structure

```
MAS651-Final-Project/
├── MAS651_Final_Project_Yelp_v4-3.ipynb    # Main analysis notebook
├── MAS651_Final_Project_Report.pdf          # Written project report
├── MAS65_Final_Project.pptx.pdf             # Presentation slides (PDF)
├── requirements.txt                         # Python dependencies
├── preprocessed_data/
│   ├── Yelp_Data_Preprocessing.ipynb        # Notebook to regenerate data from raw Yelp JSON
│   ├── tampa_businesses.csv.gz              # Business profiles (Tier 1)
│   ├── tampa_reviews_t1.csv.gz              # Reviews — full scope
│   ├── tampa_reviews_t2.csv.gz              # Reviews — medium density
│   ├── tampa_reviews_t3.csv.gz              # Reviews — dense core
│   ├── tampa_users.csv.gz                   # User profiles
│   ├── tampa_checkins.csv.gz                # Check-ins
│   ├── tampa_tips.csv.gz                    # Tips
│   └── README.md                            # Data dictionary & loading instructions
├── .gitignore
└── README.md
```

---

## Dataset

The analysis uses the Yelp Open Dataset (~4.3 GB of JSON files):

| Source File | Raw Rows |
|---|---|
| `business.json` | 150,346 |
| `review.json` | 6,990,280 |
| `user.json` | 1,987,897 |
| `checkin.json` | 131,930 |
| `tip.json` | 908,915 |

After geographic and category filtering (Tampa Bay — Restaurants, Bars & Coffee/Tea), the data is organized into **three density tiers**:

| Tier | Purpose | Reviews | Users | Businesses | Sparsity |
|------|---------|---------|-------|------------|----------|
| T1 (Full) | EDA + Churn/Survival | 323,775 | 22,657 | 3,769 | 99.62% |
| T2 (Medium) | Item-to-Item Hybrid | 190,746 | 7,352 | 2,218 | 98.83% |
| T3 (Dense) | SVD + NCF | 100,669 | 2,279 | 1,390 | 96.82% |

Preprocessed data is stored as compressed `.csv.gz` files in `preprocessed_data/` — pandas reads them natively.

---

## Getting Started

### Prerequisites

- Python 3.9–3.12 (scikit-surprise requires ≤3.12)
- ~4.3 GB disk space if regenerating from raw data

### Installation

```bash
git clone https://github.com/DanielRegaladoUMiami/MAS651-Final-Project.git
cd MAS651-Final-Project
pip install -r requirements.txt
```

### Running the Notebook

| Path | What to do | Time |
|------|-----------|------|
| **Recommended** | Run Section 1 (setup), then **skip to Section 3.7** which loads preprocessed data directly from GitHub | ~2 min |
| From raw data | Run `preprocessed_data/Yelp_Data_Preprocessing.ipynb` to download and preprocess from scratch | ~20 min |

> **Section 3.7 pulls the compressed data straight from this GitHub repo — no local files or Google Drive needed.**

### Google Colab

The notebook is Colab-ready. Open it in Colab, run Section 1 for setup, then Section 3.7 to load data.

---

## Key Results

### Recommendation Systems

**Item-to-Item Hybrid (Tier 2):** Best Hit Rate @10 of **4.11%** with optimal weights Content=0.1, Text=0.4, Collab=0.5.

**Rating Prediction (Tier 3):**

| Model | RMSE | MAE |
|-------|------|-----|
| SVD | 1.1040 | 0.8527 |
| NCF | 1.1475 | 0.8717 |

**Hit Rate Comparison (Tier 3):**

| K | Popularity | SVD | NCF |
|---|-----------|-----|-----|
| @5 | 1.10% | 0.44% | 0.83% |
| @10 | 1.67% | 0.75% | 1.36% |
| @20 | 1.67% | 0.75% | 1.36% |

**Beyond-Accuracy Metrics:**

| Metric | Popularity | SVD | NCF |
|--------|-----------|-----|-----|
| Catalog Coverage | 2.1% | 16.5% | **58.6%** |
| Intra-List Diversity | 80.1% | 84.5% | **85.3%** |

NCF recommends 814 unique businesses (58.6% of catalog) vs. Popularity's 29 (2.1%) — making it the best model for discovery and long-tail exposure.

### Business Survival Prediction

**Classification (Optuna-Tuned, 34 features):**

| Model | AUC | F1 | Accuracy |
|-------|-----|-----|----------|
| Logistic Regression | 0.6787 | 0.7002 | 61.3% |
| **Random Forest** | **0.7406** | **0.8651** | **78.8%** |
| Gradient Boosting | 0.7306 | 0.7925 | 70.6% |

**Top 5 Features (Random Forest):** total_checkins (0.088), attribute_count (0.088), checkins_per_review (0.075), competition_density (0.057), early_text_len_avg (0.054)

**Cox PH Survival Analysis (Concordance = 0.64):** Rating inconsistency (std) increases closure hazard by 18%, and slower early growth increases hazard by 26% (both p < 0.005).

---

## Notebook Structure

1. **Setup & Configuration**
2. **Data Loading** (raw JSON — skip if using preprocessed)
3. **Preprocessing & Tiered Filtering** (3.7 = load from GitHub)
4. **Exploratory Data Analysis** — Distributions, temporal patterns, open vs. closed
5. **Leakage-Safe Train/Test Split** — Temporal split
6. **Recommendation Systems**
   - 6.1 Popularity Baseline
   - 6.2 Item-to-Item Hybrid Similarity (grid search over weights)
   - 6.3 SVD Collaborative Filtering (GridSearchCV)
   - 6.4 Neural Collaborative Filtering (PyTorch)
   - 6.5 Cold-Start Bridge
   - 6.6 Beyond-Accuracy Metrics
   - 6.7 Hit Rate Evaluation
   - 6.8 NDCG & MAP Ranking Quality
7. **Predictive Analysis: Churn / Survival**
   - 7.1 Feature Engineering (34 features from first 12 months)
   - 7.2 Optuna-Tuned Classification
   - 7.3 Decision Threshold Optimization
   - 7.4 Feature Importance + SHAP Interpretability
   - 7.5 Kaplan-Meier + Cox PH Survival Analysis
8. **Business Insights & Recommendations**
9. **Conclusion**

---

## Key Technologies

| Category | Libraries |
|----------|-----------|
| Data | pandas, numpy, scipy |
| Visualization | matplotlib, seaborn |
| ML | scikit-learn, xgboost |
| Recommenders | scikit-surprise (SVD, NMF, BaselineOnly) |
| Deep Learning | PyTorch (NCF) |
| Tuning | Optuna |
| Interpretability | SHAP |
| Survival | lifelines (Kaplan-Meier, Cox PH) |
| Statistics | statsmodels |

---

## Reproducibility

- **Random seed:** 42
- **Train/test split:** Temporal (leakage-safe)
- **Hyperparameters:** Optuna-logged with cross-validation
- **Environment:** Tested on Google Colab and local Python 3.9.6

---

## License

This project was developed as part of the MAS 651 coursework at the University of Miami.
