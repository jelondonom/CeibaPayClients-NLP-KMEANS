# Ceiba Pay — Listening to Customers with Data
### Sentiment analysis (TextBlob) + customer segmentation (K-Means)

**Business question:** *What customer segments does Ceiba Pay have, how does each one feel about the service, and where should the 2027 budget go to protect margin and grow?*

Ceiba Pay is a Colombian digital wallet. This project combines **2,000 customer records** (usage, profile, margin, NPS) with their **free-text comments** to find behavior-based segments, measure how each one feels, and turn that into three budget recommendations.

---

## Key results

| Segment | Customers | NPS | % of margin | Main topic in comments |
|---|---|---|---|---|
| QR merchants | 15% | −40 | **54%** | Fees & limits (49%) |
| Savers | 20% | +34 | 27% | Savings (40%), new products (25%) |
| P2P youth | 30% | +16 | 9% | P2P transfers, app failures |
| Cash-out users | 20% | −36 | 11% | Cash withdrawal (40%), hard to use (27%) |
| Dormant | 15% | −42 | 0.3% | Sign-up & validation (44%) |

- **Overall NPS = −8** (43% detractors vs 35% promoters).
- **The most valuable segment is among the unhappiest:** QR merchants generate 54% of margin with NPS −40.
- **TextBlob sentiment agrees with NPS** (Spearman ρ = 0.53) but is more optimistic (62.6% positive comments).
- **K-Means (K = 5)** on usage variables only, silhouette = 0.44.

### 2027 recommendations
| # | Segment | Action | KPI |
|---|---|---|---|
| 1 | QR merchants | Merchant plan: volume-tiered QR fees, higher limits, priority support | Segment NPS, merchant churn, segment margin |
| 2 | Cash-out users | Card-less QR withdrawals at retail chains with small-town reach (D1, Ara, Dollarcity) + "simple mode" app | Segment NPS, % cash-withdrawal complaints, withdrawal success rate |
| 3 | Savers | Yield-bearing savings pocket / digital CDT | Adoption %, average balance, segment margin |

---

## Pipeline

| Module | Content |
|---|---|
| 0 | Setup: imports, seed, paths, colorblind-safe palettes, MD5 of raw file |
| 1 | Load & quality diagnosis |
| 2 | Cleaning with audit log (percent scale fix, age imputation, logic rules) |
| 3 | Outliers: IQR & z-score, raw vs `log1p` |
| 4 | EDA: usage, customer profile, NPS, Spearman correlations |
| 5 | Text cleaning: tokens, Spanish stopwords, word frequencies, word clouds |
| 6 | Sentiment (ES→EN translation + TextBlob) and rule-based topics |
| 7 | K-Means: scaling, choice of K, segment profiles, PCA view, saved model |
| 8 | Segments × sentiment × topics + 2027 recommendations |
| 9 | Export, integrity check (MD5), executive summary |

### Methods
- **Cleaning:** nothing deleted; every decision logged (`outputs/02_cleaning_log.xlsx`) and flagged.
- **Translation:** `Helsinki-NLP/opus-mt-es-en` (MarianMT), clause by clause with a glossary of Colombian terms; cached.
- **Sentiment:** TextBlob polarity → Positive / Neutral / Negative. NPS used only for validation.
- **Topics:** keyword rules (13 topics), 99.6% of comments covered.
- **Clustering:** `log1p` on skewed amounts/counts + `StandardScaler` + `KMeans(n_clusters=5)`. Age, strata and city excluded (used only to describe segments).

---

## Repository structure
```
ceiba-pay-analytics/
├── README.md
├── requirements.txt
├── executive_summary.md
├── notebooks/
│   └── ceiba_pay_sentiment_segmentation.ipynb   # executed, with outputs
├── data/
│   └── ceiba_pay_clientes.xlsx                  # raw data (only if publishing is allowed)
├── outputs/                                     # cleaning log, clean data, translations, final results
├── models/
│   └── kmeans_segments.joblib
├── reports/figures/                             # all charts, prefixed by module (02_, 03_, …)
└── docs/
    └── video_script.md
```

---

## How to run
1. Open the notebook in **Google Colab** (GPU optional; translation takes ~3 min on CPU).
2. Upload `ceiba_pay_clientes.xlsx` to `MyDrive/<project folder>/data/` and set `PROJECT_DIR` in cell 0.3.
3. Run cell 0.1, restart the session, then **Runtime → Run all**.

Local run: `pip install -r requirements.txt`, remove the Drive cells and point `PROJECT_DIR` to the repo folder.

## How to assign a new customer to a segment
```python
import joblib, numpy as np, pandas as pd

model = joblib.load("models/kmeans_segments.joblib")
new = pd.DataFrame([{
    "antiguedad_meses": 12, "transacciones_mes": 150, "ticket_promedio_cop": 20000,
    "saldo_promedio_cop": 1500000, "pct_pagos_qr": 70, "pct_transferencias_p2p": 10,
    "pct_retiros_corresponsal": 15, "pct_pago_servicios": 5, "usa_bolsillo_ahorro": 0,
}])
new[model["log_cols"]] = np.log1p(new[model["log_cols"]])
cluster = model["pipeline"].predict(new[model["features"]])[0]
print(model["segment_names"][cluster])   # -> "QR merchants"
```
Percent columns must be in 0–100 scale (see Module 2 for the raw-file correction).

---

## Limitations
- The data looks **synthetic**: topics appear in only one segment and clusters are unusually clean.
- TextBlob is lexicon-based: it misses sarcasm and factual complaints ("I'm going to close the account"), and it depends on machine translation.
- Topic rules were built for these comment templates; new text may need new keywords.
- NPS is a single survey snapshot (Jul–Sep 2026).

## Author
Juan Esteban Londoño-Marín — International Business, Universidad EAFIT (Business Intelligence emphasis).
