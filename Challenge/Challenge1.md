| Table: sales |             |              | 
|--------------|-------------|------------- |
| customer_id  | order_date  | product_id   |
|--------------|-------------|------------- |
| A            | 2021-01-01  | 1            |
| A            | 2021-01-01  | 2            |
| A            | 2021-01-07  | 2            |
| A            | 2021-01-10  | 3            |
| A            | 2021-01-11  | 3            |
| A            | 2021-01-11  | 3            |
| B            | 2021-01-01  | 2            |
| B            | 2021-01-02  | 2            |
| B            | 2021-01-04  | 1            |
| B            | 2021-01-11  | 1            |
| B            | 2021-01-16  | 3            |
| B            | 2021-02-01  | 3            |
| C            | 2021-01-01  | 3            |
| C            | 2021-01-01  | 3            |
| C            | 2021-01-07  | 3            |

| Table: menu |              |         | 
|--------------|-------------|---------|
| product_id   | product_name| price   |
|--------------|-------------|---------|
| 1            | sushi       | 10      |
| 2            | curry       | 15      |
| 3            | ramen       | 12      |

| Table: members |             |             |
|----------------|-------------|-------------|
| customer_id    | join_date   |             |
|----------------|-------------|-------------|
| A              | 2021-01-07  |             |
| B              | 2021-01-09  |             |

**Query #1**

    SELECT
        s.customer_id,
        SUM(m.price) AS total_sales
    FROM
        dannys_diner.sales s
    JOIN
        dannys_diner.menu m ON s.product_id = m.product_id
    GROUP BY
        s.customer_id
    ORDER BY
        total_amount_spent DESC;

| customer_id | total_sales |
| ----------- | ------------------ |
| A           | 76                 |
| B           | 74                 |
| C           | 36                 |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

