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

*Solution:*

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

*Solution:*

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

*Solution:*

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

*Solution:*

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

*Solution:*

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

*Solution:*

 <img src ="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/49d8a618-d4f6-455e-acab-6eb484461a27" width = "200">

 #### Question #7: Which item was purchased just before the customer became a member?

 ```SQL
WITH ranked_table AS (
SELECT sales.customer_id, 
order_date,
join_date,
product_name,
DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY(order_date - join_date) DESC) AS Ranking
FROM sales
JOIN members ON sales.customer_id = members.customer_id
JOIN menu ON menu.product_id = sales.product_id
WHERE order_date < join_date
)

SELECT customer_id,
product_name 
FROM ranked_table
WHERE Ranking = 1;
```
Steps:
- In a very similar fashion as the previous question,reate a Common Table Expression (CTE) named `ranked_table` in which you utilize the window function **DENSE_RANK** partitioned over `customer_id` ordered by `order_date - join_date` (effectively creating rankings based on the difference of the time from the date a customer ordered an item and the date that they became a member)
- However we would like the item purchased *before* the customer became a member, thus we can reverse the conditional statement in the **WHERE** clause to: `order_date < join_date`
- Query the CTE in which the Ranking = 1 to get the first ranking, i.e the item that was purchased right before membership aquisition

*Solution:*

<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/3c6c6dde-e631-4ba5-b218-361aa471567d" width = "200">

#### Question #8: What is the total items and amount spent for each member before they became a member?

```SQL
SELECT sales.customer_id,
COUNT(DISTINCT(product_name)) AS Total_Items,
SUM(price) AS Amount_Spent
FROM sales
JOIN menu ON sales.product_id = menu.product_id
JOIN members ON sales.customer_id = members.customer_id
WHERE order_date < join_date
GROUP BY customer_id;
```

Steps:
- **JOIN** all three tables on their respective relations to obtain necessary information
- **GROUP BY** the `customer_id` and aggregate the **SUM** of the `price` and the **COUNT** of the amount of distinct `product_names`
- Filter the table by ensuring all the values are when the individual was not yet a member using the **WHERE** clause

  *Solution:*

 <img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/7c4a7914-ffef-4633-a454-f4dee9f9a7da)" width="200">

 #### Question #9: If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have? 

```SQL
SELECT customer_id,
SUM(CASE
     WHEN product_name = 'sushi' THEN (price * 20)
     ELSE price * 10
END) AS points
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id;
```

Steps:
- Use the **CASE** statement to create a new column that calculates the points of each customer correctly (noting the double points for a sushi sale)
- **JOIN** the sales and menu tables to get the appropriate information
- **GROUP BY** the `customer_id` and aggregate by the sum of the **CASE** statement

*Solution:*

<img src ="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/7d6ad232-1f6f-434a-88e6-fbccd94f150d" width = "200">

#### Question #10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

```SQL
SELECT sales.customer_id,
SUM(CASE
     WHEN order_date >= join_date
       AND (order_date <= join_date + INTERVAL 7 DAY) OR product_name = 'sushi'
       AND MONTH(order_date) = 1 THEN (price * 20)
     ELSE price * 10
	END) AS points
FROM sales
JOIN members ON sales.customer_id = members.customer_id
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY customer_id;
```

Steps:
- Create a **CASE** statement in which it calculates the points of a given customer, ensuring that the points are doubled for sales within the first week of membership or the product purchased was sushi
- **JOIN** the sales and members data tables to obtain the required information
- **GROUP BY** and **ORDER BY** the `customer_id` to find the amount of points for each individual customer

*Solution:*

<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/4d5c7bc1-c0a2-4d73-94ce-261d7b823e48" width="200">



