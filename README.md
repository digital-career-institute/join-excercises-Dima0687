# java-DB-AdvancedSQL-join-excercises

Join excercises


1. Select all product names for suppliers from USA. 

2. Select order id, order date, employee first and last name for employee whose last name starts with 'D' and for order that was shipped to Germany.

3. Select all orders shipped by United Package for orders sooner than '1996-08-12'

4. Select shippers company name that doesn't have any orders assigned.

5. Select order ids that contains product from category 'Beverages' ordered '1996-08-14'.

6. Select product names and availability of products supplied by 'Lyngbysild' from category 'Seafood'

7. Select last names of employees assigned to 'Northern' region.

8. Select employee id and his boss last name for those who have orders with date later than '1996-07-18'.
if an employee doesn't have a boss don't include him.

9. List the employees in the warehouse with orders that are not shipped yet.

10. Calculate the price for each product with its name in each order after discount is applied.

```SQL
-- Select all product names for suppliers from USA.
SELECT 
	p.product_name,
	s.country
FROM products p
JOIN suppliers s ON p.supplier_id = s.supplier_id
WHERE s.country = 'USA'
GROUP BY p.product_name, s.country;

-- Select order id, order date, employee first and last name for employee 
-- whose last name starts with 'D' and for order that was shipped to Germany.

SELECT
	o.order_id,
	o.order_date,
	em.first_name AS employee_fist_name,
	em.last_name AS employee_last_name
FROM orders o
JOIN employees em ON em.employee_id = o.employee_id
WHERE em.last_name ILIKE 'D%'
	AND ship_country ILIKE 'Germany'
GROUP BY o.order_id, o.order_date, em.first_name, em.last_name;

-- Select all orders shipped by United Package for orders sooner than '1996-08-12'
SELECT 
	o.*,
	sh.company_name
FROM orders o
JOIN shippers sh ON o.ship_via = sh.shipper_id
WHERE sh.company_name ILIKE 'United Package'
	AND o.order_date < '1996-08-12'
GROUP BY o.order_id, sh.company_name;

-- Select shippers company name that doesn't have any orders assigned.

SELECT 
	sh.company_name
FROM shippers sh
LEFT JOIN orders o ON sh.shipper_id = o.ship_via
WHERE o.order_id IS NULL; 
	
-- Select order ids that contains product from category 'Beverages' ordered '1996-08-14'.

SELECT 
	od.order_id
FROM order_details od
JOIN products p ON od.product_id = p.product_id
JOIN categories ct ON ct.category_id = p.category_id
JOIN orders o ON od.order_id = o.order_id
WHERE
	ct.category_name ILIKE 'Beverages'
	AND o.order_date = '1996-08-14';
	
-- Select product names and availability of products supplied by 'Lyngbysild' from category 'Seafood'

SELECT
	p.product_name,
	p.units_in_stock
FROM products p
JOIN suppliers s ON p.supplier_id = s.supplier_id
JOIN categories c ON p.category_id = c.category_id
WHERE 
	s.company_name ILIKE 'Lyngbysild'
	AND c.category_name ILIKE 'Seafood';
	
-- Select last names of employees assigned to 'Northern' region.

SELECT
	em.last_name
FROM employees em
JOIN employee_territories et ON em.employee_id = et.employee_id
JOIN territories t ON t.territory_id = et.territory_id
JOIN region r ON t.region_id = r.region_id
WHERE r.region_description ILIKE 'Northern';

-- Select employee id and his boss last name for those 
-- who have orders with date later than '1996-07-18'. 
-- If an employee doesn't have a boss don't include him.

SELECT 
	em.employee_id,
	b.last_name AS boss_last_name
FROM employees em
JOIN employees b ON em.reports_to = b.employee_id
JOIN orders o ON em.employee_id = o.employee_id
WHERE
	o.order_date > '1996-07-18'
GROUP BY
	b.last_name, em.employee_id
ORDER BY
	b.last_name;
	
-- List the employees in the warehouse with orders that are not shipped yet.

SELECT DISTINCT 
	employee_id 
FROM orders
WHERE shipped_date IS NULL;

-- Calculate the price for each product with its name in each order after discount is applied.
SELECT
    o.order_id,
    p.product_name,
    od.unit_price,
    od.quantity,
    od.discount,
    TRUNC(CAST(od.unit_price * od.quantity * (1 - od.discount) AS numeric), 2) AS discounted_price
FROM
    orders o
JOIN
    order_details od ON o.order_id = od.order_id
JOIN
    products p ON od.product_id = p.product_id;

```
