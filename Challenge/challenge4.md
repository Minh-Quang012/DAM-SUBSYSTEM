**Query #1**

    SELECT COUNT(DISTINCT node_id) AS unique_node
    FROM customer_nodes;

| unique_node |
| ----------- |
| 5           |

---
**Query #2**

    SELECT 
        r.region_name, 
        COUNT(DISTINCT cn.node_id) AS number_of_nodes
    FROM 
        customer_nodes cn
    JOIN 
        regions r ON cn.region_id = r.region_id
    GROUP BY 
        r.region_name
    ORDER BY 
        r.region_name;

| region_name | number_of_nodes |
| ----------- | --------------- |
| Africa      | 5               |
| America     | 5               |
| Asia        | 5               |
| Australia   | 5               |
| Europe      | 5               |

---
**Query #3**

    SELECT r.region_name, COUNT(DISTINCT cn.customer_id) AS customers_per_region
    FROM regions AS r
    JOIN customer_nodes AS cn ON r.region_id = cn.region_id
    GROUP BY r.region_name;

| region_name | customers_per_region |
| ----------- | -------------------- |
| Africa      | 102                  |
| America     | 105                  |
| Asia        | 95                   |
| Australia   | 110                  |
| Europe      | 88                   |

---
B. Customer Transaction
---
**Query #1**

    SELECT txn_type,
    COUNT(DISTINCT customer_id) AS unique_count, 
    SUM(txn_amount) AS total_amount
    FROM customer_transactions
    GROUP BY txn_type;

| txn_type   | unique_count | total_amount |
| ---------- | ------------ | ------------ |
| deposit    | 500          | 1359168      |
| purchase   | 448          | 806537       |
| withdrawal | 439          | 793003       |

---
**Query #2**

    SELECT 
        AVG(deposit_count) AS avg_total_deposit_count, 
        AVG(total_deposit_amount) AS avg_total_deposit_amount
    FROM (
        SELECT 
            customer_id, 
            COUNT(*) AS deposit_count, 
            SUM(txn_amount) AS total_deposit_amount
        FROM 
            customer_transactions
        WHERE 
            txn_type = 'deposit'
        GROUP BY 
            customer_id
    ) AS customer_deposits;

| avg_total_deposit_count | avg_total_deposit_amount |
| ----------------------- | ------------------------ |
| 5.3420000000000000      | 2718.3360000000000000    |

---


[View on DB Fiddle](https://www.db-fiddle.com/f/2GtQz4wZtuNNu7zXH5HtV4/3)
