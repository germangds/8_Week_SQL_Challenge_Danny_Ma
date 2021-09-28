# [8-Week SQL Challenge](https://8weeksqlchallenge.com) <img src="https://s3.amazonaws.com/thinkific-import/357412/n0nS0vA3RmOtzsH99jyf_Data_With_Danny_Round_Logo_png" align="right" width="120" />

[![My Profile](https://img.shields.io/badge/View-My_Profile-blue?logo=GitHub)](https://github.com/germangds)
[![Repositories](https://img.shields.io/badge/View-My_Repositories-red?logo=GitHub)](https://github.com/germangds?tab=repositories)

# 🍜 Case Study Nº1 - Danny's Diner <p align="center"> <img src="https://user-images.githubusercontent.com/11970888/130274690-935514f2-87d6-475e-a081-d72cd6fda26b.png" width=40% height=40%>

    
## Case Study Context
    
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.
    
---

## Problem Statement
    
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.
    
---
    
## Example Datasets
    
Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:

* sales
* menu
* members
    
You can inspect the entity relationship diagram (ERD) and example data below.

![image](https://user-images.githubusercontent.com/81607668/127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8.png) <p align="center">

---
    
## Dataset

In order to properly visualise the different attributes and tuples that we will be working with, I below am providing a quick snapshot of each dataset within the case study.
    
### **```sales```**

The sales table captures all ```customer_id``` level purchases with an corresponding ```order_date``` and ```product_id``` information for when and what menu items were ordered.

|customer_id|order_date|product_id|
|-----------|----------|----------|
|A          |2021-01-01|1         |
|A          |2021-01-01|2         |
|A          |2021-01-07|2         |
|A          |2021-01-10|3         |
|A          |2021-01-11|3         |
|A          |2021-01-11|3         |
|B          |2021-01-01|2         |
|B          |2021-01-02|2         |
|B          |2021-01-04|1         |
|B          |2021-01-11|1         |
|B          |2021-01-16|3         |
|B          |2021-02-01|3         |
|C          |2021-01-01|3         |
|C          |2021-01-01|3         |
|C          |2021-01-07|3         |
    
 ### **```menu```**
    
 The menu table maps the ```product_id``` to the actual ```product_name``` and price of each menu item.

|product_id |product_name|price     |
|-----------|------------|----------|
|1          |sushi       |10        |
|2          |curry       |15        |
|3          |ramen       |12        |

### **```members```**
    
The final members table captures the ```join_date``` when a ```customer_id``` joined the beta version of the Danny’s Diner loyalty program.

|customer_id|join_date |
|-----------|----------|
|A          |1/7/2021  |
|B          |1/9/2021  |
    
---
    
## Case Study Questions
    
1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
11 Bonus Question. Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL Recreate the following table output using the available data.
12 Bonus Question. Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

---

## Solutions

### **Q1. What is the total amount each customer spent at the restaurant?**
```sql
SELECT 
    s.customer_id,
    SUM(m.price) AS total_sales 
FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
    ON s.product_id=m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
```
***Output***

| customer_id | total_spent |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

---

### **Q2. How many days has each customer visited the restaurant?**
```sql
WITH cte AS (
    SELECT 
        customer_id,
        order_date 
    FROM dannys_diner.sales 
    GROUP BY customer_id,order_date)
SELECT customer_id,
       COUNT(order_date) AS visited_days 
FROM cte
GROUP BY customer_id
ORDER BY customer_id;
```
***Output***

|customer_id|visited_days|
|-----------|------------|
|A          |4           |
|B          |6           |
|C          |2           |


---

### **Q3. What was the first item from the menu purchased by each customer?**

```sql
WITH cte AS(
    SELECT 
        customer_id, 
        order_date, 
        product_id, 
        ROW_NUMBER() OVER(
            PARTITION BY 
                customer_id 
            ORDER BY 
                customer_id, 
                order_date) AS rank 
        FROM dannys_diner.sales)
SELECT 
    s.customer_id,
    m.product_name 
FROM cte s INNER JOIN dannys_diner.menu m 
    ON s.product_id=m.product_id
WHERE rank=1;
```

***Output***

| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

---

### **Q4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
```sql
WITH cte AS(
    SELECT 
        m.product_name,
        COUNT(*) AS product_count 
    FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
        ON s.product_id=m.product_id 
    GROUP BY m.product_name 
    ORDER BY product_count DESC)
SELECT 
    product_name 
FROM cte
LIMIT 1;
```

***Output***

|product_name|
|------------|
|ramen       |

---

### **Q5. Which item was the most popular for each customer?**
```sql
WITH cte AS(
    SELECT 
        s.customer_id,
        m.product_name,
        COUNT(*) AS product_count 
     FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
        ON s.product_id=m.product_id 
     GROUP BY s.customer_id,m.product_name)
SELECT 
    v.customer_id, 
    v.product_name 
FROM (SELECT 
    *, 
    RANK() OVER(
        PARTITION BY 
            customer_id 
        ORDER BY 
            customer_id,
            product_count DESC) AS rank 
        FROM cte) v
WHERE v.rank=1;
```

***Output***

|customer_id|product_name|
|-----------|------------|
|A          |ramen       |
|B          |sushi       |
|B          |curry       |
|B          |ramen       |
|C          |ramen       |


---

### **Q6. Which item was purchased first by the customer after they became a member?**
```sql
WITH cte AS (
    SELECT 
        s.customer_id,
        s.order_date,
        m.product_name,
        RANK() OVER(
            PARTITION BY 
                s.customer_id ORDER BY s.order_date) AS rank 
        FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
            ON s.product_id=m.product_id INNER JOIN dannys_diner.members mem 
            ON s.customer_id=mem.customer_id 
        WHERE s.order_date >= mem.join_date::DATE)
SELECT 
    DISTINCT customer_id,
    order_date,
    product_name 
FROM cte
WHERE rank=1;
```

***Output***

| customer_id | order_date | product_name |
| ----------- | ---------- | ------------ |
| A           | 2021-01-07 | curry        |
| B           | 2021-01-11 | sushi        |

---

### **Q7. Which item was purchased just before the customer became a member? **

```sql
WITH cte AS (
    SELECT 
        s.customer_id,
        s.order_date,
        m.product_name,
        RANK() OVER(
            PARTITION BY 
                s.customer_id 
            ORDER BY s.order_date DESC) AS rank 
     FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
         ON s.product_id=m.product_id INNER JOIN dannys_diner.members mem 
         ON s.customer_id=mem.customer_id 
     WHERE s.order_date < mem.join_date::DATE)
SELECT 
    DISTINCT customer_id,
    product_name,
    order_date
FROM cte
WHERE rank=1;
```

***Output***

| customer_id | product_name | order_date |
| ----------- | ------------ | ---------- |
| A           | sushi        | 2021-01-01 |
| A           | curry        | 2021-01-01 |
| B           | sushi        | 2021-01-04 |

---

### **Q8. What is the total items and amount spent for each member before they became a member?**
```sql
SELECT 
    s.customer_id,
    COUNT(DISTINCT m.product_name) AS product_count, 
    SUM(m.price) AS amount_spent 
FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
    ON s.product_id=m.product_id INNER JOIN dannys_diner.members mem 
    ON s.customer_id=mem.customer_id 
WHERE s.order_date < mem.join_date::DATE
GROUP BY s.customer_id
```

***Output***

| customer_id | total_items | total_spent |
| ----------- | ----------- | ----------- |
| A           | 2           | 25          |
| B           | 3           | 40          |


---

### **Q9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
```sql
WITH cte AS (
    SELECT 
        s.customer_id,
        m.product_name,
        m.price*10 AS price 
    FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
        ON s.product_id=m.product_id)
SELECT 
    customer_id, 
    SUM(
        (CASE WHEN product_name='sushi' THEN price*2 ELSE price END)) AS points FROM cte
GROUP BY customer_id
ORDER BY customer_id
```

***Output***    
    
| customer_id | total_points |
| ----------- | ------------ |
| A           | 860          |
| B           | 940          |
| C           | 360          |

---

### **Q10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
```sql
WITH cte as (
    SELECT 
        s.customer_id,
        s.order_date,
        m.product_name,
        m.price, 
        CASE 
            WHEN m.product_name='sushi' THEN m.price*20 
            WHEN s.order_date>=mem.join_date AND s.order_date<(mem.join_date+6) THEN m.price*20 
            ELSE m.price*10 END AS points 
      FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
          ON s.product_id=m.product_id INNER JOIN dannys_diner.members mem 
          ON s.customer_id=mem.customer_id)
SELECT 
    customer_id, 
    SUM(points) AS points 
FROM cte
GROUP BY customer_id
```

***Output***                                                       
                                                                                
| customer_id | points       |
| ----------- | ------------ |
| A           | 1370         |
| B           | 820          |
                                                                                
---

### **Q11 Bonus Question. Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL Recreate the following table output using the available data.**
```sql
SELECT 
    s.customer_id,
    s.order_date,
    m.product_name,
    m.price, 
    CASE 
        WHEN s.order_date>=mem.join_date THEN 'Y'
        ELSE 'N' END AS member 
FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
    ON s.product_id=m.product_id LEFT JOIN dannys_diner.members mem 
    ON s.customer_id=mem.customer_id
ORDER BY customer_id,order_date                                                                                
```

***Output***
    
| customer_id | order_date | product_name | price | member |
| ----------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |
    
---
    
### **Q12 Bonus Question. Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.
```sql
WITH cte AS (
    SELECT 
        s.customer_id,
        s.order_date,
        m.product_name,
        m.price, 
        CASE 
            WHEN s.order_date>=mem.join_date THEN 'Y' 
            ELSE 'N' END AS member 
    FROM dannys_diner.sales s INNER JOIN dannys_diner.menu m 
        ON s.product_id=m.product_id LEFT JOIN dannys_diner.members mem 
        ON s.customer_id=mem.customer_id
    ORDER BY customer_id,order_date)
SELECT 
    *,
    CASE 
        WHEN member='Y' THEN DENSE_RANK() OVER(PARTITION BY customer_id,member ORDER BY order_date) 
        ELSE null END AS ranking 
FROM cte
```
    
***Output***
 
| customer_id | order_date               | product_name | price | member | ranking |
| ----------- | ------------------------ | ------------ | ----- | ------ | ------- |
| A           | 2021-01-01T00:00:00.000Z | sushi        | 10    | N      | null    |
| A           | 2021-01-01T00:00:00.000Z | curry        | 15    | N      | null    |
| A           | 2021-01-07T00:00:00.000Z | curry        | 15    | Y      | 1       |
| A           | 2021-01-10T00:00:00.000Z | ramen        | 12    | Y      | 2       |
| A           | 2021-01-11T00:00:00.000Z | ramen        | 12    | Y      | 3       |
| A           | 2021-01-11T00:00:00.000Z | ramen        | 12    | Y      | 3       |
| B           | 2021-01-01T00:00:00.000Z | curry        | 15    | N      | null    |
| B           | 2021-01-02T00:00:00.000Z | curry        | 15    | N      | null    |
| B           | 2021-01-04T00:00:00.000Z | sushi        | 10    | N      | null    |
| B           | 2021-01-11T00:00:00.000Z | sushi        | 10    | Y      | 1       |
| B           | 2021-01-16T00:00:00.000Z | ramen        | 12    | Y      | 2       |
| B           | 2021-02-01T00:00:00.000Z | ramen        | 12    | Y      | 3       |
| C           | 2021-01-01T00:00:00.000Z | ramen        | 12    | N      | null    |
| C           | 2021-01-01T00:00:00.000Z | ramen        | 12    | N      | null    |
| C           | 2021-01-07T00:00:00.000Z | ramen        | 12    | N      | null    |   