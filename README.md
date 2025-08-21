# Customer Segmentation

## Project Overview
This project involves customer segmentation based on their spending behavior and customer lifetime.
Using SQL, customers are grouped into three distinct segments:
- VIP: Customers with at least 12 months of transaction history and total spending over $5,000.

- Regular:Customers with at least 12 months of history but total spending of $5,000 or less.

- New: Customers with less than 12 months of activity.

## Objective
The objective is to classify each customer into one of these categories and compute the total number of customers in each segment. 
This segmentation can support targeted marketing strategies, customer value analysis, and retention planning.

## SQL Queries
```sql
/* Computing the lifespans for each customer */
WITH customer_spending AS (
SELECT 
c.customer_key,
SUM(f.sales_amount) AS total_spending,
MIN(f.order_date) AS first_order,
MAX(f.order_date)AS last_order,
EXTRACT(month FROM  AGE(MAX(f.order_date), MIN(f.order_date)))  AS lifespans
FROM public."gold.fact_sales" AS f
LEFT JOIN public."gold.dim_customers" AS c
ON f.customer_key = c.customer_key
GROUP BY c.customer_key
)

/* Find the total number of customers by each group */
SELECT 
customer_key,
total_spending,
lifespans,
CASE WHEN lifespans >= 12 AND total_spending > 5000 THEN 'VIP'
     WHEN lifespans >= 12 AND total_spending <= 5000 THEN 'Regular'
	 ELSE 'New'
END AS customer_segment,
COUNT (customer_key) AS total_customers
FROM customer_spending
GROUP BY customer_segment;

/* Categorising the total customers to each  customer segment */
SELECT
customer_segment,
COUNT(customer_key) AS total)customers

FROM (
SELECT
customer_key,
CASE WHEN lifespans >= 12 AND total_spending > 5000 THEN 'VIP'
     WHEN lifespans >= 12 AND total_spending <= 5000 THEN 'Regular'
	 ELSE 'New'
END AS customer_segment
FROM customer_spending) AS t
GROUP BY customer_segment
GROUP BY customer_segment;
ORDER BY total_customers DESC;
```
## Summary Table Of Insights

| Customer\_Segment | Total\_Customers |
| ----------------- | ---------------- |
| New               | 14,631           |
| Regular           | 2,198            |
| VIP               | 1,655            |

- New customers dominate the dataset (over 70%), suggesting strong acquisition but potential retention challenges.
- Regular customers are limited, which might indicate drop-offs before loyalty is built.
- VIP customers are the smallest group, but nurturing them could generate higher revenue per customer.
  ## Recommendations & Next Moves
-  Retention Programs for New Customers: Launch welcome campaigns and onboarding journeys to convert new customers into regulars.
Offer first-purchase discounts, email nurturing, and app notifications to reduce churn risk.
- Loyalty Programs for Regular Customers:Introduce point-based rewards or tier upgrades to encourage repeat purchases.
Personalized promotions (based on purchase history) can help move them toward the VIP segment.
- VIP Customer Engagement: Provide exclusive perks (priority service, early access to products, dedicated support).
Encourage referrals from VIPs to acquire high-value new customers.
- Customer Lifecycle Monitoring:Continuously track how customers move between segments (e.g., New → Regular → VIP).
Use churn prediction models to flag customers at risk of dropping off early.



