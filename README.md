# 📧 Greenweez CRM Mail Campaign Analysis

BigQuery SQL project analyzing email marketing campaign performance for **Greenweez**, a French organic e-commerce brand.

---

## 🎯 Objective

Greenweez's CRM team needed visibility into their email campaign effectiveness. This project answers:

- Which campaigns reached the most people?
- Which campaigns had the highest opening and click rates?
- How much revenue did each campaign generate per 1000 emails sent?
- How do campaign variants (e.g. happyhour vs happyhour_12h) compare across all KPIs?

---

## 🗃️ Dataset

One table from BigQuery (`course14` dataset):

| Table | Rows | Description |
|-------|------|-------------|
| `gwz_mail` | 136 | One row per email campaign with send, open, click, and revenue data |

Key columns in `gwz_mail`:

| Column | Description |
|--------|-------------|
| `journey_id` | Unique campaign ID |
| `journey_name` | Campaign name (encodes date, market, and theme) |
| `sent_nb` | Number of emails sent |
| `opening_nb` | Number of emails opened |
| `click_nb` | Number of clicks |
| `turnover` | Revenue generated (€) |

---

## 📋 Project Steps

### 1. Data Exploration
Previewed the table to understand structure, volume, and column types.

### 2. Duplicate Check
```sql
SELECT DISTINCT journey_id FROM course14.gwz_mail;
```
> 136 unique journey_ids confirmed — no duplicates. ✅

### 3. Sorting & Filtering
- Ranked campaigns by `sent_nb`, `opening_nb`, and `click_nb`
- Used `LIKE '%_nl_%'` and `LIKE '%_nlbe_%'` to segment by market
- Used `LIKE '%happyhour%'` to isolate campaign variants for comparison

### 4. KPI Calculation

| KPI | Formula | Description |
|-----|---------|-------------|
| `opening_rate` | `opening_nb / sent_nb` | Share of sent emails that were opened |
| `click_rate` | `click_nb / sent_nb` | Share of sent emails that were clicked |
| `CTR` | `click_nb / opening_nb` | Share of opened emails that were clicked |
| `turnover_per_mille` | `(turnover / sent_nb) * 1000` | Revenue per 1000 emails sent |

> `SAFE_DIVIDE()` used throughout to avoid zero-division errors.
> `ROUND()` used to format all KPIs to 2–3 decimal places.

### 5. KPI Dashboard
Combined all metrics into a single query for side-by-side campaign comparison.

---

## 📊 Key Findings

### Overall Campaign Performance

| Metric | Value |
|--------|-------|
| Total campaigns | 136 |
| NL market campaigns | ~73 |
| NLBE (Belgium) campaigns | ~63 |
| Top campaign by volume | `210706_nl_deuxieme` (170K sent) |

### Top Campaigns by Opening Rate
> ⚠️ Small-sample campaigns (< 100 sent) skew rates — volume context matters.

### NLBE vs NL Segment
- NLBE campaigns represent ~half the dataset but reach audiences of only ~2,800 vs 100K+ for NL campaigns

### Happyhour Variant Comparison

| Metric | nl_happyhour | nl_happyhour_12h |
|--------|-------------|-----------------|
| Sent | 117,230 | 107,484 |
| Opening Rate | 18.6% | 21.9% |
| Click Rate | 1.4% | 1.6% |
| CTR | 7.3% | 7.2% |
| Turnover / 1000 | €57.22 | €55.97 |

→ Midday send time improved opening rate by ~18% with negligible revenue difference.

---

## 🛠️ SQL Techniques Used

| Technique | Purpose |
|-----------|---------|
| `SELECT DISTINCT` | Duplicate / primary key validation |
| `WHERE` + `LIKE '%...%'` | Market and theme-based campaign filtering |
| `ORDER BY` + `LIMIT` | Top-N campaign rankings |
| `SAFE_DIVIDE(a, b)` | Zero-division-safe KPI calculation |
| `ROUND(x, n)` | Formatting KPI outputs |
| `!=` | Excluding statistical outliers |
| `AND` | Combining multiple filter conditions |

---

## 🔧 Tools

- **Google BigQuery** — SQL engine and data warehouse
- **SQL** — All analysis done in standard SQL with BigQuery-specific functions
