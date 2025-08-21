# Customer Segmentation

This project involves customer segmentation using SQL to categorize users based on their spending behavior and customer lifespan. 
The objective was to group customers into meaningful segments for better business insights and decision-making.

Using transactional and customer data, the analysis segments customers into three categories:

VIP – Customers with at least 12 months of activity and total spending greater than $5,000.

Regular – Customers with at least 12 months of activity but total spending of $5,000 or less.

New – Customers with less than 12 months of history.

After segmenting, the query calculates the total number of customers in each group, enabling quick insight into the distribution of customer types.
*/
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
