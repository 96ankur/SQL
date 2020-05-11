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
        SELECT * 
        FROM shippers s,products p