### Basics
```sql
USE sql_store;
SELECT *
FROM customers
WHERE state = ‘CA’
ORDER BY first_name
LIMIT 3;
```
- SQL is not a case-sensitive language.
- In MySQL, every statement must be terminated with a semicolon.
### Comments
- We use comments to add notes to our code.
    -- This is a comment and it won’t get executed.
### SELECT Clause
 -  Using expressions
    ```sql
    SELECT (points * 10 + 20) AS discount_factor
    FROM customers
- Order of operations:
  - Parenthesis
  - Multiplication / division
  - Addition / subtraction
 -  Removing duplicates
    ```sql
    SELECT DISTINCT state
    FROM customers
### WHERE Clause
 - We use the WHERE clause to filter data.
 - Operators:  >, <, >=, <=, =, <>, !=
### Logical Operators
- AND (both conditions must be True)
    ```sql
    SELECT *
    FROM customers
    WHERE birthdate > ‘1990-01-01’ AND points > 1000
- OR (at least one condition must be True)
    ```sql
    SELECT *
    FROM customers
    WHERE birthdate > ‘1990-01-01’ OR points > 1000
- NOT (to negate a condition)
    ```sql
    SELECT *
    FROM customers
    WHERE NOT (birthdate > ‘1990-01-01’)
### IN Operator
- Returns customers in any of these states: VA, NY, CA
    ```sql
    SELECT *
    FROM customers
    WHERE state IN (‘VA’, ‘NY’, ‘CA’)
### BETWEEN Operator
- Returns customers in the `points` range from 100 to 200
    ```sql
    SELECT *
    FROM customers
    WHERE points BETWEEN 100 AND 200
### LIKE Operator
- Returns customers whose first name starts with b
    ```sql
    SELECT *
    FROM customers
    WHERE first_name LIKE ‘b%’
- %: any number of characters
- _: exactly one character
### REGEXP Operator
- Returns customers whose first name starts with a
    ```sql
    SELECT *
    FROM customers
    WHERE first_name REGEXP ‘^a’
- ^: beginning of a string
- $: end of a string
- |: logical OR
- [abc]: match any single characters
- [a-d]: any characters from a to d
- More Examples
  - Returns customers whose first name ends with EY or ON
    ``` 
    WHERE first_name REGEXP ‘ey$|on$’
  - Returns customers whose first name starts with MY or contains SE
    ```    
    WHERE first_name REGEXP ‘^my|se’
  - Returns customers whose first name contains B followed by R or U
    ```
    WHERE first_name REGEXP ‘b[ru]’
### IS NULL Operator
- Returns customers who don’t have a phone number
    ```sql
    SELECT *
    FROM customers
    WHERE phone IS NULL
### ORDER BY Clause
- Sort customers by state (in ascending order), and then by their first name (in descending order)
    ```sql
    SELECT *
    FROM customers
    ORDER BY state, first_name DESC
### LIMIT Clause
- Return only 3 customers
    ```sql
    SELECT *
    FROM customers
    LIMIT 3
- Skip 6 customers and return 3
    ```sql
    SELECT *
    FROM customers
    LIMIT 6, 3
### JOINS
- Joins are used to combine columns from multiple table.
### Inner Joins
- We can use only join keyword as inner keyword is optional
    ```sql
    SELECT name, o.product_id, quantity, o.unit_price
    FROM order_items o
    JOIN products p
	ON o.product_id = p.product_id
- In the case, if a column is present in more than one table then we have to use the table name to include that column
### Joining across different database
- In the below mentioned query, `products` table is not in the same table as that of the `order_items` table, rather it is in `sql_inventory`
    ```sql
    SELECT name, o.product_id, quantity, o.unit_price
    FROM order_items o
    JOIN products p
    	ON o.product_id = p.product_id
### Self Join
-    ```sql
        SELECT *
        FROM employees e
        JOIN employees m
        	ON e.reports_to = m.employee_id
### Joining multiple tables
-   ```sql
        SELECT order_id, order_date, c.first_name, c.last_name, os.name AS status
        FROM orders o
        JOIN customers c
        	ON o.customer_id = c.customer_id
        JOIN order_statuses os
        	ON o.status = os.order_status_id;
### Joining a table with composite primary key with other table
- `sql_store` table has two columns named, `order_id` & `product_id` together they served as composite primary key
    ```sql
    SELECT *
    FROM order_items oi
    JOIN order_item_notes oin
	    ON oi.order_id = oin.order_id
        AND oi.product_id = oin.product_id
### Outer Join
- Return all customers whether they have any orders or not
    ```sql
        SELECT *
        FROM customers c
        LEFT JOIN orders o
        ON c.customer_id = o.customer_id
- There are two OUTER join
    - LEFT OUTER JOIN
    - RIGHT OUTER JOIN
- Query with `LEFT OUTER` join will contain all the records from left table.
- Query with `RIGHT OUTER` join will contain all the records from right table.
- `OUTER` keyword is not necessary.
### OUTER join between multiple tables
-    ```sql
        SELECT order_date,
        	   order_id,
               c.first_name,
               s.name AS shipper,
               os.name
        FROM orders o
        JOIN customers c
        		ON o.customer_id = c.customer_id
        LEFT JOIN order_statuses os
        		ON o.status = os.order_status_id
        LEFT JOIN shippers s
	        	ON o.shipper_id = s.shipper_id
- Avoid using different joins (left, right) in the same query.
### Self OUTER join
-    ```sql
        SELECT 
            e.employee_id,
            e.first_name,
            m.fitst_name AS manager
        FROM employees e
        LEFT JOIN employees m
                ON e.reports_to = m.employee_id
### USING clause
- `USING` clause is used to simplify the joining condition in 
-    ```sql
        SELECT order_date,
        	   order_id,
               c.first_name,
               s.name AS shipper,
        FROM orders o
        JOIN customers c
        		USING(customer_id)
        LEFT JOIN shippers s
	        	USING(shipper_id)
### CROSS join
-   ```sql
        SELECT * 
        FROM shippers s
        CROSS JOIN products p
- Second syntax
    ```sql
        SELECT * 
        FROM shippers s,products p
### Union
- Union is use to combine rows from multiple tables.
    ```sql
        SELECT customer_id, 
        	   first_name,
               points,
               'Bronze' AS type
        FROM customers WHERE points < 2000
        UNION
        SELECT customer_id, 
        	   first_name,
               points,
               'Silver' AS type
        FROM customers WHERE points  BETWEEN 2000 AND 3000
        UNION
        SELECT customer_id, 
        	   first_name,
               points,
               'Gold' AS type
        FROM customers WHERE points > 3000
        ORDER BY first_name
- Number of columns in both the querys for union must have same number of columns.
- Column names for the result is taken from the first query.
### Inserting a row
- Syntax-1 (order of the values must be same as that of the order of the columm)
    ```sql
        INSERT INTO customers
        VALUES(
            'Ram',
            'kumar',
            NULL,
            'address'
            'state'
            DEFAULT
        )
- Syntax-2 (order of the values is not important)
    ```sql
        INSERT INTO customers (
            first_name,
            last_name,
            address,
            state,
            points
        )
        VALUES(
            'Ram',
            'kumar',
            NULL,
            'address'
            'state'
            DEFAULT
        )
### Inserting multiple rows
-   ```sql
        INSERT INTO products(
        	name,
            quantity_in_stock,
            unit_price
        )
        VALUES ('Table', 3, 50.0),
        		('Beads', 10, 20.0),
                ('Bead bag', 10, 50.0)
### Inserting Hierarchical rows
- This is used when there is a parent child relation between two tables. That is, when record in a table depends on other table.   
    ```sql
        INSERT INTO orders (customer_id, order_date, status)
        VALUES (1, '2020-01-05', 1);
        INSERT INTO order_items
        VALUES
            (LAST_INSERT_ID(), 1, 1, 2.95)
            (LAST_INSERT_ID(), 2, 1, 3.95)
### Creating a copy of a Table
-   ```sql
        CREATE TABLE invoices_archived AS
        SELECT invoice_id, number, c.name AS client_name, invoice_total, payment_total, invoice_date, due_date, payment_date 
        FROM invoices i
        JOIN clients c
        		USING(client_id)
        WHERE payment_date IS NOT NULL
### Updating a sigle row
-   ```sql
        UPDATE invoices
        SET
            payment_total = invoice_total * 0.5
            payment_date = due_date   -- due_daate is a column name
        WHERE invoice_id = 1
### Subquery in update statement
-   ```sql
        UPDATE orders
        SET comments = "Gold Customers"
        WHERE customer_id IN (SELECT customer_id
        FROM customers
        WHERE points > 3000)
### Delete
- We can also use sub-queries with delete command
    ```sql
        DELETE FROM invoices
        WHERE client_id = 3
### Aggregate Functions
-   These functions only operate only on NON NULL value.
-   Agregate: max(), min(), sum(), avg(), count()
    ```sql
    SELECT 'First half of 2019' AS date_range,
    	   SUM(invoice_total) AS total_sales,
    	   SUM(payment_total) AS total_payments,
           SUM(invoice_total - payment_total) AS what_we_expect
    FROM invoices
    WHERE invoice_date BETWEEN '2019-01-01' AND '2019-06-30'
    UNION
    SELECT 'Second half of 2019' AS date_range,
    	   SUM(invoice_total) AS total_sales,
    	   SUM(payment_total) AS total_payments,
           SUM(invoice_total - payment_total) AS what_we_expect
    FROM invoices
    WHERE invoice_date BETWEEN '2019-07-01' AND '2019-12-31'
    UNION
    SELECT 'TOTAL' AS date_range,
    	   SUM(invoice_total) AS total_sales,
    	   SUM(payment_total) AS total_payments,
           SUM(invoice_total - payment_total) AS what_we_expect
    FROM invoices
    WHERE invoice_date BETWEEN '2019-01-01' AND '2019-12-31'
### Group By
- Whenever we use aggregate functions then we have to use all the columns for GROUP BY.
    ```sql
        SELECT date, 
        	name AS payment_method,
        	SUM(amount) AS total_payments
         FROM payments p
         JOIN payment_methods pm
        		ON p.payment_method = pm.payment_method_id
        GROUP BY date, name
### Having clause
- Having is used to filter data after grouping.
- Columns used in the having condition have to be the part of select clause.
    ```sql
        SELECT c.customer_id,
        		c.first_name,
                c.last_name,
        		SUM(quantity * unit_price) AS totalSpent
        FROM customers c
        JOIN orders o USING(customer_id)
        JOIN order_items oi USING(order_id)
        WHERE state = 'VA'
        GROUP BY 
        	c.customer_id,
        	c.first_name,
            c.last_name
        HAVING totalSpent > 100
 ### Roll UP
- Roll up applyies only to the column that aggregate values
- When we GROUP BY multiple columns and use the ROLL UP operator, then result will be the values for **each GROUP** 
    ```sql
    SELECT name payment_method,  -- no need of writting AS clause
    	   SUM(amount) total
    FROM payments p
    JOIN payment_methods pm
    		ON p.payment_method = pm.payment_method_id
    GROUP BY name WITH ROLLUP