# Customer Churn: Data Exploration & Preparation in SQL

Profiling and preparing a telecom customer churn dataset in **SQL Server** to feed a Power BI churn dashboard. The dataset covers customer demographics, contract and payment type, subscribed services, charges, revenue, and churn status and reason.

**Code:** [`churn_data_exploration.sql`](churn_data_exploration.sql)

## Approach
| Step | What was done | SQL techniques |
|---|---|---|
| 1. Profile distributions | Share of customers by gender, contract type, and state; customer count and **share of total revenue** by status (Churned / Stayed / Joined) | `GROUP BY`, scalar subqueries for percentages |
| 2. Audit missing data | Counted nulls in all 32 columns with a single query | `SUM(CASE WHEN … IS NULL THEN 1 ELSE 0 END)` |
| 3. Clean and load to production | Replaced nulls with business-meaningful defaults (for example, no internet add-on becomes "No", and an unknown churn reason becomes "Others"), then loaded the result into `prod_Churn` | `ISNULL`, `SELECT … INTO` |
| 4. Serve to Power BI | Created a view of existing customers (churned vs. stayed) to model churn, and a view of newly joined customers to score | `CREATE VIEW` |

## Why it's structured this way
- **Staging vs. production tables:** raw data stays untouched in `stg_Churn`, and reports read only the cleaned `prod_Churn`.
- **Two views:** customers who have already churned or stayed are labeled data for analysis, while newly joined customers have no outcome yet. Keeping them apart prevents mixing the two groups.
