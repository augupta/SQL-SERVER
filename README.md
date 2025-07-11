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

# Inserting Data using SELECT INTO statement to create a new table from the data of existing table or just insert the data from one table to another.
INSERT INTO tem_emp(emp_id, name)
SELECT emp_id, name FROM employees;
OR
SELECT emp_id, name INTO new_emp FROM employees;

# TOP & OFFSET FETCH
SELECT TOP(10) * FROM employees;
SELECT * FROM employees ORDER BY emp_ID OFFSET 10 ROWS FETCH NEXT 20 ROWS ONLY;

# TRANSACTION/CONCURRENCY are key concepts which are used to ensure data integrity and consistency when multiple users or processes accessing and modifying the data at the same time.
Transaction allows either full data to process or no data. It doesn't allow partial data to process which helps in preventing the incomplete or incorrect data changes in table.
# ACID Properties:



# Syntax of TRANSACTION
BEGIN TRANSACTION
UPDATE employees SET salary = salary+1000 WHERE emp_id=1;
COMMITT TRANSACTION

# CONCURRENCY is a situation when multiple users or processes accessing and modifying the data at the same time. It is common in multiple user environments.
Shared lock: when two users are trying to read the data from same table at same time, they will get the shared lock. It is shareable to other user.
Exclusive lock: when two users are trying to modify, alter or update the data in a table. The command of user gets commited first who process first and the command of the second user will go in processing. It will process after the first ones's.

# VIEW- Virtual table, doesnt store the data, simplest the complex queries, enhances the security, encourage the reusability, data abstraction
