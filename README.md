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
