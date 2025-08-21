# Customer Segmentation

This project involves customer segmentation based on their spending behavior and customer lifetime.
Using SQL, customers are grouped into three distinct segments:

##### . VIP: Customers with at least 12 months of transaction history and total spending over $5,000.

##### . Regular:Customers with at least 12 months of history but total spending of $5,000 or less.

##### . New: Customers with less than 12 months of activity.

## Objective
The objective is to classify each customer into one of these categories and compute the total number of customers in each segment. 
This segmentation can support targeted marketing strategies, customer value analysis, and retention planning.
*/ SQL Queries
```sql
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

SELECT 
customer_key,
total_spending,
lifespans,
CASE WHEN lifespans >= 12 AND total_spending > 5000 THEN 'VIP'
     WHEN lifespans >= 12 AND total_spending <= 5000 THEN 'Regular'
	 ELSE 'New'
END AS customer_segment
FROM customer_spending
