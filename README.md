# Target-SQL

Target is a globally renowned brand and a prominent retailer in the United States. Target makes itself a preferred shopping destination by offering outstanding value, inspiration, innovation and an exceptional guest experience that no other retailer can deliver.

This particular business case focuses on the operations of Target in Brazil and provides insightful information about 100,000 orders placed between 2016 and 2018. The dataset offers a comprehensive view of various dimensions including the order status, price, payment and freight performance, customer location, product attributes, and customer reviews.

By analyzing this extensive dataset, it becomes possible to gain valuable insights into Target's operations in Brazil. The information can shed light on various aspects of the business, such as order processing, pricing strategies, payment and shipping efficiency, customer demographics, product characteristics, and customer satisfaction levels.

**Problem Statement:**

Analyze the given dataset to extract valuable insights and provide actionable recommendations.


1. Import the dataset and do usual exploratory analysis steps like checking the structure & characteristics of the dataset:
2. Data type of all columns in the "customers" table.
3. Get the time range between which the orders were placed.
4. Count the Cities & States of customers who ordered during the given period.

**In-depth Exploration:**

1. Is there a growing trend in the no. of orders placed over the past years?
2. Can we see some kind of monthly seasonality in terms of the no. of orders being placed?
3. During what time of the day, do the Brazilian customers mostly place their orders? (Dawn, Morning, Afternoon or Night)
   0-6 hrs : Dawn
   7-12 hrs : Mornings
   13-18 hrs : Afternoon
   19-23 hrs : Night

**Evolution of E-commerce orders in the Brazil region:**

1. Get the month on month no. of orders placed in each state.
2. How are the customers distributed across all the states?
3. Impact on Economy: Analyze the money movement by e-commerce by looking at order prices, freight and others.
4. Get the % increase in the cost of orders from year 2017 to 2018 (include months between Jan to Aug only).
4. You can use the "payment_value" column in the payments table to get the cost of orders.
5. Calculate the Total & Average value of order price for each state.
6. Calculate the Total & Average value of order freight for each state.
7. Analysis based on sales, freight and delivery time.
8. Find the no. of days taken to deliver each order from the order’s purchase date as delivery time.
9. Also, calculate the difference (in days) between the estimated & actual delivery date of an order.

You can calculate the delivery time and the difference between the estimated & actual delivery date using the given formula:
_time_to_deliver = order_delivered_customer_date - order_purchase_timestamp
diff_estimated_delivery = order_delivered_customer_date - order_estimated_delivery_date

1. Find out the top 5 states with the highest & lowest average freight value.
2. Find out the top 5 states with the highest & lowest average delivery time.
3. Find out the top 5 states where the order delivery is really fast as compared to the estimated date of delivery.

**Analysis based on the payments:**

1. Find the month on month no. of orders placed using different payment types.
2. Find the no. of orders placed on the basis of the payment installments that have been paid.
___________________________________________________________________________________________________________

