# Introduction

**what is a Database?** 

A database is a collection of data stored in a format that can be easily be accessed. 

**DBMS (Database Management System):** 

Type1: Relational - MySQL, SQL Server, Oracle

Type2: NoSQL

# Ch2

## 2-1: SELECT Clause

Execute: ctrl+shift+enter

annotation:  - -

```sql
-- Select Statement
USE sql_store;

SELECT *
FROM customers
WHERE customer_id = 1
ORDER BY first_name
```

```sql
SELECT 
	first_name, 
	last_name, 
	points, 
	(points + 10) * 100 AS 'discount factor'
FROM customers

-- Replace Duplicate
SELECT DISTINCT state
FROM customers
```

```sql
-- EXE
-- Return all the product
--   name
--   unit price
--   new price (unit price * 1.1)

SELECT 
	name,
	unit_price,
	unit_price * 1.1 AS 'new price'
FROM products
```

## 2-2: WHERE Clause

```sql
SELECT *
FROM Customers
WHERE points > 3000
WHERE birth_date > '1990-01-01'
WHERE states <> 'VA'

-- <=, >=, !=, <>
```

### 2-2-1: NOT, AND, OR Operator

```sql
SELECT *
FROM customers
-- WHERE birth_date > '1990-01-01' OR (points > 1000 AND state = 'VA')
WHERE NOT (birth_date > '1990-01-01' OR points > 1000)

-- NOT AND OR
```

```sql
-- EXE
-- From the order_items table, get the items
--     for order #6
--     where the total price is greater than 30

SELECT *
FROM order_items
WHERE order_id = 6 AND quantity * unit_price > 30
```

### 2-2-2: IN Operator

```sql
SELECT *
FROM customers
WHERE state NOT IN ('VA', 'FL', 'GA')
```

```sql
-- EXE
-- Return products with
--    quantity in stock equal to 49, 38, 72

SELECT *
FROM products
WHERE quantity_in_stock IN (49, 38, 72)
```

### 2-2-3: BETWEEN Operator

```sql
SELECT *
FROM customers
WHERE points BETWEEN 1000 AND 3000
```

```sql
-- EXE
-- Return customers born
-- 					between 1/1/1990 and 1/1/2000

SELECT *
FROM customers
WHERE birth_date BETWEEN '1990/01/01' AND '2000/01/01'
```

### 2-2-4: LIKE Operator

```sql
SELECT *
FROM customers
-- WHERE last_name LIKE '%b%'
-- %b, b%, brush%
WHERE last_name LIKE 'b____y'
-- % any number of characters
-- _ single characters
```

```sql
-- EXE
-- Get the customers whose
--    addresses contain TRAIL or AVENUE

SELECT *
FROM customers
WHERE address LIKE '%TRAIL%' OR 
      address LIKE '%AVENUE%'
      
-- EXE
-- Get the customers whose
--    phone number end with 9

SELECT *
FROM customers
WHERE phone LIKE '%9'
```

### 2-2-5: REGEXP

```sql
SELECT *
FROM customers
WHERE last_name REGEXP 'field|mac|rose'
```

```sql
SELECT *
FROM customers
WHERE last_name REGEXP '[gim]e'

-- ^ beginning
-- $ end
-- | logical or
-- 'e[fmq]', '[a-h]e'
```

```sql
-- EXE
-- Get the customers whose
--    first names are ELKA or AMBUR
--    last names end with EY or ON
--    last names start with MY or contains SE
--    last names contain B followed by R or U

SELECT *
FROM customers
-- Q1: WHERE first_name REGEXP 'ELKA|AMBUR'

-- Q2: WHERE last_name REGEXP 'EY$|ON$'

-- Q3: WHERE last_name REGEXP '^MY|SE' 

-- Q4: WHERE last_name REGEXP 'B[RU]'
```

### 2-2-6: IS NULL

```sql
SELECT *
FROM customers
WHERE phone IS NULL
```

```sql
-- EXE
-- Get the orders that are not shipped

SELECT *
FROM orders
WHERE shipper_id IS NULL
```

## 2-3: ORDER BY Clause

```sql
SELECT *
FROM customers
-- ORDER BY first_name DESC
ORDER BY state, first_name
```

```sql
-- EXE
-- order items with #2 with total price in decreasing order

SELECT *, quantity * unit_price AS total_price
FROM order_items
WHERE order_id = 2
ORDER BY total_price DESC
```

## 2-4: LIMIT Clause

```sql
SELECT *
FROM customers
LIMIT 3     --(1-3)
LIMIT 6, 3  --(7-9)
```

```sql
-- EXE
-- Get the top three loyal customers

SELECT * 
FROM customers
ORDER BY points DESC
LIMIT 3
```

# Ch3

## 3-1: Join

### 3-1-1: Inner Joins

```sql
SELECT order_id, o.customer_id, first_name, last_name
FROM orders o
JOIN customers c 
		ON o.customer_id = c.customer_id
```

```sql
-- EXE 
-- order_id, product_id, name, quantity, unit_price

SELECT order_id, oi.product_id, name, quantity, oi.unit_price
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
```

### 3-1-2: Joining Across Databases

```sql
SELECT *
FROM order_items oi
JOIN sql_inventory.products p ON oi.product_id = p.product_id  
```

### 3-1-3: Self Joins

```sql
USE sql_hr;

SELECT e.employee_id, e.first_name, m.first_name AS manager
FROM employees e
JOIN employees m ON e.reports_to = m.employee_id
```

### 3-1-4: Joining Multiple Table

```sql
USE sql_store;

SELECT 
	o.order_id,
	o.order_date,
	c.first_name,
	c.last_name,
	os. name AS status
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN order_statuses os ON o.status = os.order_status_id
```

```sql
-- EXE 
-- Join payment methos & clients

USE sql_invoicing;

SELECT 
	p.date,
	p.invoice_id,
	p.amount,
	c.name AS Client, 
	pm.name AS 'payment method'  
FROM payments p
JOIN clients c 
	ON p.client_id = c.client_id
JOIN payment_methods pm 
	ON p.payment_method = pm.payment_method_id
```

### 3-1-5: Compound Join Conditions

```sql
SELECT *
FROM order_items oi
JOIN order_item_notes oin 
	ON oi.order_id = oin.order_id 
    AND oi.product_id = oin.product_id
```

### 3-1-6: Implicit Join Syntax

```sql
-- NOT comment
SELECT *
FROM orders o, customers c
WHERE o.customer_id = c.customer_id
```

### 3-1-7: Outer Joins

```sql
SELECT 
		c.customer_id,
    c.first_name,
    o.order_id
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.customer_id
ORDER BY c.customer_id

-- LEFT JOIN: the 1st remains
-- RIGHT JOIN: the 2nd remains
```

```sql
SELECT
	p.product_id,
	p.name,
	oi.quantity
FROM products p
LEFT JOIN order_items oi
	ON p.product_id = oi.product_id
```

### 3-1-8: Outer Joins Between Multiple Tables

```sql
SELECT 
	c.customer_id,
	c.first_name,
	o.order_id, 
	sh.name AS shipper
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.customer_id
LEFT JOIN shippers sh
	ON o.shipper_id = sh.shipper_id
ORDER BY c.customer_id
```

```sql
SELECT
	o.order_date,
	o.order_id,
	c.first_name,
	sh.name AS shipper,
	os.name AS status
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id
LEFT JOIN shippers sh ON o.shipper_id = sh.shipper_id
LEFT JOIN order_statuses os ON o.status = os.order_status_id
ORDER BY status
```

### 3-1-9: Self Outer Joins

```sql
SELECT 
	e.employee_id,
	e.first_name,
	m.first_name AS manager
FROM employees e
LEFT JOIN employees m ON e.reports_to = m.employee_id
```

## 3-2: USING Clause

```sql
SELECT 
	order_id, 
	c.first_name, 
	sh.name AS shipper
FROM orders o
JOIN customers c 
	USING (customer_id)
LEFT JOIN shippers sh 
	USING (shipper_id)

-- when colume name is the same
```

```sql
SELECT *
FROM order_items oi
JOIN order_item_notes oin
	USING (order_id, product_id)
```

```sql
-- EXE 
-- date client amount name

SELECT 
	p.date,
	c.name AS client,
	p.amount,
	pm.name
FROM payments p
JOIN clients c
	USING (client_id)
JOIN payment_methods pm 
	ON p.payment_method = pm.payment_method_id
```

## 3-3: Other Simpler Joins

### 3-3-1: Natural Joins

```sql
SELECT 
	o.order_id,
  c.first_name
FROM orders o
NATURAL JOIN customers c
```

### 3-3-2: Cross Joins

```sql
SELECT 
	c.first_name AS customer, 
    p.name AS product
FROM customers c, products p 
-- CROSS JOIN products p
ORDER BY c.first_name
```

```sql
-- EXE
-- Do a cross join between shipper & products
--  using both implicit & explicit syntax

SELECT 
	sh.name AS shipper,
    p.name AS prduct
FROM shippers sh, products p
-- CROSS JOIN products p
ORDER BY shipper
```

### 3-3-3: Unions

```sql
SELECT 
	order_id,
	order_date,
	'Active' AS statues
FROM orders
WHERE order_date >= '2019-01-01'
UNION
SELECT 
	order_id,
  order_date,
  'Archived' AS statues
FROM orders
WHERE order_date < '2019-01-01'
```

```sql
SELECT first_name AS fullname
FROM customers 
UNION
SELECT name
FROM shippers

-- same number of column
-- the 1st select 'string' determines the name of column
```

```sql
-- EXE 
-- customer_id, first name, points, type
-- 0-2k Bronze, 2k-3k Silver, 3k Gold

SELECT
	customer_id,
  first_name,
  points,
  'Bronze' AS type
FROM customers
Where points < 2000
UNION
SELECT
	customer_id,
  first_name,
  points,
  'Silver' AS type
FROM customers
Where points BETWEEN 2000 AND 3000
UNION
SELECT
	customer_id,
  first_name,
  points,
  'Gold' AS type
FROM customers
Where points > 3000
ORDER BY first_name
```

# Ch4

## 4-1: Column Attributes

![image.png](attachment:e73bf903-f4d3-4727-9726-584f002597ee:image.png)

## 4-2: Row

### 4-2-1: Inserting a Single Row

```sql
INSERT INTO customers
VALUES (DEFAULT, 
		'John', 
        'Smith', 
        '1990-01-01',
        NULL,
        'address',
        'city',
        'CA',
        DEFAULT)
```

```sql
INSERT INTO customers (
	first_name,
    last_name,
    birth_date,
    address,
    city,
    state)
VALUES (
	'John', 
	'Smith', 
	'1990-01-01',
	'address',
	'city',
	'CA')
```

### 4-2-2: Inserting Multiple Rows

```sql
INSERT INTO shippers (name)
VALUES ('shipper1'),
       ('shipper2'),
       ('shipper3')
```

```sql
-- EXE 
-- Insert 3 rows in the products table

INSERT INTO products (name, quantity_in_stock, unit_price)
VALUES ('name1', '1', '1'), 
	     ('name2', '2', '2'), 
       ('name3', '3', '3')
```

### 4-2-3: Inserting Hierachical Rows

```sql
INSERT INTO orders (customer_id, order_date, status)
VALUES (1, '2019-01-02', 1);

INSERT INTO order_items
VALUE (LAST_INSERT_ID(), 1, 1, 2.95),
      (LAST_INSERT_ID(), 2, 1, 1.95)
```

### 4-2-4: Copy Table

```sql
CREATE TABLE order_archived AS
SELECT * FROM orders
```

![image.png](attachment:b125e873-27ad-412e-ae44-d700b99f0d3b:image.png)

```sql
INSERT INTO order_archived
SELECT *
FROM orders
WHERE order_date < '2019-01-01'
```

```sql
-- EXE 
-- create a copy put in 'invoices archive'
--   client_id - name
--   have payment_date

CREATE TABLE invoices_archived AS
SELECT
	i.invoice_id,
    i.number,
    c.name AS client,
    i.invoice_total,
    i.payment_total,
    i.invoice_date,
    i.payment_date,
    i.due_date
FROM invoices i
JOIN clients c 
	USING (client_id)
WHERE payment_date IS NOT NULL
```

### 4-2-5: Updating a Single Row

```sql
UPDATE invoices
SET payment_total = 10, payment_date = '2019-03-01'
WHERE invoice_id = 1
```

```sql
UPDATE invoices
SET payment_total = DEFAULT, payment_date = NULL 
WHERE invoice_id = 1
```

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5, 
	payment_date = due_date
WHERE invoice_id = 3
```

### 4-2-6: Updating Multiple Rows

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5, 
	payment_date = due_date
WHERE client_id IN (3, 4)
```

```sql
-- EXE 
-- Write a SQL statement to
--  give any customers born before 1990
--  50 extra points

UPDATE customers
SET points = points + 50
WHERE birth_date < '1990-01-01'
```

### 4-2-7: Subqueries in Updates

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5, 
	payment_date = due_date
WHERE client_id = 

-- Subquery is a select statement within another SQL statement
		(SELECT client_id
		FROM clients
		WHERE name = 'Myworks')
```

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5, 
	payment_date = due_date
WHERE client_id IN 
		(SELECT client_id
		FROM clients
		WHERE state IN ('CA', 'NY'))
```

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5, 
	payment_date = due_date
-- SELECT *
-- FROM invoices
WHERE payment_date IS NULL
```

```sql
-- EXE 
-- update comment for customers with points larger than 3000

UPDATE orders
SET comments = 'Gold'
WHERE customer_id IN 
	(SELECT customer_id
    FROM customers
    WHERE points > 3000)
```

### 4-2-8: Deleting Rows

```sql
DELETE FROM invoices
WHERE client_id = (
	SELECT client_id
	FROM clients
	WHERE name = 'myworks')
```

## 4-3: Restoring the Databases

![image.png](attachment:4b9fda33-3bb1-48b6-a5ab-87d51a7d0978:image.png)

# Ch5

## 5-1: Aggregate Function

```sql
SELECT 
	MAX(invoice_total) AS highest,
    MIN(invoice_total) AS lowest,
    AVG(invoice_total) AS average,
    SUM(invoice_total * 1.1) AS total,
    COUNT(invoice_total) AS number_of_invoices,
    COUNT(payment_date) AS count_of_payments,
    COUNT(*) AS total_records,
    COUNT(DISTINCT client_id) AS DIS_total_records
FROM invoices 
WHERE invoice_date > '2019-07-01'
```

```sql
-- EXE 
-- date_range 			total_sales	total_payments What_we_expect
-- First half of 2019 
-- Second half of 2019
-- Total

SELECT
	'First half of 2019' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-01-01' AND '2019-06-30'
UNION
SELECT
	'Second half of 2019' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-07-01' AND '2019-12-31'
UNION
SELECT
	'Total' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-01-01' AND '2019-12-31'
```

## 5-2: GROUP BY Clause

```sql
SELECT
	state,
    city,
	SUM(invoice_total) AS total_sales
FROM invoices i
JOIN clients 
	USING(client_id)
-- WHERE invoice_date >= '2019-07-01'
GROUP BY state, city
-- ORDER BY total_sales DESC 

-- pay attention to the order of statement
-- SELECT > FROM > JOIN > WHERE > GROUP BY > ORDER BY
```

```sql
-- EXE
-- date; payment_method; total_payment

SELECT
	date,
    name AS payment_method,
    SUM(amount) AS total_payments
FROM payments p 
JOIN payment_methods pm 
	ON p.payment_method = pm.payment_method_id
GROUP BY date, payment_method
ORDER BY date
```

## 5-3: HAVING Clause

```sql
SELECT
	client_id,
    SUM(invoice_total) AS total_sales,
    COUNT(*) AS number_of_invoices
FROM invoices
GROUP BY client_id
HAVING total_sales > 500 AND number_of_invoices > 5

-- WHERE used before GROUP BY
-- HAVING used after it
```

```sql
-- EXE
-- Get the customers
-- 	  located in Virginia
--    who have spent more than $100

SELECT 
	c.customer_id,
    c.first_name,
    c.last_name,
    c.birth_date,
    c.phone,
    c.address,
    c.city,
    c.state,
    c.points,
    SUM(oi.quantity * oi.unit_price) AS Total_Spent
FROM customers c
LEFT JOIN orders o
	USING(customer_id)
LEFT JOIN order_items oi
	USING(order_id)
WHERE state = 'VA'
GROUP BY customer_id
HAVING Total_Spent > 100
```

## 5-4: ROLLUP Operator

```sql
SELECT
	state,
    city,
    SUM(invoice_total) AS total_sales
FROM invoices i
JOIN clients c USING (client_id)
GROUP BY state, city WITH ROLLUP
```

```sql
-- EXE 
-- payment_method; total

SELECT
	pm.name AS payment_method,
    SUM(amount) AS Total
FROM payments p
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
GROUP BY pm.name WITH ROLLUP
```

# Ch6

## 6-1: Subqueries

### 6-1-1: Introduction

```sql
SELECT *
FROM products
WHERE unit_price > (
	SELECT unit_price
    FROM products
    WHERE product_id = 3)
```

```sql
-- EXE 
-- Find employees whose earn more than average

SELECT *
FROM employees
WHERE salary > (
	SELECT AVG(salary)
    FROM employees)
```

### 6-1-2: vs JOIN

execution time & readablilty

```sql
-- EXE
-- Find customer with id = 3, 
--    select customer_id, first_name, last name

-- BY Subquery Method
SELECT
	customer_id, 
    first_name,
    last_name
FROM customers
WHERE customer_id IN (
	SELECT customer_id
	FROM orders
	WHERE order_id IN (
		SELECT order_id
		FROM order_items
		WHERE product_id = 3)
		)
		
		
-- BY JOIN Method
SELECT DISTINCT
	customer_id, 
    first_name,
    last_name
FROM customers c
JOIN orders USING (customer_id)
JOIN order_items USING (order_id)
WHERE product_id = 3
```

### 6-1-3: in SELECT Clause

```sql
SELECT 
	invoice_id,
    invoice_total,
    (SELECT AVG (invoice_total)
		FROM invoices) AS invoice_average,
	invoice_total - (SELECT invoice_average) AS difference
FROM invoices
```

```sql
SELECT DISTINCT
	client_id,
    c.name,
	(SELECT SUM(invoice_total) FROM invoices WHERE client_id = c.client_id) AS total_sales,
	(SELECT AVG(invoice_total) FROM invoices) AS average,
	(SELECT total_sales - average) AS difference
FROM clients c
```

### 6-1-4: in FROM Clause

```sql
SELECT *
FROM (
	SELECT DISTINCT
		client_id,
		c.name,
		(SELECT SUM(invoice_total) FROM invoices WHERE client_id = c.client_id) AS total_sales,
		(SELECT AVG(invoice_total) FROM invoices) AS average,
		(SELECT total_sales - average) AS difference
	FROM clients c
) AS sales_summury
WHERE total_sales IS NOT NULL
```

## 6-2: Operator & Keyword

### 6-2-1: IN Operator

```sql
SELECT *
FROM products
WHERE product_id NOT IN (
	SELECT DISTINCT product_id
	FROM order_items)
```

```sql
-- EXE 
-- Find clients without incoices

SELECT *
FROM clients
WHERE client_id NOT IN (
	SELECT DISTINCT client_id
	FROM invoices)
```

### 6-2-2: ALL Keyword

```sql
-- SELECT *
-- FROM invoices
-- WHERE invoice_total > (
-- 	SELECT MAX(invoice_total)
-- 	FROM invoices
-- 	WHERE client_id = 3
-- )

SELECT *
FROM invoices
WHERE invoice_total > ALL (
	SELECT invoice_total
    FROM invoices
    WHERE client_id = 3
)
```

### 6-2-3: ANY Keyword

```sql
-- SELECT *
-- FROM clients
-- WHERE client_id IN (
-- 	SELECT client_id
-- 	FROM invoices
-- 	GROUP BY client_id
-- 	HAVING COUNT(*) >= 2
-- )

SELECT *
FROM clients
WHERE client_id = ANY (
	SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
)
```

### 6-2-4: EXISTS Operator

```sql
SELECT *
FROM clients c
WHERE EXISTS (
	SELECT client_id
    FROM invoices
    WHERE client_id = c.client_id
)
```

```sql
-- EXE
-- products that have never been ordered

SELECT *
FROM products p
WHERE NOT EXISTS (
	SELECT product_id
    FROM order_items
    WHERE product_id = p.product_id
)    

```

## 6-3: Correlated Subqueries

```sql
-- for each employee
--     calculate the AVG salary for employee.office
--     return  the employee if salary > AVG

SELECT *
FROM employees e
WHERE salary > (
	SELECT AVG(salary)
    FROM employees
    WHERE office_id = e.office_id
)
```

```sql
-- EXE
-- get invoices > client's average invoice amount

SELECT *
FROM invoices i
WHERE invoice_total > (
	SELECT AVG(invoice_total)
    FROM invoices
    WHERE client_id = i.client_id
)
```

# Ch7

## 7-1: Numeric Functions

```sql
SELECT ROUND(5.76)       -- 6
SELECT ROUND(5.76, 1)    -- 5.8
SELECT TRUNCATE(5.76, 1) -- 5.7
SELECT CEILING(5.2)      -- 6
SELECT FLOOR(5.7)        -- 6
SELECT ABS(-5.2)         -- 5.2
SELECT RAND()            -- random [0, 1]
```

More Information: 

[MySQL :: MySQL 8.4 Reference Manual :: 14.6 Numeric Functions and Operators](https://dev.mysql.com/doc/refman/8.4/en/numeric-functions.html)

## 7-2: String Functions

```sql
SELECT LENGTH('sky')   -- 3
SELECT UPPER('sky')    -- SKY
SELECT LOWER('SKY')    -- sky
SELECT LTRIM('   Sky') -- Sky
SELECT RTRIM('Sky   ') -- Sky
SELECT TRIM('  Sky  ') -- Sky
SELECT LEFT('Kindergarten', 4)  --Kind
SELECT RIGHT('Kindergarten', 6) --garten
SELECT SUBSTRING ('Kindergarten', 3, 5) -- nderg
SELECT SUBSTRING ('Kindergarten',3)     -- ndergarten
SELECT LOCATE('n', 'Kindergarten')      -- 3 (N or n is OK)
SELECT LOCATE('q', 'Kindergarten')      -- 0
SELECT LOCATE('garten', 'Kindergarten') -- 7
SELECT REPLACE('Kindergarten', 'garten', 'garden') -- Kindergarden
SELECT CONCAT('first', 'last')  -- firstlast
 
```

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM customers
```

More Information: 

[MySQL :: MySQL 8.4 Reference Manual :: 14.8 String Functions and Operators](https://dev.mysql.com/doc/refman/8.4/en/string-functions.html)

## 7-3: Date

### 7-3-1: Date Function

```sql
SELECT NOW()      -- date time
SELECT CURDATE()  -- date
SELECT CURTIME()  -- time
SELECT YEAR(NOW())
-- MONTH, DAY, HOUR, MINUTE, SECOND
SELECT DAYNAME(NOW())  -- Monday
-- MONTHNAME
SELECT EXTRACT(YEAR FROM NOW())
```

```sql
SELECT *
FROM orders
WHERE YEAR(order_date) >= YEAR(NOW())
```

### 7-3-2: Formatting Dates & Times

```sql
SELECT DATE_FORMAT(NOW(), '%M %d %Y') -- February 18 2025
-- %y 25
-- %Y 2025
-- %m 02
-- %M February
-- %d 18
-- %D 18th
SELECT TIME_FORMAT(NOW(), '%H:%i %p') --09:03 AM
```

More Information: 

[MySQL :: MySQL 8.4 Reference Manual :: 14.7 Date and Time Functions](https://dev.mysql.com/doc/refman/8.4/en/date-and-time-functions.html)

### 7-3-3: Calculating Dates & Times

```sql
SELECT DATE_ADD(NOW(), INTERVAL 1 DAY)
-- 1 YEAR, -1 YEAR
SELECT DATE SUB(NOW(), INTERVAL 1 DAY)
SELECT DATEDIFF('2019-01-05 09:00', '2019-01-01 04:00')  -- 4
SELECT DATEDIFF('2019-01-01 04:00', '2019-01-05 09:00')  -- -4
SELECT TIME_TO_SEC('09:00')  -- 32400
SELECT TIME_TO_SEC('09:00') -  TIME_TO_SEC('09:02') -- -120
```

## 7-4: IFNULL & COALSCE Functions

```sql
SELECT 
	order_id,
    IFNULL(shipper_id, 'Not assigned') AS shipper
FROM orders
```

```sql
SELECT 
	order_id,
    COALESCE(shipper_id, comments, 'Not assigned') AS shipper
FROM orders
```

```sql
-- EXE
-- customers & phone

SELECT 
	CONCAT(first_name, ' ', last_name) AS customers,
	COALESCE(phone, 'Unknown') AS phone
FROM customers
```

## 7-5: IF Function

```sql
SELECT 
	  order_id,
    order_date,
    IF(
		YEAR(order_date) = 2019, 
        'Active', 
        'Archive') AS Category
FROM orders
```

```sql
SELECT 
	p.product_id,
    p.name,
    (SELECT COUNT(order_id) FROM order_items WHERE product_id = p.product_id) AS orders,
    IF(
		(SELECT orders) = 1,
        'Once',
        'Many times'
	) AS frquency
FROM  products p
```

## 7-6: CASE Operator

```sql
SELECT 
	order_id,
    CASE
		WHEN YEAR(order_date) = 2019 THEN 'Active'
        WHEN YEAR(order_date) = 2019 - 1 THEN 'Last Year'
        WHEN YEAR(order_date) < 2019 - 1 THEN 'Archived'
        ELSE 'Future'
	END AS category
FROM orders
```

```sql
SELECT 
	CONCAT(first_name, ' ', last_name),
    points,
    CASE
		WHEN points > 3000 THEN 'Gold'
        WHEN points BETWEEN 2000 AND 3000 THEN 'Silver'
        WHEN points < 2000 THEN 'Bronze'
        ELSE 'NONE'
	END AS category
FROM customers
ORDER BY points DESC
```

# Ch8

## 8-1: Creating Views

```sql
CREATE VIEW sales_by_client AS
SELECT 
	c.client_id,
    c.name,
    SUM(invoice_total) AS total_sales
FROM clients c
JOIN invoices i USING (client_id)
GROUP BY client_id, name
```

```sql
SELECT *
FROM sales_by_client
ORDER BY total_sales DESC
```

```sql
-- EXE 
-- create a view to see the balance for each client.
-- clients_balance
-- client_id, name, balance

CREATE VIEW clients_balance AS
SELECT 
	i.client_id,
    c.name,
    SUM(invoice_total - payment_total) AS balance
FROM invoices i
JOIN clients c USING (client_id)
GROUP BY client_id, name
```

<aside>
💡

Advantages:

- simplify queries
- reduce the impact of changes
- restrict access to the data
</aside>

## 8-2: Altering or Dropping Views

```sql
DROP VIEW sales_by_client
```

```sql
CREATE OR REPLACE VIEW 
```

## 8-3: Updatable Views

```sql
-- IF it doesn't use
	-- DISTINCT
	-- Aggregate Function
	-- GROUP BY / HAVING
	-- UNION

CREATE OR REPLACE VIEW invoices_with_balance AS
SELECT
	invoice_id,
    number,
    client_id,
    invoice_total,
    payment_total,
    invoice_total - payment_total AS balance,
    invoice_date,
    due_date,
    payment_date
FROM invoices 
WHERE (invoice_total - payment_total) > 0
```

```sql
DELETE FROM invoices_with_balance
WHERE invoice_id = 1
```

```sql
UPDATE invoices_with_balance
SET due_date = DATE_ADD(due_date, INTERVAL 2 DAY)
```

<aside>
💡

WITH CHECK OPTION

used to prevent delete of rows

![image.png](attachment:d61afffd-be48-4301-ba24-bc41203bb5b5:image.png)

</aside>

# Ch9

## 9-1: Stored Procedure

Stored Procedure

- Store & Organize SQL
- Faster Execution
- Data Security

### 9-1-1: Creating a Stored Procedure

**Method 1:**

```sql

DELIMITER $$
CREATE PROCEDURE get_clients()
BEGIN
	SELECT * FROM clients;
END$$

DELIMITER ;
```

![image.png](attachment:05689a77-af95-4062-b772-f92cb3642f91:image.png)

```sql
CALL get_clients()
```

```sql
-- EXE 
-- Create a stored procedure called
--    get_invoices_with_balance
--    to return all the invoices with a balance > 0

DELIMITER $$

CREATE PROCEDURE get_invoices_with_balance()
BEGIN
	SELECT *
    FROM invoices
    WHERE invoice_total - payment_total > 0;
END $$

DELIMITER ;
```

**Method 2:**

![image.png](attachment:523086cf-88e2-4f2f-ae8a-fb58e8029247:image.png)

### 9-1-2: Dropping Procedure

```sql
DROP PROCEDURE IF EXISTS get_clients
```

## 9-2: Parameter

### 9-2-1: Basic

```sql
DROP PROCEDURE IF EXISTS get_clients_by_state;

DELIMITER $$
CREATE PROCEDURE get_clients_by_state
(
	state CHAR(2)
)
BEGIN
	IF state IS NULL THEN
		SET state = 'CA';
	END IF;
	
	SELECT * FROM clients c
  WHERE c.state = state;
END$$

DELIMITER ;
```

```sql
-- EXE 
-- Write a stored procedure to return invoices
-- for a given client
--
-- get_invoices_by_client

DROP PROCEDURE IF EXISTS get_invoices_by_client;

DELIMITER $$
CREATE PROCEDURE get_invoices_by_client
(
	client_id INT
)
BEGIN
	SELECT *
    FROM invoices i
    WHERE i.client_id = client_id;
END $$

DELIMITER ;
```

### 9-2-2: Default Value

```sql
DROP PROCEDURE IF EXISTS get_clients_by_state;

DELIMITER $$
CREATE PROCEDURE get_clients_by_state
(
	state CHAR(2)
)
BEGIN
	SELECT * FROM clients c
	WHERE c.state = IFNULL(state, c.state);
END$$

DELIMITER ;
```

```sql
-- Write a stored proceduce called get_payments
-- with 2 parameters
--
--	client_id => INT
-- payment_method_id => TINYINT

DROP PROCEDURE IF EXISTS get_payments;

DELIMITER $$
CREATE PROCEDURE get_payments
(
	client_id INT,
    payment_method_id TINYINT
)

BEGIN
	SELECT *
    FROM payments p
    WHERE 
		p.client_id = IFNULL(client_id, p.client_id) AND 
        p.payment_method = IFNULL(payment_method_id, p.payment_method);
END $$

DELIMITER ;
```

### 9-2-3: Validation

SQLstate errors

[Listing of SQLSTATE values](https://www.ibm.com/docs/en/i/7.3?topic=codes-listing-sqlstate-values)

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `make_payment`(
	invoice_id INT,
    payment_amount DECIMAL(9, 2),
    payment_date DATE
)
BEGIN
	IF payment_amount <= 0 THEN
		SIGNAL SQLSTATE '22003' 
			SET MESSAGE_TEXT = 'Invalid payment amount';
    END IF;    
        
	UPDATE invoices i
    SET
		i.payment_total = payment_amount,
        i.payment_date = payment_date
	WHERE i.invoice_id = invoice_id;
END
```

### 9-2-4: Output Paramenter

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_unpaid_invoices_for_client`(
	client_id INT,
    OUT invoices_count INT,
    OUT invoices_total DECIMAL(9, 2)
)
BEGIN
	SELECT COUNT(*), SUM(invoice_total)
    INTO invoices_count, invoices_total
    FROM invoices i
    WHERE i.client_id = client_id 
		AND payment_total = 0;
END
```

## 9-3: Variable

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_risk_factor`()
BEGIN
	DECLARE risk_factor DECIMAL(9, 2) DEFAULT 0;
    DECLARE invoices_total DECIMAL(9, 2);
    DECLARE invoices_count INT;
    
    SELECT COUNT(*), SUM(invoice_total)
    INTO invoices_count, invoices_total
    FROM invoices;
    
    SET risk_factor = invoices_total / invoices_count * 5;
    
    SELECT risk_factor;
-- risk_factor = invoices_total / invoices_count * 5
END
```

## 9-4: Function

```sql
CREATE DEFINER=`root`@`localhost` FUNCTION `get_risk_factor_for_client`(
	client_id INT
) RETURNS int
    READS SQL DATA
BEGIN
	DECLARE risk_factor DECIMAL(9, 2) DEFAULT 0;
    DECLARE invoices_total DECIMAL(9, 2);
    DECLARE invoices_count INT;
    
    SELECT COUNT(*), SUM(invoice_total)
    INTO invoices_count, invoices_total
    FROM invoices i
    WHERE i.client_id = client_id;
    
    SET risk_factor = invoices_total / invoices_count * 5;
    
	RETURN IFNULL(risk_factor, 0);
END
```

```sql
SELECT
	client_id,
    name,
    get_risk_factor_for_client(client_id) AS risk_factor
FROM clients
```

```sql
DROP FUNCTION IF EXISTS get_risk_factor_for_client
```

# Ch10

## 10-1: Trigger

A block of SQL code that automatically gets executed before or after an insert, update or delete statement. 

```sql
DELIMITER $$

CREATE TRIGGER payments_after_insert
	AFTER INSERT ON payments
    FOR EACH ROW
BEGIN
	UPDATE invoices
    SET payment_total = payment_total + NEW.amount
    WHERE invoice_id = NEW.invoice_id;
END $$

DELIMITER ;
```

```sql
-- EXE
-- Create a trigger that gets fired when we delete a payment.

DELIMITER $$

CREATE TRIGGER payments_after_delete
	AFTER DELETE ON payments
    FOR EACH ROW
BEGIN
	UPDATE invoices
    SET payment_total = payment_total - OLD.amount
    WHERE invoice_id = OLD.invoice_id;
END $$

DELIMITER ;
```

View trigger

```sql
SHOW TRIGGERS LIKE 'payments%'
```

Dropping trigger

```sql
DROP TRIGGER IF EXISTS payments_after_delete2
```

for auditing

```sql
USE sql_invoicing;

CREATE TABLE payments_audit
(
	client_id		INT				NOT NULL,
    date			date			NOT NULL,
    amount			DECIMAL(9, 2)	NOT NULL,
    action_type		VARCHAR(50)		NOT NULL,
    action_date		DATETIME		NOT NULL
)
```

```sql
    INSERT INTO payments_audit
    VALUES (NEW.client_id, NEW.date, NEW.amount, 'Insert', NOW());
```

## 10-2: Event

A block of SQL code that gets executed according to a schedule

```sql
DELIMITER $$

CREATE EVENT yearly_delete_stale_audit_rows
ON SCHEDULE 
	-- AT '2019-05-01'
    EVERY 1 YEAR STARTS '2019-01-01' ENDS '2029-01-01'
DO BEGIN
	DELETE FROM payments_audit
    WHERE action_date < NOW() - INTERVAL 1 YEAR;
END $$ 

DELIMITER ;
```

```sql
SHOW EVENTS LIKE 'yearly%';
DROP EVENT IF EXISTS yearly_delete_stale_audit_rows;
ALTER EVENT yearly_delete_stale_audit_rows ENABLE;
```

# Ch11

A group of SQL statements that represent a single unit of work

ACID properity: 

- Atomicity
- consistency
- Isolation
- Durability

```sql
USE sql_store;

START TRANSACTION;

INSERT INTO orders (customer_id, order_date, status)
VALUE (1, '2019-01-01', 1);

INSERT INTO order_items
VALUES (LAST_INSERT_ID(), 1, 1, 1);

COMMIT;
```

## 11-1: Concurrency
