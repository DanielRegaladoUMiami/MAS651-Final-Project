# Preprocessed Data

Pre-filtered and compressed datasets for the Tampa Bay Yelp analysis. All files are **gzip-compressed CSVs** (`.csv.gz`) — `pandas` reads them natively with `pd.read_csv()`.

## Files

| File | Description | Tier |
|------|-------------|------|
| `tampa_businesses.csv.gz` | Business profiles (name, location, categories, stars, attributes) | T1 |
| `tampa_reviews_t1.csv.gz` | Reviews — full scope (users ≥5, businesses ≥20 reviews) | T1 |
| `tampa_reviews_t2.csv.gz` | Reviews — medium density (users ≥10, businesses ≥30 reviews) | T2 |
| `tampa_reviews_t3.csv.gz` | Reviews — dense core (users ≥20, businesses ≥30 reviews) | T3 |
| `tampa_users.csv.gz` | User profiles (review count, friends, elite status, yelping since) | T1 |
| `tampa_checkins.csv.gz` | Check-in timestamps per business | T1 |
| `tampa_tips.csv.gz` | User tips for businesses | T1 |

## Tier Summary

| Tier | Purpose | Reviews | Users | Businesses | Sparsity |
|------|---------|---------|-------|------------|----------|
| T1 (Full) | EDA + Churn | 323,775 | 22,657 | 3,769 | 99.62% |
| T2 (Medium) | Item-to-Item | 190,746 | 7,352 | 2,218 | 98.83% |
| T3 (Dense) | SVD + NCF | 100,669 | 2,279 | 1,390 | 96.82% |

## Quick Start

```python
import os
import pandas as pd

PREPROCESS_DIR = "preprocessed_data/"

biz        = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_businesses.csv.gz"))
reviews    = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_reviews_t1.csv.gz"))
reviews_t2 = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_reviews_t2.csv.gz"))
reviews_t3 = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_reviews_t3.csv.gz"))
users      = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_users.csv.gz"))
checkins   = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_checkins.csv.gz"))
tips       = pd.read_csv(os.path.join(PREPROCESS_DIR, "tampa_tips.csv.gz"))

# Parse dates
reviews['date'] = pd.to_datetime(reviews['date'])
reviews_t2['date'] = pd.to_datetime(reviews_t2['date'])
reviews_t3['date'] = pd.to_datetime(reviews_t3['date'])
tips['date'] = pd.to_datetime(tips['date'])
```

## How These Were Generated

Run `Yelp_Data_Preprocessing.ipynb` to regenerate from the raw Yelp Open Dataset (~4.3 GB). The notebook downloads, filters to Tampa Bay restaurants/bars/coffee & tea, applies iterative density thresholds, and saves these compressed files.
