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
**Query #3**

    SELECT p.plan_name, COUNT(*) 
    FROM subscriptions s
    JOIN plans p ON s.plan_id = p.plan_id
    WHERE s.start_date > '2020-12-31' 
    GROUP BY p.plan_name;

| plan_name     | count |
| ------------- | ----- |
| pro annual    | 63    |
| churn         | 71    |
| pro monthly   | 60    |
| basic monthly | 8     |

---
**Query #4**

    SELECT
      COUNT(DISTINCT sub.customer_id) AS churned_customers,
      ROUND(100.0 * COUNT(sub.customer_id)
        / (SELECT COUNT(DISTINCT customer_id) 
        	FROM subscriptions)
      ,1) AS churn_percentage
    FROM subscriptions AS sub
    JOIN plans
      ON sub.plan_id = plans.plan_id
    WHERE plans.plan_id = 4;

| churned_customers | churn_percentage |
| ----------------- | ---------------- |
| 307               | 30.7             |

---

**Query #5**

    WITH initial_trial AS (
        SELECT customer_id
        FROM subscriptions
        WHERE plan_id = 0
    ),
    
    churn_after_trial AS (
        SELECT DISTINCT s1.customer_id
        FROM subscriptions s1
        JOIN subscriptions s2 ON s1.customer_id = s2.customer_id
        WHERE s1.plan_id = 0
          AND s2.plan_id = 4
          AND s1.start_date < s2.start_date
          AND NOT EXISTS (
              SELECT 1
              FROM subscriptions s3
              WHERE s3.customer_id = s1.customer_id
                AND s3.start_date > s1.start_date
                AND s3.start_date < s2.start_date
          )
    )
    
    SELECT
        COUNT(DISTINCT churn_after_trial.customer_id) AS churned_after_trial,
        COUNT(DISTINCT initial_trial.customer_id) AS total_initial_trial,
        ROUND(
            100.0 * COUNT(DISTINCT churn_after_trial.customer_id) / COUNT(DISTINCT initial_trial.customer_id)
        ) AS percentage_churned
    FROM initial_trial
    LEFT JOIN churn_after_trial
    ON initial_trial.customer_id = churn_after_trial.customer_id;

| churned_after_trial | total_initial_trial | percentage_churned |
| ------------------- | ------------------- | ------------------ |
| 92                  | 1000                | 9                  |

---

**Query #6**

    WITH ranked_cte AS (
      SELECT 
        sub.customer_id, 
        p.plan_id, 
        p.plan_name,
        ROW_NUMBER() OVER (
          PARTITION BY sub.customer_id 
          ORDER BY sub.start_date) AS row_num
      FROM subscriptions AS sub
      JOIN plans p
        ON sub.plan_id = p.plan_id
    )
      
    SELECT 
      plan_name,
      COUNT(*) AS number_of_customers,
      ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_customers
    FROM ranked_cte
    WHERE row_num = 2 
    GROUP BY plan_name;

| plan_name     | number_of_customers | percentage_of_customers |
| ------------- | ------------------- | ----------------------- |
| basic monthly | 546                 | 54.60                   |
| churn         | 92                  | 9.20                    |
| pro annual    | 37                  | 3.70                    |
| pro monthly   | 325                 | 32.50                   |

---
**Query #7**

    SELECT 
      p.plan_name,
      COUNT(DISTINCT sub.customer_id) AS customer_count,
      ROUND(100.0 * COUNT(DISTINCT sub.customer_id) / SUM(COUNT(DISTINCT sub.customer_id)) OVER (), 2) AS customer_percentage
    FROM subscriptions AS sub
    JOIN plans p ON sub.plan_id = p.plan_id
    WHERE sub.start_date <= '2020-12-31'
    
    GROUP BY p.plan_name;

| plan_name     | customer_count | customer_percentage |
| ------------- | -------------- | ------------------- |
| basic monthly | 538            | 21.98               |
| churn         | 236            | 9.64                |
| pro annual    | 195            | 7.97                |
| pro monthly   | 479            | 19.57               |
| trial         | 1000           | 40.85               |

---
**Query #8**

    SELECT COUNT(DISTINCT customer_id) AS customers_upgraded_to_annual_2020
    FROM subscriptions
    WHERE plan_id = 3
      AND EXTRACT(YEAR FROM start_date) = 2020;

| customers_upgraded_to_annual_2020 |
| --------------------------------- |
| 195                               |

---
**Query #9**

    WITH annual_upgrade AS (
        SELECT s.customer_id,
               MIN(s.start_date) AS join_date,
               MIN(s.start_date) FILTER (WHERE s.plan_id IN (3)) AS upgrade_to_annual_date
        FROM subscriptions s
        GROUP BY s.customer_id
    )
    SELECT AVG(upgrade_to_annual_date - join_date) AS average_days_to_upgrade
    FROM annual_upgrade;

| average_days_to_upgrade |
| ----------------------- |
| 104.6201550387596899    |

---
[View on DB Fiddle](https://www.db-fiddle.com/f/rHJhRrXy5hbVBNJ6F6b9gJ/16)
