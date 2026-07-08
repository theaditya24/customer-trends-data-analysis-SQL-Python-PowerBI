<div align="center">

# 🛍️ Customer Shopping Behaviour Analysis

### An End-to-End Data Analytics Project — Python · SQL · Power BI

![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Cleaning-150458?logo=pandas&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Analysis-4479A1?logo=mysql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![Status](https://img.shields.io/badge/Status-Complete-2E8B57)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

*Turning 3,900 raw shopping transactions into segment, loyalty, and purchase-driver insights a retail team can act on.*

</div>

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Business Problem](#-business-problem)
- [Tech Stack](#-tech-stack)
- [Dataset](#-dataset)
- [Project Workflow](#-project-workflow)
- [Key Insights](#-key-insights)
- [Dashboard Preview](#-dashboard-preview)
- [Repository Structure](#-repository-structure)
- [How to Reproduce](#-how-to-reproduce)
- [Business Recommendations](#-business-recommendations)
- [Future Improvements](#-future-improvements)
- [Author](#-author)

---

## 📖 Overview

A leading retail company wanted to understand how demographics, product categories, discounts, and sales channels shape its customers' shopping behavior — and use that understanding to improve sales, satisfaction, and long-term loyalty.

This project answers that brief end-to-end:

1. **Python** — clean, validate, and feature-engineer the raw transaction data
2. **SQL** — structure the data and query it for customer segments, loyalty patterns, and purchase drivers
3. **Power BI** — visualize the findings in an interactive, filterable dashboard

> 💡 **The headline finding:** per-customer spending and satisfaction barely move across age, gender, subscription status, or payment method. Revenue differences between segments come almost entirely from *how many* customers are in each group — not from any group spending meaningfully more per visit. That single insight reshapes how the business should target and measure campaigns (see [Key Insights](#-key-insights)).

---

## 🎯 Business Problem

> *"How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?"*

**Deliverables scoped for this project:**

| # | Deliverable | Tool |
|---|---|---|
| 1 | Clean & transform the raw dataset | Python (Pandas) |
| 2 | Query customer segments, loyalty & purchase drivers | SQL |
| 3 | Build an interactive stakeholder dashboard | Power BI |
| 4 | Summarize findings & recommendations | Report |
| 5 | Version-controlled, reproducible codebase | GitHub |

---

## 🛠 Tech Stack

| Layer | Tools |
|---|---|
| **Language** | Python 3 |
| **Data Wrangling** | Pandas, NumPy |
| **Database** | MySQL (via `mysql-connector-python` / SQLAlchemy) |
| **Visualization** | Power BI Desktop, DAX, Matplotlib (EDA) |
| **Environment** | Jupyter Notebook |

---

## 🗂 Dataset

**Source:** `customer_shopping_behavior.csv` — 3,900 rows × 18 columns

| Category | Fields |
|---|---|
| **Demographics** | `Customer ID`, `Age`, `Gender`, `Location` |
| **Product** | `Item Purchased`, `Category`, `Size`, `Color`, `Season` |
| **Transaction** | `Purchase Amount (USD)`, `Discount Applied`, `Promo Code Used`, `Payment Method` |
| **Engagement** | `Review Rating`, `Subscription Status`, `Previous Purchases`, `Frequency of Purchases`, `Shipping Type` |

---

## 🔄 Project Workflow

### 1️⃣ Data Preparation (Python)

- Imputed **37 missing `Review Rating`** values using the **category-level median** (more outlier-resistant than the mean)
- Standardized all column names to `lower_snake_case`
- Renamed `purchase_amount_(usd)` → `purchase_amount` for cleaner downstream access
- Dropped `promo_code_used` after confirming it was **100% identical** to `discount_applied` — redundant signal
- **Feature engineered:**
  - `age_group` → binned into *Young Adult (≤25), Adult (26–40), Middle-Aged (41–60), Senior (61+)*
  - `purchase_frequency_days` → mapped text cadence (`Weekly`, `Monthly`, `Quarterly`...) to a numeric day interval
- Loaded the cleaned dataset into a MySQL database (`customers` table) via SQLAlchemy for structured querying

### 2️⃣ Data Analysis (SQL)

Queried the `customers` table to answer three core questions:

```sql
-- Who are the customer segments?
SELECT age_group, COUNT(*) AS customers,
       ROUND(AVG(purchase_amount),2) AS avg_purchase_usd,
       ROUND(AVG(review_rating),2)   AS avg_review_rating
FROM customers
GROUP BY age_group
ORDER BY avg_purchase_usd DESC;

-- What does loyalty look like?
SELECT subscription_status, COUNT(*) AS customers,
       ROUND(AVG(previous_purchases),2) AS avg_previous_purchases
FROM customers
GROUP BY subscription_status;

-- What drives purchases?
SELECT category, COUNT(*) AS transactions,
       ROUND(SUM(purchase_amount),0) AS total_revenue
FROM customers
GROUP BY category
ORDER BY total_revenue DESC;
```

### 3️⃣ Visualization (Power BI)

An interactive one-page dashboard (`Customer_Behaviour_Dashboard.pbix`) built around:

- **KPI cards** — Number of Customers, Average Review Rating, Average Purchase Amount
- **Donut chart** — % of Customers by Subscription Status
- **Column charts** — Revenue & Sales by Category
- **Bar charts** — Revenue & Sales by Age Group
- **Slicers** — Subscription Status, Gender, Category, Shipping Type (fully cross-filtered)

---

## 💡 Key Insights

| Insight | Detail |
|---|---|
| 🧭 **Spend is uniform, not segment-driven** | Average basket ($57–$61) and satisfaction (3.7–3.8 / 5) barely vary by age, gender, subscription, or payment method |
| 👕 **Category concentration** | Clothing (44.7%, $104,264) + Accessories (31.8%, $74,200) = **76.5% of total revenue** |
| 🔁 **A deep loyal core** | **39.7%** of customers (1,549) have 30+ previous purchases — but their basket size ($60.03) matches the overall average, so loyalty shows up as *frequency*, not bigger baskets |
| 📬 **Soft subscription lift** | Subscribers show +1.0 more previous purchases than non-subscribers, but no larger basket — 73% of the base (2,847 customers) isn't subscribed at all |
| 🏷️ **Discounts don't lift basket size** | Discount users spend **$0.85 less** per transaction on average than non-users |
| 🚚 **Fulfillment affects satisfaction** | Standard & 2-Day Shipping post the highest ratings (3.82 / 3.77); Store Pickup is the lowest-rated channel (3.71) |
| 📅 **Mild seasonality** | Fall is strongest ($60,018 revenue); Summer is weakest ($55,777) — a ~7% swing |
| 💳 **No checkout risk** | All 6 payment methods sit within a narrow 612–677 transaction band |
| 🌎 **Dispersed geography** | Top 5 states (Montana, Illinois, California, Idaho, Nevada) are within a tight $200–300 revenue band of each other |

**Overall KPIs:** 3,900 customers · $233,081 total revenue · $59.76 avg. basket · 3.75/5 avg. rating · 25.35 avg. previous purchases

---

## 📊 Dashboard Preview

<div align="center">

*Add a screenshot of your Power BI dashboard here:*

```markdown
![Dashboard Preview](./Images/Dashboard.png)
```

</div>

---

## 📁 Repository Structure

```bash
customer-trends-data-analysis-SQL-Python-PowerBI/
│
├── Customer Behaviour Analysis.ipynb      # Python: cleaning, feature engineering, SQL load
├── customer_shopping_behavior.csv         # Raw source dataset (3,900 rows × 18 columns)
├── Customer_Behaviour_Dashboard.pbix      # Interactive Power BI dashboard
├── Business Problem  Document.pdf         # Original business brief & deliverables
└── README.md                              # You are here
```

---

## ▶️ How to Reproduce

```bash
# 1. Clone the repo
git clone https://github.com/theaditya24/customer-trends-data-analysis-SQL-Python-PowerBI.git
cd customer-trends-data-analysis-SQL-Python-PowerBI

# 2. Install dependencies
pip install pandas numpy matplotlib mysql-connector-python sqlalchemy pymysql

# 3. Launch the notebook
jupyter notebook "Customer Behaviour Analysis.ipynb"

# 4. Open the dashboard
# Requires Power BI Desktop (Windows) — open Customer_Behaviour_Dashboard.pbix
```

---

## ✅ Business Recommendations

- **Protect the core:** double down on Clothing & Accessories — they already carry 76.5% of revenue
- **Target behaviorally, not demographically:** age/gender/payment method don't predict spend — purchase frequency and previous-purchase count do
- **Convert the 73% non-subscribed base** with a structured subscription push
- **Audit discount ROI:** discounted baskets are smaller, not bigger — test narrower, time-boxed offers
- **Invest in fulfillment quality**, especially the Store Pickup experience
- **Build a dedicated retention track** for the 1,549 high-frequency loyal customers

---

## 🔮 Future Improvements

- [ ] Add cohort / RFM (Recency-Frequency-Monetary) segmentation
- [ ] Build a churn-prediction model on top of the engineered features
- [ ] Automate the Python → SQL → Power BI refresh pipeline
- [ ] A/B test the discount-ROI hypothesis surfaced in this analysis

---

## 👤 Author

**Aditya**
📂 [GitHub](https://github.com/theaditya24) · Data Analyst

If this project was useful or interesting, consider ⭐ starring the repo!
