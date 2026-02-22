# MAS 651 Final Project: Yelp Data Analysis
### Recommendation Systems & Predictive Analytics for Tampa Bay Restaurants & Bars

**Course:** MAS 651 — Applied Machine Learning (Spring 2026)

**University of Miami**

---

## Overview

This project implements a full analytics pipeline on the [Yelp Open Dataset](https://business.yelp.com/data/resources/open-dataset/), simulating a real-world business analytics scenario for the Tampa Bay, Florida restaurant and bar market.

**Key Components:**

- **Recommendation System:** Popularity baseline, SVD collaborative filtering (user → business), and hybrid item-to-item similarity (content + collaborative)
- **Cold-Start Analysis:** Hybrid fallback strategy for new users with beyond-accuracy metrics (coverage, diversity, popularity bias)
- **Predictive Analytics:** Churn/survival analysis predicting business closure using early-life features, Kaplan-Meier curves, and Cox Proportional Hazards modeling

## Dataset

| Source | Description |
|--------|-------------|
| **Raw Data** | [Yelp Open Dataset](https://business.yelp.com/data/resources/open-dataset/) (~4.3 GB) |
| **Google Drive** | [yelp_dataset.tar](https://drive.google.com/file/d/1wzVfcOyvjh5u3ZpC__f6-5Gg13m0V_Rr/view?usp=share_link) |
| **Preprocessed** | Available in `preprocessed_data/` (filtered Tampa Bay restaurants & bars) |

### Filtering Criteria

- **Geography:** Tampa Bay metro area (Tampa, Clearwater, St. Petersburg, Brandon, +12 surrounding cities)
- **Categories:** Restaurants, Bars, Food, Nightlife
- **Users:** ≥ 5 reviews
- **Businesses:** ≥ 20 reviews
- **Final dataset:** ~341K reviews, ~23K users, ~4K businesses

## Repository Structure

```
MAS651-Final-Project/
├── README.md
├── requirements.txt
├── MAS651_Final_Project_Tampa_Yelp.ipynb    # Main notebook (run sequentially)
├── preprocessed_data/                        # Filtered CSVs for reproducibility
│   ├── tampa_businesses.csv
│   ├── tampa_reviews.csv
│   ├── tampa_users.csv
│   ├── tampa_checkins.csv
│   └── tampa_tips.csv
└── .gitignore
```

## Quick Start

### Option A: Run from preprocessed data (recommended)

```bash
git clone https://github.com/YOUR_USERNAME/MAS651-Final-Project.git
cd MAS651-Final-Project
pip install -r requirements.txt
jupyter notebook MAS651_Final_Project_Tampa_Yelp.ipynb
```

### Option B: Run in Google Colab

1. Open the notebook in [Google Colab](https://colab.research.google.com/)
2. The first cells automatically install dependencies and download the raw dataset from Google Drive
3. Run all cells sequentially

## Methodology

### Recommendation System

| Model | Type | Description |
|-------|------|-------------|
| Popularity Baseline | Non-personalized | Bayesian-weighted popularity score |
| SVD | Collaborative Filtering | Matrix factorization with GridSearchCV tuning |
| Item-to-Item Hybrid | Content + Collaborative | Cosine similarity on combined feature vectors (α=0.6 content, 0.4 collaborative) |

### Evaluation Metrics

- **Accuracy:** RMSE, MAE, Hit Rate@K
- **Beyond-accuracy:** Catalog coverage, intra-list diversity, popularity bias

### Churn Prediction

- **Models:** Logistic Regression, Random Forest, Gradient Boosting
- **Features:** Early-life metrics (first 12 months) — review velocity, avg rating, engagement, unique reviewers
- **Survival Analysis:** Kaplan-Meier curves + Cox Proportional Hazards

## Reproducibility

| Parameter | Value |
|-----------|-------|
| Random Seed | 42 |
| Train/Test Split | Per-user time-based holdout |
| SVD Tuning | 3-fold GridSearchCV |
| Churn Split | 80/20 stratified |
| Python | 3.10+ |

## Requirements

```
pandas>=2.0
numpy>=1.24
scikit-learn>=1.3
scikit-surprise>=1.1
lifelines>=0.27
matplotlib>=3.7
seaborn>=0.12
scipy>=1.10
```

## License

This project uses the [Yelp Open Dataset](https://business.yelp.com/data/resources/open-dataset/) for academic purposes under Yelp's Dataset License.
