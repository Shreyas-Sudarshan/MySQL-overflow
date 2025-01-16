create database database_name;
drop database database_name;
use database_name;
show databases;
datatypes in MySQL
integer---
tinyint 1byte
smallint 2bytes
mediumint 3 bytes
int/integer 4 bytes
bigint 8 bytes
floating point
float 4bytes    7 decimal precesion
double 8bytes    15 decimal precesion
decimal(65,35)
bit Values are stored as binary numbers but interpreted as integers when queried.
Boolean true false ,1-0
binary Stores raw binary strings. 101,as1 gets convered to binary form and padded with /0 if required.
Enum and set(contains a set of options to chose from . Enum only 1 can be chosen .set multiple options can be chosen.upto 64
char(0-255) varchar(0-65000)
tinytext mediumtext text largetext blob
date tme datetime timestamp year

integrity constraints
check  age INT CHECK (age >= 0 AND age <= 120)
primary key .emp_id INT PRIMARY KEY
foreign key (empid) reference department(empid)
unique Ensures a column's values are unique but allows one 0 value(multipole null cuz null value not known).
not null
default  country VARCHAR(50) DEFAULT 'India'
composite primary key CREATE TABLE TableName (
    column1 DataType,
    column2 DataType,
    PRIMARY KEY (column1, column2)
);
composite foreign keys
CREATE TABLE OrderDetails (
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id, product_id) REFERENCES Orders(order_id, product_id);
if not exists   --create table if not exists name(a,s,d)
auto_increment usually with primary key. default 1
CREATE TABLE Users (
    user_id INT AUTO_INCREMENT,
    username VARCHAR(50),
    email VARCHAR(100),
    PRIMARY KEY (user_id)
) AUTO_INCREMENT = 100;
only 1 auto_increment per table
we can also name the foreign key. default naming is done by MySQL.
CREATE TABLE OrderDetails (
    order_id INT PRIMARY KEY, 
    product_id INT,
    quantity INT,
    CONSTRAINT order_fk FOREIGN KEY (product_id) REFERENCES Orders(product_id)
);

show tables;set of tables
desc table_name; gives rows and columns
operators----- and ,or ,not, in, between, like ,is null, is not null
in is substitute for or
SELECT * FROM Employees
WHERE department IN ('Sales', 'Marketing', 'HR'); instead of dept='a' and dept='f'
like is used for matching
where name like 'j%'  ---returns name starting from j

temporary tables
similar to table
create temporary table name()
drop temporary table name()
show temp_table_name
insert into temp_name
select * from temp_name

comparison operators----  =,!= or <> not equal to , >,<,>=,<=
not null used when describing table column values, is not null in where comparison
aggregate functions count sum avg max min
distinct ------select distinct name from user
limit. limit no of rows

select keyword order-----
select-> distinct-> from-> join -> where -> group by -> having->order by->limit->offset
SELECT  product_id, 
       SUM(quantity * price) AS total_sales
FROM Orders
JOIN Products ON Orders.product_id = Products.product_id
WHERE order_date >= '2025-01-01'
GROUP BY product_id
HAVING SUM(quantity * price) > 500
ORDER BY total_sales DESC
LIMIT 10 OFFSET 5;

limit -no of rows to display. offset -x no of first rowsd to skip

order by---used with aggregate function which is used in select 
We want to calculate the total sales per product (i.e., quantity * price) and group the result by product_id to get the total sales for each product.
SELECT product_id, 
       SUM(quantity * price) AS total_sales
FROM Orders
GROUP BY product_id;

 In SQL, when you're using GROUP BY, any non-aggregated column in the SELECT clause must be part of the GROUP BY clause as well.
The DISTINCT keyword is generally unnecessary when using GROUP BY, because GROUP BY already ensures uniqueness based on the specified columns.



dml

update value of a column
UPDATE employees
SET salary = 60000
WHERE employee_id = 5;

add column
ALTER TABLE employees
ADD COLUMN hire_date DATE;

change column name
ALTER TABLE employees
CHANGE COLUMN hire_date joining_date DATE;

drop column
ALTER TABLE employees
DROP COLUMN joining_date;

remove rows based on condition
DELETE FROM employees
WHERE employee_id = 5;

rename table
RENAME TABLE employees TO staff;


drop table name

rename database
RENAME DATABASE old_db_name TO new_db_name;

DROP DATABASE new_database;
------------------------------------------------------------
procedures are function calls
delimeter//
create procedure display()
begin
select * from student;
end
delimeter;
call display();
----------------------------------------
DELIMITER $$

CREATE PROCEDURE get_employee_salary_from_emp_id(
    IN p_employee_id INT,              -- `IN` parameter: employee ID to search for
    OUT p_current_salary DECIMAL(10,2),-- `OUT` parameter: to return current salary
    INOUT p_new_salary DECIMAL(10,2)   -- `INOUT` parameter: salary to adjust
)
BEGIN
    -- Step 1: Retrieve the current salary of the employee and assign to OUT parameter
    SELECT salary INTO p_current_salary FROM employees WHERE employee_id = p_employee_id;
    
    -- Step 2: Modify the salary (e.g., increase by 10%) using the INOUT parameter
    SET p_new_salary = p_new_salary * 1.10;

    -- Step 3: Optionally, update the salary in the database (if required)
    UPDATE employees
    SET salary = p_new_salary
    WHERE employee_id = p_employee_id;
    
END $$

DELIMITER ;

-- Step 1: Declare session variables
SET @current_salary = 0;  -- This will store the current salary (OUT parameter)
SET @new_salary = 50000;  -- Initial salary to be passed as INOUT parameter (adjusted by the procedure)

-- Step 2: Call the stored procedure
CALL process_employee_salary(1, @current_salary, @new_salary);

-- Step 3: Retrieve the values
SELECT @current_salary AS current_salary, @new_salary AS updated_salary;

session variables begin with @
-- Step 1: Declare session variables
SET @current_salary = 0;  -- This will store the current salary (OUT parameter)
SET @new_salary = 50000;  -- Initial salary to be passed as INOUT parameter (adjusted by the procedure)

-- Step 2: Call the stored procedure
CALL process_employee_salary(1, @current_salary, @new_salary);

-- Step 3: Retrieve the values
SELECT @current_salary AS current_salary, @new_salary AS updated_salary;

-------------------------------------------
variables inside the stored procedure are used for calculation purpose inside the procedure.
begin
declare e_salary int default 0;
select salary into e_salary from employee where e_id-emp_id;
IF emp_salary < 50000 THEN
        set salary_level='low;
    ELSE
        set salary_level='high'
    END IF;
end //
delimeter;
set @salary_level  ='o';
call()
select @salary_level;

for out initialization as session variable is not required but no issues if done. for inout it is required.
-------------------------------
case condition
SELECT e_id, salary,
       CASE 
           WHEN salary < 30000 THEN 'Low'
           WHEN salary BETWEEN 30000 AND 70000 THEN 'Medium'
           ELSE 'High'
       END AS salary_level
FROM employee;

UPDATE employee
SET salary_level = CASE 
                       WHEN salary < 30000 THEN 'Low'
                       WHEN salary BETWEEN 30000 AND 70000 THEN 'Medium'
                       ELSE 'High'
                   END;
-----------------------------------------------
cursor is a sql object used to traverse sql query row by row. they work on select query. they store the result of select for further operations
declare ->open->fetch->close

DECLARE emp_cursor CURSOR FOR 
    SELECT name, salary 
    FROM employees;

declare error handler
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

OPEN emp_cursor;

WHILE done = 0 DO
        FETCH emp_cursor INTO emp_name, emp_salary;

        -- Process each row (for example, display or log it)
        IF done = 0 THEN
            SELECT CONCAT('Employee: ', emp_name, ', Salary: ', emp_salary) AS message;
        END IF;
    END WHILE;

CLOSE emp_cursor;
END $$

using repeat loop
REPEAT
        FETCH emp_cursor INTO emp_name, emp_salary;

        -- Process each row (for example, display or log it)
        IF done = 0 THEN
            SELECT CONCAT('Employee: ', emp_name, ', Salary: ', emp_salary) AS message;
        END IF;
    UNTIL done = 1
    END REPEAT;

loop
OPEN emp_cursor;

cursor_loop: LOOP
  FETCH emp_cursor INTO emp_name, emp_salary;
 IF done = 1 THEN
     LEAVE cursor_loop;
    END IF;
   SELECT CONCAT('Employee: ', emp_name, ', Salary: ', emp_salary) AS message;
    END LOOP;

CLOSE emp_cursor;
-----------------------------------------------------------------------------------------
errors in MySQL
used to handle and continue instead of breaking the program
CONTINUE Handler:
Used to ignore the error or continue execution after the error occurs.
The program moves to the next statement after the error.
DECLARE CONTINUE HANDLER FOR SQLEXCEPTION
BEGIN
        SELECT 'An error occurred, but execution continues.' AS message;
    END;
eg- if u enter into table that not exists this message displays