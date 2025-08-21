# Customer Segmentation


/* Group customers into three segments based on their spending behaviours 
- VIP : Customers with atleast 12 months of history and spending more than $5000.
- Regular: Customers with atleast 12months of history but spending $ 5000 or less.
- New : Customers with a lifespan less than 12 months.
- And Find The Total Number Of Customers By Each Group
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
