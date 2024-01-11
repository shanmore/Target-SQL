# Target-SQL

**Time period for which the data is given.**
*****************************************************************************
SELECT
order_id,
order_purchase_timestamp,
EXTRACT (YEAR FROM order_purchase_timestamp) as YEAR,
EXTRACT (QUARTER FROM order_purchase_timestamp) as QUARTER,
EXTRACT(MONTH FROM order_purchase_timestamp) as MONTH
FROM `Assignment.orders_dataset`
ORDER BY order_purchase_timestamp
LIMIT 10_
*************************************************************************

**Insights : -
YEAR WISE data from 2016 -2018.
QUARTER WISE – 3rd Quarter in 2016 – 4th Quarter in 2018
Month Wise – Sep 2016 to Oct 2018**
************************************************************************


**3. Cities and States of customers ordered during the given period.**
*********************************************************************
SELECT
c.customer_id,
order_id,
c.customer_city,
c.customer_state
FROM `Assignment.customers_dataset` c
JOIN `Assignment.orders_dataset` as o
ON c.customer_id = o.customer_id
WHERE EXTRACT(YEAR FROM order_purchase_timestamp) BETWEEN 2016 and 2018
ORDER BY c.customer_id
LIMIT 10
***********************************************************************************
**Insights :-
Incorrect city names have resulted in different rows if data is grouped by city.**
***********************************************************************************
**In-depth Exploration:**
**1. Is there a growing trend on e-commerce in Brazil? How can we describe a complete
scenario? Can we see some seasonality with peaks at specific months?**
********************************************************************************
SELECT
ROUND(SUM(p.payment_value),2) AS total_sum,
EXTRACT(YEAR FROM order_purchase_timestamp) as Years
FROM `Assignment.orders_dataset` as o
JOIN `Assignment.payments_dataset` as p
ON o.order_id = p.order_id
GROUP BY Years
ORDER BY total_sum ASC

SELECT
COUNT( DISTINCT c.customer_id) AS total_num_of_customers_per_year,
EXTRACT(YEAR FROM order_purchase_timestamp) as Years
FROM `Assignment.orders_dataset` as o
JOIN `Assignment.customers_dataset` as c
ON o.customer_id = c.customer_id
GROUP BY Years
ORDER BY total_num_of_customers_per_year ASC

**Insights :-
There is a definite increase in e commerce over the duration on which data is processed.
We can see an increase in total number of customers per year as well as increase in total
sum of payments per year.**
********************************************************************************************
SELECT
EXTRACT(YEAR from order_purchase_timestamp) as YEAR,
EXTRACT(MONTH from order_purchase_timestamp) as MONTH,
COUNT(order_id) as num_of_orders
FROM `Assignment.orders_dataset` as o
GROUP BY YEAR, MONTH
ORDER BY YEAR, MONTH
LIMIT 10

**Insights :-**
**With the growth of e commerce in Brazil from 2016 to 2018 more users start to place orders.
Hence we see an increase in number of orders each year with the month of Nov 2017 hitting the
peak in terms of total orders placed.
Max orders each year are placed during the 3nd and 4th Quarter i.e Between the Jul- Dec**
*********************************************************************************************
**2. What time do Brazilian customers tend to buy (Dawn, Morning, Afternoon or Night)?**

SELECT
COUNT(d.order_id) as num_orders,
d.Part_of_day
FROM
(
SELECT
order_id,
order_purchase_timestamp,
CASE
WHEN EXTRACT(TIME FROM order_purchase_timestamp) BETWEEN "05:00:00" AND "05:30:00"
THEN "Dawn"
WHEN EXTRACT(TIME FROM order_purchase_timestamp) BETWEEN "06:00:00" AND "11:59:59"
THEN "Morning"
WHEN EXTRACT(TIME FROM order_purchase_timestamp) BETWEEN "1:00:00" AND "16:59:59"
THEN "Afternoon"
WHEN EXTRACT(TIME FROM order_purchase_timestamp) BETWEEN "1:00:00" AND "18:59:59"
THEN "Evening"
ELSE "Night"
END AS Part_of_day
FROM `Assignment.orders_dataset`
ORDER BY order_purchase_timestamp
) as d
GROUP BY d.Part_of_day
ORDER BY num_orders

**Insights : -**
**Max customers tend to buy during afternoon and night.**
**********************************************************************************************
**3. Evolution of E-commerce orders in the Brazil region:
Get month on month orders by states.**

Month on month data comparison among all states.

SELECT
EXTRACT(MONTH from order_purchase_timestamp) as MONTH,
c.customer_state as States,
COUNT(order_id) as num_of_orders
FROM `Assignment.orders_dataset` as o
JOIN `Assignment.customers_dataset` as c
ON c.customer_id = o.customer_id
GROUP BY States, MONTH
ORDER BY MONTH,num_of_orders DESC
LIMIT 10
States : SP, RJ and MG lead in total number of orders per month for each month when data
checked over total duration available.
Month on month data comparison for each state separately.
SELECT*,
LAG(d.num_of_orders,1) OVER (PARTITION BY d.States ORDER BY d.MONTH) as prev_month_order,
LEAD(d.num_of_orders,1) OVER (PARTITION BY d.States ORDER BY d.MONTH) as next_month_order
FROM
(
SELECT DISTINCT
c.customer_state as States,
EXTRACT(MONTH from order_purchase_timestamp) as MONTH,
COUNT(order_id) as num_of_orders,
FROM `Assignment.orders_dataset` as o
JOIN `Assignment.customers_dataset` as c
ON c.customer_id = o.customer_id
GROUP BY States, MONTH
) as d
ORDER BY d.States, d.MONTH
LIMIT 10
**************************************************************************************************************
**2. Distribution of customers across the states in Brazil.
SELECT**

COUNT(DISTINCT customer_id) as num_of_customers_per_state,
customer_state
FROM `Assignment.customers_dataset`
GROUP BY customer_state
ORDER BY num_of_customers_per_state DESC
LIMIT 10
***********************************************************************************************************
**Impact on Economy: Analyze the money movement by e-commerce by
looking at order prices, freight and others.**

**1. Get % increase in cost of orders from 2017 to 2018 (include months between Jan to Augonly)**

SELECT *,
ROUND(((e.total_payments - e.previous_year_cost)/e.total_payments) * 100 ,2) as
cost_inc_precentage
FROM
(
SELECT *,
LAG(d.total_payments,1) OVER (ORDER BY d.Year) as previous_year_cost
FROM
(
SELECT
EXTRACT(YEAR from order_purchase_timestamp) as Year,
SUM(payment_value) as total_payments
FROM `Assignment.orders_dataset` as o
JOIN `Assignment.payments_dataset` as p
ON o.order_id = p.order_id
WHERE EXTRACT(YEAR from order_purchase_timestamp) IN(2017,2018) AND
EXTRACT(MONTH from order_purchase_timestamp) BETWEEN 1 AND 8
GROUP BY Year
ORDER BY Year
) as d
) as e
ORDER BY e.Year
2. Mean & Sum of price and freight value by customer state
SELECT
c.customer_state,
SUM(oi.price) as sum_price,
AVG(oi.price) as mean_price,
SUM(oi.freight_value) as freight_value_per_state
FROM `Assignment.order_items_dataset` as oi
JOIN `Assignment.orders_dataset` as o
ON oi.order_id = o.order_id
JOIN `Assignment.customers_dataset` as c
ON o.customer_id = c.customer_id
GROUP BY c.customer_state
ORDER BY c.customer_state
LIMIT 10
5. Analysis on sales, freight and delivery time
SELECT
order_status,
COUNT(order_id) as num_of_orders
FROM `Assignment.orders_dataset`
GROUP BY order_status
ORDER BY order_status

**We see that we have different number of orders based on their order status. It is found when the
date in order_dataset is ordered by order status. Only orders with status as delivered, shipped
and some orders which were cancelled after the product was shipped have an actual date value
order_deliver_carrier_date populated for them in the table. Rest all orders have NULL. This
means only the above mentioned orders have freight value populated for them. Hence in the
below query while calculating freight value we have filtered the status condition to meet this
requirement.**
**************************************************************************************************************
**1. Calculate days between purchasing, delivering and estimated delivery**

SELECT
order_status,
order_purchase_timestamp,
order_estimated_delivery_date,
DATE_DIFF(order_estimated_delivery_date,order_purchase_timestamp, DAY) as
est_del_time,
order_delivered_customer_date,
DATE_DIFF(order_delivered_customer_date,order_purchase_timestamp, DAY) as
act_del_time,
DATE_DIFF(order_estimated_delivery_date, order_delivered_customer_date, DAY) as
order_delayed_by
FROM `Assignment.orders_dataset`
WHERE order_status IN ("delivered", "shipped", "cancelled") AND
order_delivered_carrier_date IS NOT NULL
ORDER BY order_purchase_timestamp
LIMIT 10
****************************************************************************************************

**2. Find time_to_delivery & diff_estimated_delivery.**


SELECT
c.customer_state,
ROUND(AVG(DATE_DIFF(order_estimated_delivery_date,order_purchase_timestamp,
DAY)),2) as time_to_delivery,
ROUND(AVG(DATE_DIFF(order_estimated_delivery_date,order_delivered_customer_date,
DAY)),2) as diff_estimated_delivery,
ROUND(AVG(oi.freight_value),2) as avg_freight_value
FROM `Assignment.orders_dataset` as o
INNER JOIN `Assignment.order_items_dataset` as oi
ON o.order_id = oi.order_id
INNER JOIN `Assignment.customers_dataset` as c
ON o.customer_id = c.customer_id
WHERE order_status IN ("delivered", "shipped", "cancelled") AND
order_delivered_carrier_date IS NOT NULL
GROUP BY c.customer_state
ORDER BY avg_freight_value
LIMIT 10


**Top 5 states with highest/lowest average freight value - sort in desc/asc limit 5**

Same query as above except ORDER BY avg_freight_value DESC in last second line and
LIMIT 5 after that.


**7. Top 5 states with highest/lowest average time to delivery**

SELECT
c.customer_state,
ROUND(AVG(DATE_DIFF(order_estimated_delivery_date,order_purchase_timestamp,
DAY)),2) as avg_time_to_delivery,
ROUND(AVG(DATE_DIFF(order_estimated_delivery_date,order_delivered_customer_date,
DAY)),2) as avg_diff_estimated_delivery,
ROUND(AVG(oi.freight_value),2) as avg_freight_value
FROM `Assignment.orders_dataset` as o
INNER JOIN `Assignment.order_items_dataset` as oi
ON o.order_id = oi.order_id
INNER JOIN `Assignment.customers_dataset` as c
ON o.customer_id = c.customer_id
WHERE order_status IN ("delivered", "shipped", "cancelled") AND
order_delivered_carrier_date IS NOT NULL
GROUP BY c.customer_state
ORDER BY avg_time_to_delivery
LIMIT 5


**Highest time to delivery.**

Same query as above, except ORDER BY avg_time_to_delivery DESC in last second line.

**8. Top 5 states where delivery is really fast/ not so fast compared to estimated date.**

SELECT
c.customer_state,
ROUND(AVG(DATE_DIFF(order_estimated_delivery_date,order_purchase_timestamp,
DAY)),2) as avg_time_to_delivery,
ROUND(AVG(DATE_DIFF(order_estimated_delivery_date,order_delivered_customer_date,
DAY)),2) as avg_diff_estimated_delivery,
ROUND(AVG(oi.freight_value),2) as avg_freight_value
FROM `Assignment.orders_dataset` as o
INNER JOIN `Assignment.order_items_dataset` as oi
ON o.order_id = oi.order_id
INNER JOIN `Assignment.customers_dataset` as c
ON o.customer_id = c.customer_id
WHERE order_status IN ("delivered", "shipped", "cancelled") AND
order_delivered_carrier_date IS NOT NULL
GROUP BY c.customer_state
ORDER BY avg_diff_estimated_delivery
LIMIT 5

**Delivery not so fast : -**

Same query except ORDER BY avg_diff_estimated_deliveryDESC in last second line.

**Payment type analysis:**

**1. Month over Month count of orders for different payment types**
SELECT*,
LAG(d.num_orders,1) OVER (PARTITION BY d.payment_type ORDER BY
d.payment_type,d.Month) as previous_month_orders,
LEAD(d.num_orders,1) OVER (PARTITION BY d.payment_type ORDER BY
d.payment_type,d.Month) as next_month_orders
FROM
(
SELECT
payment_type,
EXTRACT(MONTH FROM o.order_purchase_timestamp) as Month,
COUNT(o.order_id) as num_orders
FROM `Assignment.payments_dataset` as p
JOIN `Assignment.orders_dataset` as o
ON o.order_id = p.order_id
GROUP BY payment_type, Month
ORDER BY payment_type, Month
) as d
ORDER by d.payment_type, d.Month
2. Count of orders based on the no. of payment instalments.
SELECT
payment_installments,
COUNT(o.order_id) as num_orders
FROM `Assignment.payments_dataset` as p
JOIN `Assignment.orders_dataset` as o
ON o.order_id = p.order_id
GROUP BY payment_installments
ORDER BY payment_installments
LIMIT 10
