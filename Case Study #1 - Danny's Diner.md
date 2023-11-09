# Case Study #1 - Danny's Diner

<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/119101ec-6715-4582-99d8-fcdd4a16c429" width = "450">

## 1. Introduction

Danny wants to use data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Provided are 3 key datasets : `sales`, `menu` and `members` to undergo the analysis.

## 2. Dataset Relationship Diagram

![DatasetRelationshipCase1](https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/b10c6fa5-149a-49c5-b95e-b6444c7c33b0)

## 3. The Data

Utilizing the GUI MySQLWorkbench, we can begin to understand and view the data using `SELECT` statements:

```SQL
SELECT * FROM members;
```
<img src= "https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/d3f489ea-e73e-4109-a23c-1430c1145ff7" width = "200">

```SQL
SELECT * FROM menu;
```

<img src ="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/d48b8023-f4c4-41e2-a2b0-ef44dcaafd4c" width = "200">

```SQL
SELECT * FROM sales
LIMIT 15;
```

<img src ="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/5ee6592e-7189-47f7-94cf-50973f46a3a9" width = "200">


## 4. Data Analysis 

Please Note: Analysis for this Case Study and all the following Case Studies in this repository are conducted by answering business questions provided by the data apprenticeship [here](https://8weeksqlchallenge.com/)!

### Question #1: What is the total amount each customer spent at the restaurant?

```SQL
SELECT customer_id,
SUM(price) AS total_spent FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY total_spent DESC;
```
Steps:
- Use **JOIN** to join the `sales` table with the `menu` table on the `product_id`
- **GROUP BY** the `customer_id` 
- **SUM** the price column to aggregate the groups and find the total amount spent by each customer.

Solution:

<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/1980fa4c-90c3-479f-9b0a-722df21bf9ba" width ="200">

#### Question #2: How many days has each customer visited the restaurant?

```SQL
SELECT customer_id, 
COUNT(DISTINCT order_date) AS NumOfVisits
FROM sales 
GROUP BY customer_id;
```
Steps:
- **GROUP BY** `customer_id`
- **COUNT** the **DISTINCT** `order_date` to determine the amount of unique days that the customer visited the restaurant

Solution:

<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/5bf74d4e-8cfc-41e7-b954-bff4bfc3f806" width = "200">

#### Question #3: What was the first item from the menu purchased by each customer?

```SQL
WITH ordered_sales AS(
SELECT customer_id,
	order_date,
	DENSE_RANK() OVER(PARTITION BY sales.customer_id ORDER BY order_date) AS my_rank,
    menu.product_name
FROM sales
JOIN menu ON menu.product_id = sales.product_id
)

SELECT customer_id,
product_name 
FROM ordered_sales
WHERE my_rank = 1
GROUP BY customer_id, product_name;
```
Steps: 
- Create a Common Table Expression (CTE) named `ordered_sales` in which you utilize the window function **DENSE_RANK** partitioned over `customer_id` ordered by `order_date` (effectively creating rankings of earliest date associated with each unique customer id)
- To obtain the first item purchased, we can **SELECT** the `product_name` from the CTE and use the **WHERE** clause to find the row in which the ranking is at the first position i.e: *WHERE my_rank = 1*\

Solution:

<img src ="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/5a611ae5-3a99-4612-bcf0-19d1189fd968" width="200">

#### Question #4: What is the most purchased item on the menu and how many times was it purchased by all customers?

```SQL
SELECT  
product_name,
COUNT(product_name) AS AmountOfTimesPurchased
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY product_name
ORDER BY AmountOfTimesPurchased DESC
LIMIT 1;
```

Steps: 
- ***JOIN** the `sales` and `menu` data tables to obtain the necessary information on product id and names
- **COUNT** the `product_name` to find the amount of purchases per group of product names
- Use **ORDER BY DESC** and **LIMIT** by 1 to find the top purchased item

Solution:

<img src= "https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/1f76a34c-ae00-400d-8acf-38ee80e330fb" width ="200">

#### Question #5:Which item was the most popular for each customer?

```SQL
WITH ordered_table AS (
SELECT customer_id,
product_name,
DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(sales.product_id)DESC) AS Ranking
FROM sales
JOIN menu ON menu.product_id = sales.product_id
GROUP BY customer_id, product_name
)

SELECT customer_id,
product_name,
FROM ordered_table 
WHERE Ranking = 1;
```

Steps:
- Create a Common Table Expression (CTE) named `ordered_table` in which you utilize the window function **DENSE_RANK** partitioned over `customer_id` ordered by `COUNT(sales.product_id)DESC` (effectively creating rankings of amount of products purchased for each customer)
- Query the CTE with the **WHERE** clause filters the rows to have the most popular item for the customer (Ranking = 1)

Solution:

<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/1fbe73e8-c78a-480b-a160-a9ee8ff382a7" width = "200">

#### Question #6: Which item was purchased first by the customer after they became a member?

```SQL
WITH ranked_table AS (
SELECT sales.customer_id, 
order_date,
join_date,
product_name,
DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY(order_date - join_date)) AS Ranking
FROM sales
JOIN members ON sales.customer_id = members.customer_id
JOIN menu ON menu.product_id = sales.product_id
WHERE join_date < order_date
)

SELECT customer_id,
product_name 
FROM ranked_table
WHERE Ranking = 1;
```
Steps:
- Create a Common Table Expression (CTE) named `ranked_table` in which you utilize the window function **DENSE_RANK** partitioned over `customer_id` ordered by `order_date - join_date` (effectively creating rankings based on the difference of the time from the date a customer ordered an item and the date that they became a member)
- Query the CTE in which the **WHERE** clause filters the rows to get the first item purchased after joining the membership (Ranking = 1)

Solution:

 <img src ="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/49d8a618-d4f6-455e-acab-6eb484461a27" width = "200">

 


