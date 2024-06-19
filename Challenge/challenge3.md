B.Data Analysis questions
---

**Query #1**

    SELECT COUNT(DISTINCT customer_id) AS num_of_customers
    FROM foodie_fi.subscriptions;

| num_of_customers |
| ---------------- |
| 1000             |

---
**Query #2**

    SELECT DATE_PART('month', start_date) AS month, COUNT(*) 
    FROM foodie_fi.subscriptions 
    WHERE plan_id = 0 
    GROUP BY month 
    ORDER BY month;

| month | count |
| ----- | ----- |
| 1     | 88    |
| 2     | 68    |
| 3     | 94    |
| 4     | 81    |
| 5     | 88    |
| 6     | 79    |
| 7     | 89    |
| 8     | 88    |
| 9     | 87    |
| 10    | 79    |
| 11    | 75    |
| 12    | 84    |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/rHJhRrXy5hbVBNJ6F6b9gJ/16)
