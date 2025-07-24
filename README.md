# SQL-SERVER
# DML Commands: SELECT INSERT UPDATE DELETE
# DDL- CREATE ALTER DROP TRUNCATE
# DCL- GRANT REVOKE DENY, control the access of the data stored in database. only authorise users perform certain action on database.
GRANT permission_typ ON object TO user; GRANT SELECT ON PRODUCTS TO JOHN;
REVOKE permission_typ ON object FROM user; REVOKE INSERT ON PRODUCTS FROM JOHN;

# DATA TYPES- INT, DECIMAL OR NUMERIC, CHAR, VARCHAR, NVARCHAR, DATE DATETIME, BIT, FLOAT, MONEY

# Order in select statement: SELECT FROM WHERE GROUP BY HAVING ORDER BY
 
# Primary Key- is a column or combination of columns that uniquely identifies each row in a table. Each value is unique. no null values are allowed.

# Foreign Key- is a column in a table that creates a link b/w two tables and it takes reference from the primary key of another table. Foreign Key columns must contain the values that matches the value in the referenced primary key column.

# Operators: Arithmetic- add, sub, div, mul.   Comparison- =,<,>,=!...    Logical- AND OR NOT

# Predicates- consitions or expressions use to filter the data, for ex: BETWEEN, IN, LIKE, IS NULL, 

# NULL & NOT NULL
NULL +2 = NULL
NULL= NULL -> FALSE
NULL+ 'MYSTR'= NULL
NULL IS NULL -> TRUE
NULL value is different from '' & zero. NULL represents missing or unknown data.
NOT NULL means that column must has a value.

# Ex of sql command including primary and foreign key
# Select All Enrollments with Student and Course Information
SELECT E.EnrollmentID, S.FirstName, S.LastName, C.CourseName,
E.EnrollmentDate
FROM Enrollments E
JOIN Students S ON E.StudentID = S.StudentID
JOIN Courses C ON E.CourseID = C.CourseID;

# CROSS JOIN

# SELF JOIN

# CTE 
# Using a CTE, list each employee along with the name of their manager.
Table: Employees

emp_id	name	manager_id
1	Raj	   NULL
2	Priya	1
3	Arun	1
4	Meera	2
WITH EmployeeManager AS (
    SELECT
        e.name AS employee_name,
        m.name AS manager_name
    FROM Employees e
    LEFT JOIN Employees m ON e.manager_id = m.emp_id
)
SELECT * FROM EmployeeManager;

# Explanation: e.manager_id = m.emp_id is the key—employee’s manager_id matches manager’s emp_id.
LEFT JOIN ensures the CEO/Head (with NULL manager) is shown with no manager_name.
Aliasing with AS makes column names clear.

# Using SUM(s.total_amount) inside RANK() directly doesn’t work, because window functions can’t reference aggregated columns from GROUP BY. Use a subquery or CTE to calculate the aggregates, then use the window function in the outer query.

Write a query to display, for each product: product_name, category, total quantity sold, total sales amount, the rank of each product by total sales amount (highest = 1), Only include products in the "Electronics" category, Order the results by total sales amount DESC, Use JOIN, GROUP BY, ORDER BY, HAVING, and RANK() window function.

WITH Productsales AS (
  SELECT p.product_name, p.category,
  SUM(s.quantity) AS 'total_quantity_sold',
  SUM(s.total_amount) AS 'total_sales_amount'
  FROM Products p INNER JOIN Sales s 
  ON p.product_id = s.product_id
  GROUP BY p.product_name, p.category
  HAVING p.category='Electronics'
  )
SELECT
  product_name,
  category,
  total_quantity_sold,
  total_sales_amount,
  RANK() OVER(ORDER BY total_sales_amount DESC) AS 'Sales rank'
  FROM Productsales
  ORDER BY total_sales_amount DESC;




# Inserting Data using SELECT INTO statement to create a new table from the data of existing table or just insert the data from one table to another.
INSERT INTO tem_emp(emp_id, name)
SELECT emp_id, name FROM employees;
OR
SELECT emp_id, name INTO new_emp FROM employees;

# TOP & OFFSET FETCH
SELECT TOP(10) * FROM employees;
SELECT TOP 2 name, price FROM Products ORDER BY price DESC;
SELECT * FROM employees ORDER BY emp_ID OFFSET 10 ROWS FETCH NEXT 20 ROWS ONLY;

# TRANSACTION/CONCURRENCY are key concepts which are used to ensure data integrity and consistency when multiple users or processes accessing and modifying the data at the same time.
Transaction allows either full data to process or no data. It doesn't allow partial data to process which helps in preventing the incomplete or incorrect data changes in table.
# ACID Properties:

ACID Properties of Transactions:

Atomicity - All or nothing
Consistency - Data remains valid
Isolation - Concurrent transactions don't interfere
Durability - Committed changes are permanent

# Syntax of TRANSACTION
BEGIN TRANSACTION
UPDATE employees SET salary = salary+1000 WHERE emp_id=1;
COMMITT TRANSACTION

# CONCURRENCY is a situation when multiple users or processes accessing and modifying the data at the same time. It is common in multiple user environments.
Shared lock: when two users are trying to read the data from same table at same time, they will get the shared lock. It is shareable to other user.
Exclusive lock: when two users are trying to modify, alter or update the data in a table. The command of user gets commited first who process first and the command of the second user will go in processing. It will process after the first ones's.

# VIEW- Virtual table, doesnt store the data, simplest the complex queries, enhances the security, encourage the reusability, data abstraction, can only save the SELECT command
CREATE VIEW view_name AS SELECT ....... FROM table_name AS t JOIN table1_name AS t1 ON ...=...;
ALTER VIEW view_name AS SELECT ....... FROM table_name AS t JOIN table1_name AS t1 ON ...=...;
DROP VIEW view_name;

# SQL FUNCTIONS : can simplify the queries and make the codes modular and reusable. Can save the SELECT command and can access the parameter.
# Scalar Function: returns a single value in a result set.
# Table valued Functions: when user is running a query and in the result set if the whole table is getting retrieved then we will call it as table valued function.

CREATE FUNCTION square_fun(num, INT)
RETURNS INT
AS
BEGIN RETURN num*num
END

# WINDOW FUNCTIONS

# Write a query to display each customer_id, order_id, amount, and a running total (cumulative sum) of amount per customer, ordered by order_date.
SELECT 
    customer_id, 
    order_id, 
    amount,
    SUM(amount) OVER (
        PARTITION BY customer_id 
        ORDER BY order_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS [cumulative sum]
FROM Orders
ORDER BY customer_id, order_date;

# Write a query to display each employee’s emp_id, department, salary, and the average salary in their department (as a column for each record).
SELECT emp_id, department, salary,
       AVG(salary) OVER (PARTITION BY department) AS [average salary]
FROM Employees;


# Real world case scenario

Orders
order_id	customer_id	amount	order_date
1	101	800	2024-02-12
2	102	1200	2024-02-13
3	101	500	2024-03-01

Customers
customer_id	name
101	Maya
102	Vipul
103	Sarah

Task:
Write a query to display each customer’s name and their most recent order amount (if any order exists for them).
If no order exists, show NULL for the amount.

How to get only the most recent order’s amount (one row per customer):
Approach 1: Use ROW_NUMBER() in a CTE/subquery to pick the most recent order per customer.

sql
WITH RankedOrders AS (
  SELECT
    c.name,
    o.amount,
    ROW_NUMBER() OVER (
      PARTITION BY c.customer_id
      ORDER BY o.order_date DESC
    ) AS rn
  FROM Customers c
  LEFT JOIN Orders o ON c.customer_id = o.customer_id
)
SELECT name, amount AS 'recent order amount'
FROM RankedOrders
WHERE rn = 1;
The LEFT JOIN ensures all customers are included (even if they have no orders).

The window function assigns a rank (rn=1) to the most recent order (highest order_date) per customer.

The outer query filters to rn=1 (the most recent order or NULL if not found).

Your current query would list as many rows as matching orders exist for a customer (not one row per customer).





# STORED PROCEDURE 
it can save all type of commands ex SELECT, UPDATE, DELETE...

# Syntax to create stored procedure
CREATE PROCEDURE Customer
AS
BEGIN 
SELECT * FROM Customer_data
END

# Syntax to execute stored procedure
exec Customer

OR

CREATE PROCEDURE Customer_info @customer_id
AS
BEGIN SELECT * FROM Customer_data WHERE customer_id= @customer_id
END

exec Customer_info @customer_id=1;



# TRIGGER: 
A trigger in SQL Server is a special type of stored procedure that automatically executes (fires) in response to certain events occurring in the database, such as:


# Create a trigger that logs deletions from the Employees table
CREATE TRIGGER trg_AfterDelete ON Employees
AFTER DELETE
AS
BEGIN
    INSERT INTO EmployeeAudit (EmployeeID, DeletedOn)
    SELECT EmployeeID, GETDATE() FROM deleted;
END;

# Authentication & Authorization
Authentication: verifies the identity of a user
Windows authentication: verifies the identity through windows credentials which are already validated in windows by the user.
SQL Server authentication: verifies the identity within SQL Server by entering login credentials.

Authorization: once user is authenticated, then will check what kind of permissions user has.

# Login & Users
Login: login is created at sql server level and it allows user to connect with sql server instance by entering login credentials.
User: User is created at database level. it allows login to acess specific database. Database administrator creates specific user for specific databases in a server. 

# Creating Index:

Simple index:

CREATE INDEX idx_lastname ON employees(last_name);
Rebuilding Indexes:

Rebuild all indexes (admin):

EXEC sp_MSforeachtable @command1="DBCC DBREINDEX ('?', '', 80)";

# Window Functions
ROW_NUMBER, RANK:

Assign row numbers:

sql
SELECT employee_id, salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num
FROM employees;



# Creating a new table from the data of existing table
CREATE TABLE purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    purchase_date DATE NOT NULL
);

-- INSERT queries for all purchase records
INSERT INTO purchases (purchase_id, customer_id, amount, purchase_date) VALUES
(1, 101, 450.00, '2023-04-03'),
(2, 102, 2500.00, '2023-04-10'),
(3, 103, 1200.00, '2023-04-12'),
(4, 101, 700.00, '2023-04-15'),
(5, 104, 3400.00, '2023-04-18'),
(6, 102, 800.00, '2023-04-19');

SELECT * FROM purchases;

-- Task
-- Create a new table called CustomerTiers using SELECT INTO.
-- The table should contain each distinct customer_id and a column called Tier which is:
-- 'Platinum' if the customer's total purchase amount is ≥ 3000
-- 'Gold' if their total is between 2000 and 2999
-- 'Silver' if their total is between 1000 and 1999
-- 'Bronze' otherwise
-- Only customers who have at least one purchase in April 2023 should be included.

SELECT DISTINCT customer_id,
  CASE
  WHEN SUM(amount)>=3000 THEN 'Platinum'
  WHEN SUM(amount) BETWEEN 2000 and 2999 THEN 'Gold'
  WHEN SUM(amount) BETWEEN 1000 and 1999 THEN 'Silver'
  ELSE 'Bronze'
  END AS 'Tier'
  INTO CustomerTiers FROM purchases
  GROUP BY customer_id;

SELECT * FROM CustomerTiers;



















