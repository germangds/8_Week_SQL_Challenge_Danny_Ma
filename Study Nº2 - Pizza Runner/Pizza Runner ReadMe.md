# [8-Week SQL Challenge](https://8weeksqlchallenge.com) <img src="https://s3.amazonaws.com/thinkific-import/357412/n0nS0vA3RmOtzsH99jyf_Data_With_Danny_Round_Logo_png" align="right" width="120" />

[![My Profile](https://img.shields.io/badge/View-My_Profile-blue?logo=GitHub)](https://github.com/germangds)
[![Repositories](https://img.shields.io/badge/View-My_Repositories-red?logo=GitHub)](https://github.com/germangds?tab=repositories)

# 🍕 Case Study Nº2 - Pizza Runner <p align="center"> <img src="https://user-images.githubusercontent.com/81607668/127271856-3c0d5b4a-baab-472c-9e24-3c1e3c3359b2.png" width=40% height=40%>

    
## Case Study Context
    
Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway…)

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.
     
---
    
## Example Datasets
    
has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

All datasets exist within the pizza_runner database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions.

Danny has shared with you 6 key datasets for this case study:

* runners
* runner_orders
* customer_orders
* pizza_names
* pizza_recipes
* pizza_toppings
    
You can inspect the entity relationship diagram (ERD) and example data below.

![image](https://user-images.githubusercontent.com/81607668/127271531-0b4da8c7-8b24-4a14-9093-0795c4fa037e.png) <p align="center">

---
    
## Dataset

In order to properly visualise the different attributes and tuples that we will be working with, I below am providing a quick snapshot of each dataset within the case study.
    
### **```runners```**

The runners table shows the ```registration_date``` for each new runner.

|runner_id|registration_date|
|---------|-----------------|
|1        |1/1/2021         |
|2        |1/3/2021         |
|3        |1/8/2021         |
|4        |1/15/2021        |
    
 ### **```customer_orders```**
    
Customer pizza orders are captured in the ```customer_orders``` table with 1 row for each individual pizza that is part of the order.
    
The ```pizza_id``` relates to the type of pizza which was ordered whilst the ```exclusions``` are the ```ingredient_id``` values which should be removed from the pizza and the ```extras``` are the ```ingredient_id``` values which need to be added to the pizza.

Note that customers can order multiple pizzas in a single order with varying ```exclusions``` and ```extras``` values even if the pizza is the same type!

The ```exclusions``` and ```extras``` columns will need to be cleaned up before using them in your queries.

|order_id|customer_id|pizza_id|exclusions|extras|order_time          |
|--------|-----------|--------|----------|------|--------------------|
|1       |101        |1       |          |      |2020-01-01T18:05:02 |
|2       |101        |1       |          |      |2021-01-01 19:00:52 |
|3       |102        |1       |          |      |2021-01-02 23:51:23 |
|3       |102        |2       |          |NaN   |2021-01-02 23:51:23 |
|4       |103        |1       |4         |      |2021-01-04 13:23:46 |
|4       |103        |1       |4         |      |2021-01-04 13:23:46 |
|4       |103        |2       |4         |      |2021-01-04 13:23:46 |
|5       |104        |1       |null      |1     |2021-01-08 21:00:29 |
|6       |101        |2       |null      |null  |2021-01-08 21:03:13 |
|7       |105        |2       |null      |1     |2021-01-08 21:20:29 |
|8       |102        |1       |null      |null  |2021-01-09 23:54:33 |
|9       |103        |1       |4         |1, 5  |2021-01-10 11:22:59 |
|10      |104        |1       |null      |null  |2021-01-11 18:34:49 |
|10      |104        |1       |2, 6      |1, 4  |2021-01-11 18:34:49 |

### **```runner_orders```**
    
After each orders are received through the system - they are assigned to a runner - however not all orders are fully completed and can be cancelled by the restaurant or the customer.

The ```pickup_time``` is the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the freshly cooked pizzas. The ```distance``` and ```duration``` fields are related to how far and long the runner had to travel to deliver the order to the respective customer.

There are some known data issues with this table so be careful when using this in your queries - make sure to check the data types for each column in the schema SQL!

|order_id|runner_id|pickup_time    |distance  |duration  |cancellation           |
|--------|---------|---------------|----------|----------|-----------------------|
|1       |1        |1/1/2021 18:15 |20km      |32 minutes|                       |
|2       |1        |1/1/2021 19:10 |20km      |27 minutes|                       |
|3       |1        |1/3/2021 0:12  |13.4km    |20 mins   |NaN                    |
|4       |2        |1/4/2021 13:53 |23.4      |40        |NaN                    |
|5       |3        |1/8/2021 21:10 |10        |15        |NaN                    |
|6       |3        |null           |null      |null      |Restaurant Cancellation|
|7       |2        |1/8/2020 21:30 |25km      |25mins    |null                   |
|8       |2        |1/10/2020 0:15 |23.4 km   |15 minute |null                   |
|9       |2        |null           |null      |null      |Customer Cancellation  |
|10      |1        |1/11/2020 18:50|10km      |10minutes |null                   |
    
### **```pizza_names```**

At the moment - Pizza Runner only has 2 pizzas available the Meat Lovers or Vegetarian!
    
|pizza_id|pizza_name |
|--------|-----------|
|1       |Meat Lovers|
|2       |Vegetarian |  
    
### **```pizza_recipes```**    

This table contains all of the ```topping_name``` values with their corresponding ```topping_id``` value.

|pizza_id|toppings               |
|--------|-----------------------|
|1       |1, 2, 3, 4, 5, 6, 8, 10| 
|2       |4, 6, 7, 9, 11, 12     |  
    
### **```pizza_toppings```**     

This table contains all of the ```topping_name``` values with their corresponding ```topping_id``` value.


|topping_id|topping_name|
|----------|------------|
|1         |Bacon       | 
|2         |BBQ Sauce   | 
|3         |Beef        |  
|4         |Cheese      |  
|5         |Chicken     |     
|6         |Mushrooms   |  
|7         |Onions      |     
|8         |Pepperoni   | 
|9         |Peppers     |   
|10        |Salami      | 
|11        |Tomatoes    | 
|12        |Tomato Sauce|
    
---
    
## Case Study Questions

This case study has LOTS of questions - they are broken up by area of focus including:

* Pizza Metrics
* Runner and Customer Experience
* Ingredient Optimisation
* Pricing and Ratings
* Bonus DML Challenges (DML = Data Manipulation Language)

Each of the following case study questions can be answered using a single SQL statement.

Again, there are many questions in this case study - please feel free to pick and choose which ones you’d like to try!

Before you start writing your SQL queries however - you might want to investigate the data, you may want to do something with some of those ```null``` values and data types in the ```customer_orders``` and ```runner_orders``` tables!
    
### A. Pizza Metrics
    
1. How many pizzas were ordered?
2. How many unique customer orders were made?
3. How many successful orders were delivered by each runner?
4. How many of each type of pizza was delivered?
5. How many Vegetarian and Meatlovers were ordered by each customer?
6. What was the maximum number of pizzas delivered in a single order?
7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
8. How many pizzas were delivered that had both exclusions and extras?
9. What was the total volume of pizzas ordered for each hour of the day?
10. What was the volume of orders for each day of the week?

### B. Runner and Customer Experience

1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
4. What was the average distance travelled for each customer?
5. What was the difference between the longest and shortest delivery times for all orders?
6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
7. What is the successful delivery percentage for each runner?

### C. Ingredient Optimisation

1. What are the standard ingredients for each pizza?
2. What was the most commonly added extra?
3. What was the most common exclusion?
4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
6. For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
7. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

### D. Pricing and Ratings

1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
2. What if there was an additional $1 charge for any pizza extras?
- Add cheese is $1 extra
3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.
4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas
5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?

### E. Bonus Questions

If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
    
---

## Solutions

### A. Pizza Metrics

### **Q1. How many pizzas were ordered?**
```sql
SELECT 
    COUNT(*) AS pizza_counter
FROM pizza_runner.customer_orders;
```
***Output***

|pizza_counter|
|-------------|
|14           |

### **Q2. How many unique customer orders were made?**
```sql
SELECT 
    COUNT(DISTINCT pizza_id) AS n_customers_orders
FROM pizza_runner.customer_orders;
```
***Output***

|n_customers_orders|
|------------------|
|10                |

### **Q3. How many successful orders were delivered by each runner?**
```sql
SELECT 
    runner_id, 
    COUNT(DISTINCT order_id) AS succesful_orders
FROM pizza_runner.runner_orders
WHERE pickup_time NOT LIKE 'null'
GROUP BY runner_id
ORDER BY runner_id;
```
***Output***

| runner_id | successful_orders |
|-----------|-------------------|
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

### **Q4. How many successful orders were delivered by each runner?**
```sql
WITH cte AS (
    SELECT 
        c.order_id,
        c.pizza_id,
        r.pickup_time 
    FROM pizza_runner.customer_orders c LEFT JOIN pizza_runner.runner_orders r 
        ON c.order_id=r.order_id)
SELECT 
    pizza_id,
    COUNT(*) AS pizza_counter FROM cte
WHERE pickup_time NOT LIKE 'null'
GROUP BY pizza_id;
```
***Output***

| pizza_name | pizza_counter |
|------------|---------------|
| Meatlovers | 9             |
| Vegetarian | 3             |

### **Q5. How many Vegetarian and Meatlovers were ordered by each customer?**
```sql
SELECT 
  customer_id, 
  SUM(CASE WHEN pizza_id = 1 THEN 1 ELSE 0 END) AS MeatLovers, 
  SUM(CASE WHEN pizza_id = 2 THEN 1 ELSE 0 END) AS Vegetarian 
FROM pizza_runner.customer_orders
GROUP BY customer_id;
```
***Output***

| customer_id | meatlovers | vegetarian |
|-------------|-------------|------------|
| 101         | 2           | 1          |
| 103         | 3           | 1          |
| 104         | 3           | 0          |
| 105         | 0           | 1          |
| 102         | 2           | 1          |

### **Q6. What was the maximum number of pizzas delivered in a single order?**
```sql
SELECT 
    r.order_id, 
    c.n_pizzas 
FROM pizza_runner.runner_orders r LEFT JOIN (SELECT order_id, COUNT(*) AS n_pizzas FROM pizza_runner.customer_orders GROUP BY order_id) c 
    ON r.order_id=c.order_id
WHERE r.pickup_time NOT LIKE 'null'
ORDER BY c.n_pizzas DESC
LIMIT 1;
```
***Output***

| order_id  | n_pizzas  |
|-----------|-----------|
| 4         | 3         |

### **Q7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
```sql
WITH cte AS (
    SELECT 
        c.customer_id, 
        CASE WHEN c.exclusions IN ('','null') THEN 0 ELSE 1 END AS exclusions, 
        CASE WHEN c.extras IN ('','null') OR c.extras IS NULL THEN 0 ELSE 1 END AS extras 
FROM pizza_runner.customer_orders c LEFT JOIN pizza_runner.runner_orders r 
    ON c.order_id=r.order_id WHERE r.pickup_time NOT LIKE 'null')
SELECT 
    customer_id, 
    SUM(CASE WHEN (exclusions=1 OR extras=1) OR (exclusions=1 AND extras=1) THEN 1 ELSE 0 END) AS at_least_one_change, 
    SUM(CASE WHEN (exclusions=0 AND extras=0) THEN 1 ELSE 0 END) AS no_changes 
FROM cte
GROUP BY customer_id
ORDER BY customer_id;
```
***Output***

| customer_id | at_least_one_change | no_changes |
|-------------|---------------------|------------|
| 101         | 0                   | 2          |
| 102         | 0                   | 3          |
| 103         | 3                   | 3          |
| 104         | 2                   | 2          |
| 105         | 1                   | 1          |

### **Q8. How many pizzas were delivered that had both exclusions and extras?**
```sql
WITH cte AS (
    SELECT 
        c.customer_id, 
        CASE WHEN c.exclusions IN ('','null') THEN 0 ELSE 1 END AS exclusions, 
        CASE WHEN c.extras IN ('','null') OR c.extras IS NULL THEN 0 ELSE 1 END AS extras 
    FROM pizza_runner.customer_orders c LEFT JOIN pizza_runner.runner_orders r 
        ON c.order_id=r.order_id WHERE r.pickup_time NOT LIKE 'null')
SELECT 
    COUNT(*) AS d_pizzas_exclusions_and_extras 
FROM cte
WHERE exclusions=1 AND extras=1;
```
***Output***

| d_pizzas_exclusions_and_extras |
|--------------------------------|
| 1                              |

