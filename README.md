# Olist Brazilian E‑Commerce — EDA, Segmentation & Forecasting

This project explores the public **Olist Brazilian E‑Commerce** dataset to understand demand, customer behavior, logistics performance, and to **forecast daily orders**.

## 🎯 Objectives
- Explore sales trends, popular products, and market dynamics.  
- Analyze customer behavior & seller performance (RFM + clustering).  
- Identify logistics/delivery patterns & their impact on **review scores**.  
- Build and compare forecasting models to support **capacity & inventory planning**.

---

## 📦 Data (CSV files)
Place these files in your working directory (e.g., `DATA_DIR=/content` for Colab):

```
olist_orders_dataset.csv
olist_order_items_dataset.csv
olist_order_payments_dataset.csv
olist_customers_dataset.csv
olist_products_dataset.csv
olist_sellers_dataset.csv
olist_geolocation_dataset.csv
olist_order_reviews_dataset.csv
product_category_name_translation.csv
```

---

## ⚙️ Environment & Setup

### Google Colab (recommended)
```python
%%capture
!pip install -U pip setuptools wheel cython
!pip install "numpy<2.0" "pandas<2.2" "scipy<1.13" "scikit-learn<1.4" "statsmodels<0.15"
!pip install "pmdarima==2.0.4" geopandas calmap tensorflow keras

# Restart kernel after installing pinned packages
import IPython, warnings
IPython.Application.instance().kernel.do_shutdown(True)
```

### Local environment
```bash
python -m venv .venv && source .venv/bin/activate
pip install -U pip setuptools wheel cython
pip install "numpy<2.0" "pandas<2.2" "scipy<1.13" "scikit-learn<1.4" "statsmodels<0.15"
pip install "pmdarima==2.0.4" geopandas calmap tensorflow keras
```

**Optional:** Silence known deprecation warnings during modeling:
```python
import warnings
warnings.filterwarnings("ignore", category=DeprecationWarning, message=r".*ndim > 0 to a scalar.*")
warnings.filterwarnings("ignore", category=FutureWarning, message=r".*Series.__getitem__ treating keys as positions.*")
```

---

## 🗂️ Quick Start (minimal)
```python
import os, pandas as pd, numpy as np

DATA_DIR = "/content"  # change if needed
def load_csv(name): return pd.read_csv(os.path.join(DATA_DIR, name), low_memory=False)

orders            = load_csv("olist_orders_dataset.csv")
order_items       = load_csv("olist_order_items_dataset.csv")
payments          = load_csv("olist_order_payments_dataset.csv")
customers         = load_csv("olist_customers_dataset.csv")
products          = load_csv("olist_products_dataset.csv")
sellers           = load_csv("olist_sellers_dataset.csv")
geolocation       = load_csv("olist_geolocation_dataset.csv")
reviews           = load_csv("olist_order_reviews_dataset.csv")
product_category  = load_csv("product_category_name_translation.csv")
```

---

## 🧼 Cleaning Highlights
- **Orders:** Convert timestamps; drop logically impossible timelines (keep canceled/undelivered if consistent).  
- **Order Items:** Keep `price>0` and `freight_value>=0`; rename `shipping_limit_date→shipping_deadline` (datetime).  
- **Payments:** Normalize `payment_type` (`boleto→bank_slip`), drop `not_defined` for EDA focus.  
- **Reviews:** Coerce times to datetime; de‑duplicate `review_id`.  
- **Products:** Fix typos (`product_*_lenght`); drop missing categories; median‑impute weight/dimensions; remove non‑positive values.  
- **Category Translation:** Ensure PT→EN coverage; append missing keys (`pc_gamer`, etc.).  
- **Sellers:** Normalize city names (lowercase, strip accents); fix common typos.  
- **Geolocation:** Aggregate by ZIP prefix to representative lat/lng & city/state.

---

## 🔗 Wrangling
- Join **orders + items + payments + reviews + products + categories + sellers**, then merge customers & geolocation to build a unified, analysis‑ready table (`commerce`).

---

## 📊 Key EDA Insights
- **Sales & Seasonality:** Orders grew through 2017; peak in **November** (Black Friday/holidays). Weekdays (Mon–Tue) dominate demand.  
- **Payments:** **Credit card (~74%)** leads; higher installments associated with **higher payment values**.  
- **Geography & Reviews:** Demand & supply concentrate in **SP–MG–RJ**; **late deliveries sharply reduce review scores**.  
- **Products:** Top categories: **bed & bath, health & beauty, sports, furniture, computer accessories**.

---

## 🧪 Hypothesis Tests (examples)
- **Delays → Reviews:** Both OLS and Mann–Whitney show significant negative effect of delay on review scores.  
- **Delays by State:** Significant differences across states, but **small effect size**.  
- **Weekday vs Weekend Sales:** Distributions differ; prioritize weekdays but test weekend promos.

---

## 👥 Segmentation
- **RFM scoring** → segments (Champions, Loyal, Potential Loyalist, At Risk, Hibernating, New, Others).  
- **K‑Means clustering** on (R, log1p(F), log1p(M)); report **cluster profiles** and **personas**.  
- Exports: `rfm_segments.csv`, `segment_summary.csv`, `cluster_profile.csv`.

---

## 🔮 Forecasting
Daily order forecasting with rolling time‑series CV:
- **Families compared:** Linear Regression (lag features), **Exponential Smoothing (ES)**, Auto‑ARIMA, LSTM.  
- Best practical trade‑off: **ES (additive trend & seasonality)** with tuned smoothing (e.g., `alpha=0.2, beta=0.0, gamma=0.8, m=7`).  
- Final chart: **62‑day out‑of‑sample** forecast (weekday seasonality preserved).

---

## 🧭 Business Recommendations (from ES 62‑day forecast)
- **Capacity & SLA:** Plan for **230–360 orders/day**; keep **buffer 400–450**. Add shifts **Mon–Tue**; ≥**90%** orders processed **<24h**.  
- **Inventory:** Safety stock = **1–1.5×** weekly daily deviation; prioritize **top‑revenue categories**.  
- **Logistics:** Strengthen **SP–MG–RJ** hub; **mix carriers** where delay variability is high.  
- **Marketing:** Push acquisition/retention on **weekdays**; use **weekend promos** to smooth demand.  
- **Payment/AOV:** Encourage **credit card & installments** for high‑value items.  
- **Seasonality:** Add **special‑event uplifts** (e.g., **Black Friday**) for inventory, ads, and capacity.

---

## 📝 Citation
Dataset: **Olist Brazilian E‑Commerce Public Dataset** (Kaggle).

---

## ⚖️ License
For educational/research use. Check Olist dataset license for redistribution terms.
