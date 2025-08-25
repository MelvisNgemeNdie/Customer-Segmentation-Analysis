## Customer Segmentation Analysis
### Project Overview
This project involves customer segmentation based on their spending behavior and customer lifetime.
Using SQL, customers are grouped into three distinct segments:
- **VIP**: Customers with at least 12 months of transaction history and total spending over $5,000.
- **Regula**r:Customers with at least 12 months of history but total spending of $5,000 or less.
- **New**: Customers with less than 12 months of activity.
### Objective
The objective is to classify each customer into one of these categories and compute the total number of customers in each segment. 
This segmentation can support targeted marketing strategies, customer value analysis, and retention planning.
### SQL Queries
```sql
/* Computing the lifespans for each customer */
WITH customer_spending AS (
SELECT 
c.customer_key,
SUM(f.sales_amount) AS total_spending,
MIN(f.order_date) AS first_order,
MAX(f.order_date)AS last_order,
EXTRACT(YEAR FROM AGE(MAX(order_date), MIN(order_date))) * 12 +
        EXTRACT(MONTH FROM AGE(MAX(order_date), MIN(order_date))) AS lifespans
FROM public."gold.fact_sales" AS f
LEFT JOIN public."gold.dim_customers" AS c
ON f.customer_key = c.customer_key
GROUP BY c.customer_key
),

/* Find the total number of customers by each group */
customer_segment AS (
SELECT 
customer_key,
total_spending,
lifespans,
CASE WHEN lifespans >= 12 AND total_spending > 5000 THEN 'VIP'
     WHEN lifespans >= 12 AND total_spending <= 5000 THEN 'Regular'
	 ELSE 'New'
END AS customer_group
FROM customer_spending
)
/* Categorising the total customers to each  customer segment */

SELECT
    customer_group,
    COUNT(*) AS total_customers
FROM customer_segment
GROUP BY customer_group
ORDER BY total_customers DESC;

/* What this does */
1. customer_spending CTE
- Calculates each customer’s total_spending.
- Finds their lifespans (difference between first and last order date).

2. customer_segment CTE
- Applies your rules with a CASE statement.

3. Final SELECT
- Groups by segment (VIP, Regular, New).
- Counts customers in each.

```
### Summary Table Of Insights

| Customer\_ Group | Total\_ Customers | % of Total (\~18.5K) |
| --------------   | ---------------   | -------------------- |
| **New**          | 14,828            | \~80%                |
| **Regular**      | 2,037             | \~11%                |
| **VIP**          | 1,619             | \~9%                 |

- 80% (14,828) of customers are New (<12 months lifespan) suggesting strong acquisition but low retention.
- 11% (2,037) are Regulars (≤ €5000 spend, ≥12 months) suggesting stable but low-value long-term base.
- 9% (1,619) are VIPs (> €5000 spend, ≥12 months) suggesting high-value customers, critical to overall revenue despite being the smallest segment.

### Recommendations & Next Moves
#### Retention Programs for New Customers
- Launch welcome campaigns and onboarding journeys to convert new customers into regulars.
- Offer first-purchase discounts, email nurturing, and app notifications to reduce churn risk.
#### Loyalty Programs for Regular Customers
- Introduce point-based rewards or tier upgrades to encourage repeat purchases.
- Personalized promotions (based on purchase history) can help move them toward the VIP segment.
#### VIP Customer Engagement
- Provide exclusive perks (priority service, early access to products, dedicated support).
- Encourage referrals from VIPs to acquire high-value new customers.
#### Customer Lifecycle Monitoring
- Continuously track how customers move between segments (e.g., New → Regular → VIP).
- Use churn prediction models to flag customers at risk of dropping off early.
### Skills Highlighted
- **SQL Joins & Data Integration** – Combined multiple tables using `LEFT JOIN`.  
- **Aggregate Functions** – Applied `SUM()`, `MIN()`, `MAX()`, `COUNT()` for customer KPIs.  
- **Date Functions** – Used `AGE()` & `EXTRACT()` to compute customer lifespans.  
- **Conditional Logic** – Applied `CASE WHEN` statements for customer segmentation.  
- **CTEs** – Structured queries with `WITH` statements for clarity.  
- **Data Cleaning & Accuracy** – Ensured consistent grouping and calculations.  
- **Business Intelligence** – Translated raw data into customer lifecycle insights.
### Tools & Technologies
- SQL (PostgreSQL)
- Excel / CSV (for data handling)
- GitHub (Version Control & Portfolio Presentation)
