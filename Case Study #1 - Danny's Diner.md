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

Please Note: Analysis for this Case Study and all the following Case Studies in this repository are conducted by answering business questions provided by the data apprenticeship [here!](https://8weeksqlchallenge.com/)

Question #1: What is the total amount each customer spent at the restaurant?

```SQL
SELECT customer_id, SUM(price) AS total_spent FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY total_spent DESC;
```
<img src="https://github.com/dylanviyar/8WeekSQLChallenge/assets/81194849/1980fa4c-90c3-479f-9b0a-722df21bf9ba" width ="200">



