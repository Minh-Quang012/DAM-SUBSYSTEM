**Schema (PostgreSQL v15)**

    
    CREATE TABLE sales (
      "customer_id" VARCHAR(1),
      "order_date" DATE,
      "product_id" INTEGER
    );
    
    INSERT INTO sales
      ("customer_id", "order_date", "product_id")
    VALUES
      ('A', '2021-01-01', '1'),
      ('A', '2021-01-01', '2'),
      ('A', '2021-01-07', '2'),
      ('A', '2021-01-10', '3'),
      ('A', '2021-01-11', '3'),
      ('A', '2021-01-11', '3'),
      ('B', '2021-01-01', '2'),
      ('B', '2021-01-02', '2'),
      ('B', '2021-01-04', '1'),
      ('B', '2021-01-11', '1'),
      ('B', '2021-01-16', '3'),
      ('B', '2021-02-01', '3'),
      ('C', '2021-01-01', '3'),
      ('C', '2021-01-01', '3'),
      ('C', '2021-01-07', '3');
     
    
    CREATE TABLE menu (
      "product_id" INTEGER,
      "product_name" VARCHAR(5),
      "price" INTEGER
    );
    
    INSERT INTO menu
      ("product_id", "product_name", "price")
    VALUES
      ('1', 'sushi', '10'),
      ('2', 'curry', '15'),
      ('3', 'ramen', '12');
      
    
    CREATE TABLE members (
      "customer_id" VARCHAR(1),
      "join_date" DATE
    );
    
    INSERT INTO members
      ("customer_id", "join_date")
    VALUES
      ('A', '2021-01-07'),
      ('B', '2021-01-09');

---

**Query #1**

    SELECT customer_id, SUM(price) as total_spent
    FROM sales
    JOIN menu ON sales.product_id = menu.product_id
    GROUP BY customer_id
    ORDER BY sales.customer_id ASC;

| customer_id | total_spent |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

---
**Query #2**

    SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
    FROM sales
    GROUP BY customer_id;

| customer_id | days_visited |
| ----------- | ------------ |
| A           | 4            |
| B           | 6            |
| C           | 2            |

---
**Query #3**

    WITH ordered_sales_cte AS (
        SELECT
            s.customer_id,
            m.product_name,
            ROW_NUMBER() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
        FROM
            sales s
            JOIN menu m ON s.product_id = m.product_id
    )
    
    SELECT DISTINCT ON (customer_id)
        customer_id,
        product_name
    FROM
        ordered_sales_cte
    WHERE
        rank = 1
    ORDER BY
        customer_id, rank;

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| B           | curry        |
| C           | ramen        |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/7240)
