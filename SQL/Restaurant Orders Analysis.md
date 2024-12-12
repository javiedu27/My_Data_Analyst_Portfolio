## In this project I analized the Restaurant Orders datasets found in Maven Analytics, you can check the datasets i used [HERE](https://www.mavenanalytics.io/data-playground?order=date_added%2Cdesc&search=restaurant%20orders)


## OBJECTIVE 1: EXPLORE THE MENU ITEMS TABLE ##

--1. View the menu_items table.
  SELECT 
    *
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`;

--2. Find the number of items in the menu.
  SELECT
    COUNT(*) AS number_of_items
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`;

    ##Answer: 32 total items

--3. What are the least and most expensive items on the menu?
  SELECT
    *
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  ORDER BY
    price;

  SELECT
    *
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  ORDER BY
    price DESC;

    ##Answer: Least expensive is Edamame, Most expensive is Shrimp Scampi

--4. How many italian dishes are on the menu?
  SELECT
    COUNT(*) AS italian_dishes
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  WHERE
    category='Italian';

    ##Answer: 9 total italian dishes

--5. What are the least and most expensive italian dishes on the menu?
  SELECT
    *
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  WHERE
    category= 'Italian'
  ORDER BY price;

  SELECT
    *
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  WHERE
    category= 'Italian'
  ORDER BY price DESC;

    ##Answer: Least expensive italian dish is Spaghetti, Most expensive italian dish is Shrimp Scampi

--6. How many dishes are in each category?
  SELECT
    category,
    COUNT(menu_item_id) AS num_dishes_category
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  GROUP BY 
    category;

    ##Answer: American 6, Asian 8, Mexican and Italian 9

--7. What is the average dish price within each category?
  SELECT
    category,
    ROUND(AVG(price), 2) AS avg_price_dishes
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Menu_Items`
  GROUP BY 
    category;

    ##Answer: American $10.07, Asian $13.48, Mexican $11.8, Italian $16.75
## OBJECTIVE 2: EXPLORE THE ORDERS TABLE ##

--1. View the order_details table. 
  SELECT
    *
  FROM
    quick-geography-433113-t1.Restaurant_Orders.Order_Details;

--2. What is the date range of the table?
  SELECT
    MIN(order_date) AS date_range_start,
    MAX(order_date) AS date_range_end
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Order_Details`;

  ##Answer: from 2023-01-01 to 2023-03-31 (YYYY-MM-DD)
 
--3. How many orders were made within this date range? 
  SELECT
    COUNT(DISTINCT order_id) AS num_orders
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Order_Details`;

  ##Answer: 5370 total orders

--4. How many items were ordered within this date range?
  SELECT
    COUNT(*) AS num_items
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Order_Details`;
  
  ##Answer: 12234 total items

--5. Which orders had the most number of items?
  SELECT
    order_id,
    COUNT(item_id) AS num_items
  FROM
    `quick-geography-433113-t1.Restaurant_Orders.Order_Details`
  GROUP BY order_id
  ORDER BY num_items DESC;

  ##Answer: Orders 330, 440, 443, 1957, 2675, 3473, 4305, 4482 have 14 items 

--6. How many orders had more than 12 items?
  SELECT
    COUNT(*) AS num_orders
  FROM
    (SELECT
     order_id,
     COUNT(item_id) AS num_items
   FROM
     `quick-geography-433113-t1.Restaurant_Orders.Order_Details`
   GROUP BY order_id
   HAVING
     num_items > 12);

  ##Answer: There are 23 orders with more than 12 items


## OBJECTIVE 3: ANALYZE CUSTOMER BEHAVIOR ##

--1. Combine the menu_items and order_details tables into a single table

SELECT
  *
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id;

--2. What were the least and most ordered items?
SELECT
  item_name,
  COUNT(order_details_id) AS num_purchases
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id
GROUP BY item_name
ORDER BY(num_purchases);

SELECT
  item_name,
  COUNT(order_details_id) AS num_purchases
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id
GROUP BY item_name
ORDER BY num_purchases DESC;
  ##Answer: The least ordered item was Chicken Tacos with 123, and the most ordered item was Hamburguer with 622

--4. What categories were they in?
SELECT
  item_name,
  category,
  COUNT(order_details_id) AS num_purchases
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id
GROUP BY item_name, category
ORDER BY num_purchases DESC;
  ##Answer: Chicken Tacos is in Mexican and Hamburguer is in American

--5. What were the top 5 orders that spent the most money?
SELECT
  order_id, ROUND(SUM(price), 2) AS total_spend
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id
GROUP BY order_id
ORDER BY total_spend DESC
LIMIT 5;
  ##Answer: The top 5 orders that spend the mostm oney are: Order 440 with $192.15, order 2075 with $191.05, order 1957 with $190.1, order 330 with $189.7 and order 2675 with $185.1
  
--6. View the details of the highest spend order. What insights can you gather from the results?
SELECT
  category, COUNT(item_id) AS num_items
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id
WHERE order_id = 440
GROUP BY category;
  ##The highest spending order prefered Italian more than the other categories

--8. View the details of the top 5 highest spend orders. What insights can you gather from the results?
SELECT
  category, COUNT(item_id) AS num_items
FROM
  `quick-geography-433113-t1.Restaurant_Orders.Order_Details` AS od
  LEFT JOIN
  `quick-geography-433113-t1.Restaurant_Orders.Menu_Items` AS mi
  ON
   SAFE_CAST(od.item_id AS INT64) = mi.menu_item_id
WHERE order_id IN (440,2075,1957,330,2675)
GROUP BY category;
  ##Answer: The top 5 highest spending orders also prefere Italian more than the other categories
