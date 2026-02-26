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
├── MAS651_Final_Project_Yelp_v4-3.ipynb   # Main analysis notebook (all code + results)
├── MAS651_Final_Project_Report.pdf         # Written project report
├── MAS65_Final_Project.pptx.pdf            # Presentation slides (PDF)
├── preprocessed_data/
│   ├── README.md                           # Instructions for obtaining preprocessed CSVs
│   └── .gitkeep
├── requirements.txt                        # Python dependencies
├── .gitignore
└── README.md                               # This file
```

---

## Dataset

The analysis uses the Yelp Open Dataset (~4.3 GB of JSON files):

| Source File      | Raw Rows     |
|------------------|-------------|
| `business.json`  | 150,346     |
| `review.json`    | 6,990,280   |
| `user.json`      | 1,987,897   |
| `checkin.json`   | 131,930     |
| `tip.json`       | 908,915     |

After geographic and category filtering (Tampa Bay — Restaurants, Bars & Coffee/Tea), the data is organized into three density tiers:

| Tier | Purpose                 | Reviews   | Users  | Businesses | Sparsity |
|------|-------------------------|-----------|--------|------------|----------|
| 1    | Full scope (EDA + Churn)| 323,775   | 22,657 | 3,769      | 99.62%   |
| 2    | Item-to-Item similarity | 190,746   | 7,352  | 2,218      | 98.83%   |
| 3    | Dense core (SVD / NCF)  | 100,669   | 2,279  | 1,390      | 96.82%   |

---

## Notebook Structure

The notebook (`MAS651_Final_Project_Yelp_v4-3.ipynb`) is organized into 9 sections:

1. **Setup & Configuration** — Dependencies and library initialization
2. **Data Loading** — Yelp JSON file ingestion
3. **Preprocessing & Tiered Filtering**
   - Geographic & category filtering
   - Tier 1 (full scope), Tier 2 (medium density), Tier 3 (dense core)
   - Save / load preprocessed data
4. **Exploratory Data Analysis (EDA)** — Distributions, temporal patterns, activity analysis, open vs. closed businesses, sparsity
5. **Leakage-Safe Train/Test Split** — Temporal split to prevent data leakage
6. **Recommendation Systems**
   - 6.1 Popularity Baseline
   - 6.2 Item-to-Item Hybrid Similarity
   - 6.3 SVD Collaborative Filtering
   - 6.4 Neural Collaborative Filtering (NCF)
   - 6.5 Cold-Start Bridge
   - 6.6 Beyond-Accuracy Metrics (coverage, diversity, novelty, bias)
   - 6.7 Hit Rate Evaluation
7. **Predictive Analysis: Churn / Survival**
   - 7.1 Feature Engineering
   - 7.2 Optuna-Tuned Classification Models
   - 7.3 Decision Threshold Optimization
   - 7.4 Feature Importance (SHAP)
   - 7.5 Survival Analysis (Kaplan-Meier, Cox PH)
8. **Business Insights & Recommendations**
9. **Conclusion**

---

## Getting Started

### Prerequisites

- Python 3.9–3.12 (scikit-surprise requires ≤3.12)
- ~4.3 GB disk space for the raw Yelp dataset (or use preprocessed data)

### Installation

```bash
git clone https://github.com/DanielRegaladoUMiami/MAS651-Final-Project.git
cd MAS651-Final-Project
pip install -r requirements.txt
```

### Data Access

There are two paths to run the notebook:

| Path | Steps | Time |
|------|-------|------|
| **From raw data** | Run notebook cells 1–17 to download and preprocess | ~15–20 min |
| **Preprocessed shortcut** | Load pre-built CSVs via Section 3.5 | ~1 min |

> **Note:** Preprocessed CSVs (~276 MB total) are too large for GitHub. The notebook provides a Google Drive download link, or you can generate them by running the preprocessing cells.

### Running on Google Colab

The notebook includes Colab-compatible setup cells that install all dependencies and download data automatically via `gdown`.

---

## Key Technologies

| Category | Libraries |
|----------|-----------|
| Data manipulation | pandas, numpy, scipy |
| Visualization | matplotlib, seaborn |
| Machine learning | scikit-learn, xgboost |
| Recommender systems | scikit-surprise (SVD, NMF, BaselineOnly) |
| Deep learning | PyTorch (Neural Collaborative Filtering) |
| Hyperparameter tuning | Optuna |
| Model interpretability | SHAP |
| Survival analysis | lifelines (Kaplan-Meier, Cox PH) |
| Statistics | statsmodels |

---

## Reproducibility

- **Random seed:** 42 (fixed across all stochastic processes)
- **Train/test split:** Temporal (leakage-safe)
- **Hyperparameters:** Documented and Optuna-logged
- **Environment:** Tested on Google Colab and local Python 3.9.6

---

## License

This project was developed as part of the MAS 651 coursework at the University of Miami.
