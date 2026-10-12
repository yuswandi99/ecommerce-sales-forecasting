# E-commerce Sales Analysis & Forecasting — Time Series

Analyzes one year of electronics e-commerce sales (2019) to answer business questions about revenue, products, cities, and peak hours. It then **forecasts daily transactions for the next 30 days** with SARIMA and Exponential Smoothing (ETS) to support capacity and stock planning.

## Dataset
- **E-commerce sales data 2019** (public dataset): 12 monthly CSV files merged into **186,850 rows**
- **185,686 rows** after cleaning (removed 355 repeated header rows, 545 empty rows, and 1,162 duplicates)
- Columns: Order ID, Product, Quantity, Price, Order Date, Purchase Address

## Tools
Python · Pandas · NumPy · Statsmodels (SARIMAX, ETS, ADF test, seasonal decomposition) · Matplotlib · Seaborn

## Workflow
1. **Data cleaning and feature engineering**: Revenue, City/State, Date, and Hour. Removed a partial day (2020-01-01).
2. **EDA (business questions)**
   - Q1: Revenue, orders, items per transaction, and AOV
   - Q2: Daily, weekly, and monthly trends (normalized per day)
   - Q3: Top products and a simple **market basket analysis**
   - Q4: Top cities by orders and spending
   - Q5: Rush-hour analysis
3. **Time series analysis**: seasonal decomposition (weekly), ADF stationarity test (stationary after 1 differencing, so d = 1)
4. **Modeling**: a naive seasonal baseline (lag-7) vs **SARIMA** (grid search by AIC) and 5 ETS variants. The last 30 days were held out as the test set.
5. **Forecast**: 30-day forecast with a 95% confidence interval

## Key Findings
| Metric | Value |
|---|---|
| Total revenue (GMV) | **$34.46M** |
| Orders | 178,406 |
| Avg spending per transaction | $193 |
| Avg items per transaction | **1.17** |

- **Low items per order (1.17)** leaves a big **cross-sell** opportunity.
- **Revenue ≠ volume:** MacBook Pro is #1 in revenue, while batteries and cables lead in quantity.
- **Natural bundles:** phone + matching accessory (e.g., Lightning Cable + iPhone, 1,010 orders). Laptops are usually bought alone, so laptop bundles are not recommended.
- **Rush hours:** 11:00–13:00 and 18:00–20:00. The quietest hours are 02:00–04:00.
- **Trend dominates seasonality:** weekly seasonal amplitude is only ~6 transactions, compared with a ~510-transaction trend range.

## Forecasting Results (test set, 30 days)
| Model | MAE | RMSE | MAPE |
|---|---|---|---|
| **SARIMA(1,1,2)(0,1,1,7)** | **101.2** | **105.3** | **12.95%** |
| ETS Holt-Winters (m=7) | 101.9 | 105.9 | 13.04% |
| ETS Holt's linear | 102.2 | 106.1 | 13.08% |
| Naive baseline (lag-7) | — | — | 24.26% |

SARIMA **cuts the baseline error almost in half** (MAPE 24.3% → 13.0%).

**30-day forecast:** ~**779 transactions/day** (95% CI: 613–945), which is +0.5% vs the previous 30 days.

## Business Recommendations
- Plan capacity for **~779 orders/day**, with a buffer up to the upper 95% bound for peak days.
- Send push notifications and flash sales **before** the rush-hour peaks.
- Schedule maintenance and deployments at **02:00–04:00**.
- Promote phone + accessory bundles. Do not discount laptops through bundles.
- Use the forecast for 2–4 week planning only, and **retrain weekly**.

## Limitations
- Only one year of data, so yearly seasonality (holidays, Black Friday) cannot be modeled.
- "Visitor" is a proxy (unique addresses), so conversion rate cannot be calculated.
- No time zone information, so hourly patterns mix US East and West Coast time.

## Repository Structure
```
ecommerce-sales-forecasting/
├── sales_forecasting.ipynb   # full analysis (narrative in Bahasa Indonesia)
└── README.md
```

## How to Run
1. Open the notebook in Google Colab or Jupyter
2. Put the 12 `sales_data_*.csv` files in one folder and update `data_dir`
3. Run all cells

---
**Author:** Andi Yuswandi · [GitHub](https://github.com/yuswandi99)
