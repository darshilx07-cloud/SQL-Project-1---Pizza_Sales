# SQL-Project-1---Pizza_Sales

### Project Category: Beginner

![SQL](https://img.shields.io/badge/Language-SQL-blue?logo=sqlite)
![MySQL](https://img.shields.io/badge/Database-MySQL-orange?logo=mysql)
![Beginner Friendly](https://img.shields.io/badge/Level-Beginner-green)
![Project Type](https://img.shields.io/badge/Project-Data%20Analysis-yellow)


## Overview
This project presents an **Annual Pizza Sales SQL Report**, showcasing practical SQL queries applied to a real-world pizza sales dataset. It covers **Basic (5), Intermediate (5), and Advanced (3) SQL queries**, demonstrating key analytical tasks such as calculating total revenue, identifying top-selling pizzas, analyzing order distribution, and measuring category-wise and cumulative sales performance.

Through this project, I practiced query structuring, joins, grouping, and optimization while gaining hands-on experience in data analysis with SQL. The dataset and SQL scripts are included for reproducibility and learning.

```sql
-- CREATE TABLE ORDERS
(
ORDER_ID INT NOT NULL,
ORDER_DATE DATE NOT NULL,
ORDER_TIME TIME NOT NULL,
PRIMARY KEY (ORDER_ID)
);

CREATE TABLE ORDERS_DETAILS 
(
ORDER_DETAILS_ID INT NOT NULL,
ORDER_ID INT NOT NULL,
PIZZA_ID TEXT NOT NULL,
QUANTITY INT NOT NULL,
PRIMARY KEY (ORDER_DETAILS_ID)
);

```

---

## 13 Practice Questions

### Easy Level
1. Retrieve the total number of orders placed.
2. Calculate the total revenue generated from pizza sales.
3. Identify the highest-priced pizza.
4. Identify the most common pizza size ordered.
5. List the top 5 most ordered pizza types along with their quantities.

### Medium Level
1. Join the necessary tables to find the total quantity of each pizza category ordered.
2. Determine the distribution of orders by hour of the day.
3. Join relevant tables to find the category-wise distribution of pizzas.
4. Group the orders by date and calculate the average number of pizzas ordered per day.
5. Determine the top 3 most ordered pizza types based on revenue.

### Advanced Level
1. Calculate the percentage contribution of each pizza type to total revenue.
2. Analyze the cumulative revenue generated over time.
3. Determine the top 3 most ordered pizza types based on revenue for each pizza category.


---
### Easy Level Quries
1. Retrieve the total number of orders placed?
```sql
SELECT COUNT(ORDER_ID) 
AS TOTAL_ORDERS 
FROM ORDERS;
```

2. Calculate the total revenue generated from pizza sales?
```sql
SELECT ROUND(SUM(ORDER_DETAILS.QUANTITY * PIZZAS.PRICE), 0) 
AS TOTAL_PROFIT
FROM ORDER_DETAILS JOIN PIZZAS
ON PIZZAS.PIZZA_ID = ORDER_DETAILS.PIZZA_ID;
```

3. Identify the highest-priced pizza?
```sql
SELECT PIZZA_TYPES.NAME, PIZZAS.PRICE
FROM PIZZA_TYPES JOIN PIZZAS 
ON PIZZA_TYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
ORDER BY PIZZAS.PRICE DESC
LIMIT 1;
```

 4) Identify the most common pizza size ordered?
```sql
SELECT PIZZAS.SIZE, COUNT(ORDER_DETAILS.ORDER_DETAILS_ID) 
AS COUNT_ORDER
FROM PIZZAS JOIN ORDER_DETAILS
ON PIZZAS.PIZZA_ID = ORDER_DETAILS.PIZZA_ID
GROUP BY PIZZAS.SIZE 
ORDER BY COUNT_ORDER DESC
LIMIT 1;
```

5) List the top 5 most ordered pizza types along with their quantities?
```sql
SELECT PIZZA_TYPES.NAME,
COUNT(ORDER_DETAILS.QUANTITY) AS ORDER_QUA
FROM PIZZA_TYPES JOIN PIZZAS
ON PIZZA_TYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
JOIN ORDER_DETAILS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
GROUP BY PIZZA_TYPES.NAME 
ORDER BY ORDER_QUA DESC
LIMIT 5;
```


---
### Medium Level

1) Join the necessary tables to find the total quantity of each pizza category ordered
```sql
SELECT PIZZA_TYPES.CATEGORY,
COUNT(ORDER_DETAILS.QUANTITY) AS ORDER_QUA
FROM PIZZA_TYPES JOIN PIZZAS
ON PIZZA_TYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
JOIN ORDER_DETAILS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
GROUP BY PIZZA_TYPES.CATEGORY 
ORDER BY ORDER_QUA DESC;
```

2) Determine the distribution of orders by hour of the day?
```sql
SELECT HOUR(ORDER_TIME) AS ORDER_HRS, 
COUNT(ORDER_ID) AS ORDER_COUNTS 
FROM ORDERS
GROUP BY HOUR(ORDER_TIME);
```

3) Join relevant tables to find the category-wise distribution of pizzas?
```sql
SELECT PIZZA_TYPES.CATEGORY, 
COUNT(PIZZA_TYPES.NAME) AS PIZZA_TYPES
FROM PIZZA_TYPES
GROUP BY PIZZA_TYPES.CATEGORY;
```

4) Group the orders by date and calculate the average number of pizzas ordered per day?
```sql
SELECT ROUND(AVG(QUANTITY), 0) AS AVG_ORD_PLACED FROM
(SELECT ORDERS.ORDER_DATE, SUM(ORDER_DETAILS.QUANTITY) AS QUANTITY
FROM ORDERS JOIN ORDER_DETAILS 
ON ORDERS.ORDER_ID = ORDER_DETAILS.ORDER_ID
GROUP BY ORDERS.ORDER_DATE) 
AS ORDER_QUANTITY;
```

5) Determine the top 3 most ordered pizza types based on revenue?
```sql
SELECT PIZZA_TYPES.NAME	,
SUM(ORDER_DETAILS.QUANTITY * PIZZAS.PRICE) AS REVENUE
FROM PIZZA_TYPES JOIN PIZZAS
ON PIZZA_TYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
JOIN ORDER_DETAILS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
GROUP BY PIZZA_TYPES.NAME 
ORDER BY REVENUE DESC
LIMIT 3;
```


---
### Advanced Level

1) Calculate the percentage contribution of each pizza type to total revenue?
```sql
SELECT PIZZA_TYPES.CATEGORY, 
ROUND(SUM(ORDER_DETAILS.QUANTITY * PIZZAS.PRICE) /
(SELECT ROUND(SUM(ORDER_DETAILS.QUANTITY * PIZZAS.PRICE), 2) AS TOTAL_SALES
FROM ORDER_DETAILS JOIN PIZZAS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID) *100,2) AS REVENUE
FROM PIZZA_TYPES JOIN PIZZAS
ON PIZZA_TYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
JOIN ORDER_DETAILS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
GROUP BY PIZZA_TYPES.CATEGORY 
ORDER BY REVENUE DESC;
```




2) Analyze the cumulative revenue generated over time?
```sql
SELECT ORDER_DATE, SUM(REVENUE) 
OVER(ORDER BY ORDER_DATE) AS CUMM_REVENU
FROM    
(SELECT ORDERS.ORDER_DATE,
SUM(ORDER_DETAILS.QUANTITY * PIZZAS.PRICE) AS REVENUE
FROM ORDER_DETAILS JOIN PIZZAS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
JOIN ORDERS
ON ORDERS.ORDER_ID = ORDER_DETAILS.ORDER_ID 
GROUP BY ORDERS.ORDER_DATE) AS SALES;
```





3) Determine the top 3 most ordered pizza types based on revenue for each pizza category?
```sql
SELECT NAME, REVENUE FROM 
(SELECT CATEGORY, NAME, REVENUE, 
RANK() OVER(PARTITION BY CATEGORY ORDER BY REVENUE DESC) 
AS RANK_REV
FROM
(SELECT PIZZA_TYPES.CATEGORY, PIZZA_TYPES.NAME,
SUM(ORDER_DETAILS.QUANTITY * PIZZAS.PRICE) AS REVENUE
FROM PIZZA_TYPES JOIN PIZZAS
ON PIZZA_TYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
JOIN ORDER_DETAILS
ON ORDER_DETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
GROUP BY PIZZA_TYPES.CATEGORY, PIZZA_TYPES.NAME) AS A) C
WHERE RANK_REV >= 3;
```

---

## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## How to Run the Project
1. Install PostgreSQL and pgAdmin (if not already installed).
2. Set up the database schema and tables using the provided normalization structure.
3. Insert the sample data into the respective tables.
4. Execute SQL queries to solve the listed problems.
5. Explore query optimization techniques for large datasets.

---

## Next Steps
- **Visualize the Data**: Use a data visualization tool like **Tableau** or **Power BI** to create dashboards based on the query results.
- **Expand Dataset**: Add more rows to the dataset for broader analysis and scalability testing.
- **Advanced Querying**: Dive deeper into query optimization and explore the performance of SQL queries on larger datasets.

---

## Contributing
If you would like to contribute to this project, feel free to fork the repository, submit pull requests, or raise issues.

---




