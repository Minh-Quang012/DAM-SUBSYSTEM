**Schema (PostgreSQL v15)**

    
    DROP TABLE IF EXISTS runners;
    CREATE TABLE runners (
      "runner_id" INTEGER,
      "registration_date" DATE
    );
    INSERT INTO runners
      ("runner_id", "registration_date")
    VALUES
      (1, '2021-01-01'),
      (2, '2021-01-03'),
      (3, '2021-01-08'),
      (4, '2021-01-15');
    
    
    DROP TABLE IF EXISTS customer_orders;
    CREATE TABLE customer_orders (
      "order_id" INTEGER,
      "customer_id" INTEGER,
      "pizza_id" INTEGER,
      "exclusions" VARCHAR(4),
      "extras" VARCHAR(4),
      "order_time" TIMESTAMP
    );
    
    INSERT INTO customer_orders
      ("order_id", "customer_id", "pizza_id", "exclusions", "extras", "order_time")
    VALUES
      ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
      ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
      ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
      ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
      ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
      ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
      ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
      ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
      ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
      ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
      ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
      ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
      ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
      ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');
    
    
    DROP TABLE IF EXISTS runner_orders;
    CREATE TABLE runner_orders (
      "order_id" INTEGER,
      "runner_id" INTEGER,
      "pickup_time" VARCHAR(19),
      "distance" VARCHAR(7),
      "duration" VARCHAR(10),
      "cancellation" VARCHAR(23)
    );
    
    INSERT INTO runner_orders
      ("order_id", "runner_id", "pickup_time", "distance", "duration", "cancellation")
    VALUES
      ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
      ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
      ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
      ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
      ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
      ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
      ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
      ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
      ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
      ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');
    
    
    DROP TABLE IF EXISTS pizza_names;
    CREATE TABLE pizza_names (
      "pizza_id" INTEGER,
      "pizza_name" TEXT
    );
    INSERT INTO pizza_names
      ("pizza_id", "pizza_name")
    VALUES
      (1, 'Meatlovers'),
      (2, 'Vegetarian');
    
    
    DROP TABLE IF EXISTS pizza_recipes;
    CREATE TABLE pizza_recipes (
      "pizza_id" INTEGER,
      "toppings" TEXT
    );
    INSERT INTO pizza_recipes
      ("pizza_id", "toppings")
    VALUES
      (1, '1, 2, 3, 4, 5, 6, 8, 10'),
      (2, '4, 6, 7, 9, 11, 12');
    
    
    DROP TABLE IF EXISTS pizza_toppings;
    CREATE TABLE pizza_toppings (
      "topping_id" INTEGER,
      "topping_name" TEXT
    );
    INSERT INTO pizza_toppings
      ("topping_id", "topping_name")
    VALUES
      (1, 'Bacon'),
      (2, 'BBQ Sauce'),
      (3, 'Beef'),
      (4, 'Cheese'),
      (5, 'Chicken'),
      (6, 'Mushrooms'),
      (7, 'Onions'),
      (8, 'Pepperoni'),
      (9, 'Peppers'),
      (10, 'Salami'),
      (11, 'Tomatoes'),
      (12, 'Tomato Sauce');

---

**Query #1**

    SELECT COUNT(*) AS pizza_order_count
    FROM customer_orders;

| pizza_order_count |
| ----------------- |
| 14                |

---
**Query #2**

    SELECT COUNT(DISTINCT order_id) AS unique_order_count
    FROM customer_orders;

| unique_order_count |
| ------------------ |
| 10                 |

---
**Query #3**

    SELECT runner_id, COUNT(order_id) AS Successful_orders
    FROM runner_orders
    Where cancellation IS NULL OR cancellation=''
    Group by runner_id;

| runner_id | successful_orders |
| --------- | ----------------- |
| 1         | 3                 |
| 2         | 1                 |
| 3         | 1                 |

---
**Query #4**

    SELECT
        pn.pizza_name,
        COUNT(co.order_id) AS total_delivered
    FROM
        customer_orders co
    JOIN
        pizza_names pn ON co.pizza_id = pn.pizza_id
    JOIN
        runner_orders ro ON co.order_id = ro.order_id
    WHERE
        ro.cancellation IS NULL OR ro.cancellation = ''
    GROUP BY
        pn.pizza_name;

| pizza_name | total_delivered |
| ---------- | --------------- |
| Meatlovers | 6               |
| Vegetarian | 2               |

---
**Query #5**

    SELECT
        co.customer_id,
        pn.pizza_name,
        COUNT(co.order_id) AS total_ordered
    FROM
        customer_orders co
    JOIN
        pizza_names pn ON co.pizza_id = pn.pizza_id
    GROUP BY
        co.customer_id, pn.pizza_name
    ORDER BY
        co.customer_id, pn.pizza_name;

| customer_id | pizza_name | total_ordered |
| ----------- | ---------- | ------------- |
| 101         | Meatlovers | 2             |
| 101         | Vegetarian | 1             |
| 102         | Meatlovers | 2             |
| 102         | Vegetarian | 1             |
| 103         | Meatlovers | 3             |
| 103         | Vegetarian | 1             |
| 104         | Meatlovers | 3             |
| 105         | Vegetarian | 1             |

---
**Query #6**

    SELECT
        co.order_id,
        COUNT(co.pizza_id) AS pizzas_delivered
    FROM
        customer_orders co
    JOIN
        runner_orders ro ON co.order_id = ro.order_id
    WHERE
        ro.cancellation IS NULL OR ro.cancellation = ''
    GROUP BY
        co.order_id
    ORDER BY
        pizzas_delivered DESC
    LIMIT 1;

| order_id | pizzas_delivered |
| -------- | ---------------- |
| 4        | 3                |

---
**Query #7**

    SELECT
        co.customer_id,
        SUM(CASE 
                WHEN (co.exclusions IS NOT NULL AND co.exclusions != '') 
                   OR (co.extras IS NOT NULL AND co.extras != '') 
                THEN 1 
                ELSE 0 
            END) AS pizzas_with_changes,
        SUM(CASE 
                WHEN (co.exclusions IS NULL OR co.exclusions = '') 
                   AND (co.extras IS NULL OR co.extras = '') 
                THEN 1 
                ELSE 0 
            END) AS pizzas_without_changes
    FROM
        customer_orders co
    JOIN
        runner_orders ro ON co.order_id = ro.order_id
    WHERE
        ro.cancellation IS NULL OR ro.cancellation = ''
    GROUP BY
        co.customer_id
    ORDER BY
        co.customer_id;

| customer_id | pizzas_with_changes | pizzas_without_changes |
| ----------- | ------------------- | ---------------------- |
| 101         | 0                   | 2                      |
| 102         | 0                   | 2                      |
| 103         | 3                   | 0                      |
| 104         | 1                   | 0                      |

---
**Query #8**

    SELECT
        COUNT(*) AS pizzas_with_both_changes
    FROM
        customer_orders co
    JOIN
        runner_orders ro ON co.order_id = ro.order_id
    WHERE
        (co.exclusions IS NOT NULL AND co.exclusions != '')
        AND (co.extras IS NOT NULL AND co.extras != '')
        AND (ro.cancellation IS NULL OR ro.cancellation = '');

| pizzas_with_both_changes |
| ------------------------ |
| 1                        |

---

**Query #9**

    SELECT
        EXTRACT(HOUR FROM co.order_time) AS order_hour,
        COUNT(co.order_id) AS total_pizzas_ordered
    FROM
        customer_orders co
    GROUP BY
        order_hour
    ORDER BY
        order_hour;

| order_hour | total_pizzas_ordered |
| ---------- | -------------------- |
| 11         | 1                    |
| 13         | 3                    |
| 18         | 3                    |
| 19         | 1                    |
| 21         | 3                    |
| 23         | 3                    |

---
**Query #10**

    SELECT 
      EXTRACT(ISODOW FROM order_time) AS day_of_week,
      COUNT(order_id) AS volume_of_orders
    FROM 
      customer_orders
    GROUP BY 
      day_of_week;

| day_of_week | volume_of_orders |
| ----------- | ---------------- |
| 3           | 5                |
| 4           | 3                |
| 6           | 5                |
| 5           | 1                |

---
B. Runner and customer experience
---


[View on DB Fiddle](https://www.db-fiddle.com/f/7VcQKQwsS3CTkGRFG7vu98/65)
