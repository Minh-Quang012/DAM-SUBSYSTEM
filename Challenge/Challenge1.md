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
**Query #4**
```sql
SELECT 
    m.product_name, 
    COUNT(s.product_id) AS most_purchased_item 
FROM 
    sales s 
INNER JOIN 
    menu m ON s.product_id = m.product_id 
GROUP BY 
    m.product_name 
ORDER BY 
    most_purchased_item DESC 
LIMIT 1;
```
| product_name | most_purchased_item |
|--------------|---------------------|
| ramen        | 8                   |

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/7240)
---
**Query #5**
```sql

    WITH most_popular AS (
    SELECT 
    	sales.customer_id,
    	menu.product_name,
    	COUNT (menu.product_id) AS order_count,
      	DENSE_RANK () OVER (
          PARTITION BY sales.customer_id
        	ORDER BY COUNT(sales.customer_id) DESC) AS RANK
      FROM menu
      INNER JOIN sales 
      ON menu.product_id = sales.product_id
      GROUP BY sales.customer_id , menu.product_name
      
    )
    SELECT 
    	customer_id,
        product_name,
        order_count
    from most_popular
    Where rank=1;


```
| customer_id | product_name | order_count |
| ----------- | ------------ | ----------- |
| A           | ramen        | 3           |
| B           | ramen        | 2           |
| B           | curry        | 2           |
| B           | sushi        | 2           |
| C           | ramen        | 3           |
---
**Query #6**
```sql
    WITH joined_as_member AS (
      SELECT
        m.customer_id, 
        s.product_id,
        ROW_NUMBER() OVER (
          PARTITION BY m.customer_id
          ORDER BY s.order_date) AS row_num
      FROM members m
      JOIN sales s
        ON m.customer_id = s.customer_id
        AND s.order_date > m.join_date
    )
    
    SELECT 
      j.customer_id, 
      menu.product_name 
    FROM joined_as_member j
    JOIN menu 
      ON j.product_id = menu.product_id
    WHERE j.row_num = 1
    ORDER BY j.customer_id;
[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
```
| customer_id | product_name |
| ----------- | ------------ |
| A           | ramen        |
| B           | sushi        |
---
**Query #7**
```sql
    SELECT customer_id, product_name
    FROM (
      SELECT m.customer_id, menu.product_name, 
      ROW_NUMBER() OVER(PARTITION BY m.customer_id ORDER BY s.order_date DESC) as rn
      FROM members m
      JOIN sales s ON m.customer_id = s.customer_id
      JOIN menu ON s.product_id = menu.product_id
      WHERE s.order_date < m.join_date
    ) tmp
    WHERE rn = 1;
```
| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| B           | sushi        |
---
**Query #8**
```sql
 SELECT m.customer_id, COUNT(*) as total_items, SUM(price) as total_spent
FROM members m
JOIN sales s ON m.customer_id = s.customer_id
JOIN menu ON s.product_id = menu.product_id
WHERE s.order_date < m.join_date
GROUP BY m.customer_id
ORDER BY m.customer_id ASC;
```
| customer_id | total_items | total_spent |
| ----------- | ----------- | ----------- |
| A           | 2           | 25          |
| B           | 3           | 40          |

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
