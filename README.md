# SQL Case Study 2: Pizza Runner  

## D.Pricing and Ratings

**Question 1:** If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?

---

## SQL Code

```sql

-- Clean runner_orders table cancellation column - make sure the condition includes trimming white spaces ,checking for nulls and Nan.

UPDATE runner_orders 
SET cancellation = null 
WHERe cancellation = 'null' OR  TRIM(cancellation) = '' OR 
cancellation = 'NaN' ;

WITH pizza_runner_costs AS
(
SELECT  co.pizza_id,pizza_name,count(co.pizza_id) as pizzas_ordered,
CASE WHEN co.pizza_id = 1 THEN count(co.pizza_id)*12
ELSE count(co.pizza_id)*10
END AS total_pizza_cost 
FROM customer_orders co 
LEFT JOIN runner_orders ro ON co.order_id = ro.order_id
LEFT JOIN pizza_names pn  ON co.pizza_id = pn.pizza_id
WHERE cancellation IS NULL 
GROUP BY co.pizza_id,pizza_name
ORDER BY co.pizza_id 

) 

SELECT SUM(total_pizza_cost) AS money_pizza_runner_made FROM pizza_runner_costs

```
pizza_runner_costs CTE TABLE

| pizza_id | pizza_name | pizzas_ordered | total_pizza_cost |
| -------- | ---------- | -------------- | ---------------- |
| 1        | Meatlovers | 9              | 108              |
| 2        | Vegetarian | 3              | 30               |

---


| money_pizza_runner_made |
| ----------------------- |
| 138                     |

---

**Question 2:** What if there was an additional $1 charge for any pizza extras? Add cheese is $1 extra

---

## SQL Code

```sql
UPDATE runner_orders 
SET cancellation = null 
WHERe cancellation = 'null' OR  TRIM(cancellation) = '' OR 
cancellation = 'NaN' ;

UPDATE customer_orders
SET extras = null 
WHERe extras = 'null' OR  TRIM(extras) = '' OR 
extras = 'NaN' ;

WITH pizza_runner_costs AS
(
SELECT  co.pizza_id,pizza_name,count(co.pizza_id) as pizzas_ordered,
CASE WHEN  cardinality(string_to_array(extras,',')) IS NULL THEN  1 
ELSE cardinality(string_to_array(extras,',')) * 1 
END AS  extras_charge,
CASE WHEN co.pizza_id = 1 THEN count(co.pizza_id)*12
ELSE count(co.pizza_id)*10
END AS total_pizza_cost 
FROM customer_orders co 
LEFT JOIN runner_orders ro ON co.order_id = ro.order_id
LEFT JOIN pizza_names pn  ON co.pizza_id = pn.pizza_id
WHERE cancellation IS NULL 
GROUP BY co.pizza_id,pizza_name, extras
ORDER BY co.pizza_id 
)

SELECT  SUM(total_pizza_cost)+SUM(extras_charge) AS money_pizza_runner_made FROM pizza_runner_costs
```

---
| money_pizza_runner_made |
| ----------------------- |
| 144                     |

---
