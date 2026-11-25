# TradeSight Export Analytics Platform
**Cynaris Solutions Learning Path | Capstone Project (Data Analyst Intern)**

An enterprise-grade export business intelligence and data engineering platform built for **TradeSight Global Export Management Ltd.** (Chennai, India). The platform tracks shipment transit performance, multi-currency foreign exchange (FX) impact, client profitability rankings, and export compliance exception monitoring across 50 active B2B clients in 12 global destination markets.

---

## 🏗️ Architecture Overview

```
                                  DATA SOURCES
           [da_b2b_orders.csv]  [da_customer_profiles.csv]  [da_product_catalogue.csv]
                                       │
                                       ▼
                              EXTRACT & VALIDATE
                        pipeline/extract.py · pipeline/validate.py
                                       │
                                       ▼
                       TRANSFORM & FEATURE ENGINEERING
                            pipeline/transform.py
                     (FX drift %, Transit variance, Margins)
                                       │
                                       ▼
                              STAR SCHEMA WAREHOUSE
                          sql/schema.sql · tradesight.db
            ┌──────────────────────────┬──────────────────────────┐
            ▼                          ▼                          ▼
      dim_customers              dim_products               dim_countries
            │                          │                          │
            └──────────────────────────┼──────────────────────────┘
                                       │
                                       ▼
                               fct_export_orders
                                       │
                                       ▼
                               ANALYTICAL VIEWS
                   vw_c_suite_kpis · vw_country_fx_summary
                vw_client_profitability · vw_transit_performance
                                       │
                                       ▼
                           BASELINE METRICS ENGINE
                            pipeline/metrics.py
                        reports/baseline_metrics_summary.md
```

---

## 📊 Baseline Trade Metrics (C-Suite Benchmark)

| Metric | Baseline Value | Description |
|---|:---:|---|
| **Total Export Orders** | **1,250 Shipments** | Historical orders across 14 consecutive months (Jan 2025 – Feb 2026) |
| **Total Realized Revenue** | **₹1,724,140,493.69** | Realized sales value converted to INR upon payment settlement |
| **Total Realized Gross Profit** | **₹494,730,887.74** | Gross margin after COGS, freight logistics, and customs clearance |
| **Gross Profit Margin** | **28.69%** | Average realized margin across all 5 export sectors |
| **Net Realized FX Impact** | **-₹1,230,050.67** | Net unhedged currency drift between booking and settlement |
| **On-Time Delivery Rate (OTD%)**| **35.14%** | Shipments delivered on or before the target transit SLA |
| **Average Transit Lead Time** | **22.5 Days** | End-to-end maritime (25.8d avg) and air cargo (7.0d avg) lead time |
| **Compliance Exception Rate** | **15.84%** | 198 orders flagged for customs inspection, paperwork, or HS codes |
| **Active Client Accounts** | **50 Clients** | Enterprise, Mid-Market, and Strategic accounts across 12 countries |
| **Export Destination Markets** | **12 Countries** | USA, UK, Germany, UAE, Singapore, Japan, Australia, Netherlands, etc. |

---

## 🚀 How to Run the Repeatable Pipeline

### Prerequisites
- Python 3.10+ (Standard Library `sqlite3`, `csv`, `json`, `pathlib`)
- Git

### Execute Pipeline in One Command
```bash
python run_pipeline.py
```

### Pipeline Execution Lifecycle
1. **Extraction (`pipeline/extract.py`):** Ingests raw CSV data packs with encoding sanity checks.
2. **Transformation (`pipeline/transform.py`):** Cleans dates, standardizes currency formats, and engineers 8+ derived features (`transit_delay_days`, `fx_drift_pct`, `fx_exposure_tier`, `margin_tier`, `compliance_risk_level`). Saves cleaned datasets to `data/processed/`.
3. **Data Quality Validation (`pipeline/validate.py`):** Executes automated tests validating PK uniqueness, FK referential integrity, chronological consistency, positive values, and domain whitelists.
4. **Star Schema Ingestion (`pipeline/load.py`):** Initializes SQLite Star Schema (`tradesight.db`) with `dim_customers`, `dim_products`, `dim_countries`, `fct_export_orders`, and performance indices.
5. **Metrics Engine (`pipeline/metrics.py`):** Calculates C-Suite KPIs and persists baseline metrics to `agg_baseline_metrics` and `reports/baseline_metrics_summary.md`.

---

## 🗄️ Database & SQL Views

The SQLite database (`tradesight.db`) provides production-ready analytical views:

- **`vw_c_suite_kpis`**: Executive summary rollup of macro trade metrics.
- **`vw_country_fx_summary`**: Country-by-country breakdown of revenue, margins, and currency volatility.
- **`vw_client_profitability_ranked`**: Ranking of all 50 B2B clients by profit contribution in INR.
- **`vw_route_transit_performance`**: Logistics transit days, SLA variance, and on-time delivery rates by port and mode.
- **`vw_compliance_risk_register`**: Frequency, delay impact, and cost inflation of compliance exception flags.

---

## 🌿 Git Version Control Strategy

The project adheres to standard Semantic Versioning and Conventional Commits:

```bash
# Check commit history
git log --oneline --graph --decorate
```

Branches:
- `main`: Production-ready, validated code and milestone documentation.
- `feature/checkpoint-1`: Stakeholder brief and data inventory.
- `feature/checkpoint-2-pipeline`: Repeatable data pipeline, Star Schema DDL, and baseline metrics.
