
# SQL Concepts and Examples

## 1. Basic SQL Syntax

**Problem:** Retrieve all columns from the "employees" table.

Starting Table Schema:
```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    age INT,
    salary DECIMAL(10, 2)
);

SQL Query:
SELECT * FROM employees;
```
## 2. Data Definition Language (DDL)
**Problem:**: Add a new column "email" of type VARCHAR(100) to the "employees" table.

SQL Query:
```sql
ALTER TABLE employees
ADD email VARCHAR(100);
```
## 3. Data Manipulation Language (DML)
**Problem:**: Insert a new employee with the following details:

employee_id: 101
first_name: 'John'
last_name: 'Doe'
age: 30
salary: 50000.00
SQL Query:

```sql
INSERT INTO employees (employee_id, first_name, last_name, age, salary)
VALUES (101, 'John', 'Doe', 30, 50000.00);
```

## 4. Aggregate Functions
**Problem:**: Calculate the total salary of all employees.

SQL Query:

```sql
SELECT SUM(salary) AS total_salary FROM employees;
```
## 5. Data Constraints
**Problem:**: Create a new table "departments" with the following columns:

department_id: INT (Primary Key)
department_name: VARCHAR(50)
manager_id: INT (Foreign Key referencing employee_id in employees table)
Starting Table Schema:

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
);
```
## 6. Joins
**Problem:**: Retrieve a list of employees along with their department names.

SQL Query:

```sql
SELECT e.employee_id, e.first_name, e.last_name, d.department_name
FROM employees e
JOIN departments d ON e.employee_id = d.manager_id;
```
## 7. Subqueries
**Problem:**: Retrieve the names of employees who earn more than the average salary.

SQL Query:

```sql
SELECT first_name, last_name
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```
## 8. Advanced SQL Functions
**Problem:**: Retrieve the full name (concatenated first name and last name) of employees in uppercase.

SQL Query:

```sql
SELECT CONCAT(UPPER(first_name), ' ', UPPER(last_name)) AS full_name
FROM employees;
```
## 9. Views
**Problem:**: Create a view "employee_details" that displays the employee_id, full name, and salary.

SQL Query:

```sql
CREATE VIEW employee_details AS
SELECT employee_id, CONCAT(first_name, ' ', last_name) AS full_name, salary
FROM employees;
```
## 10. Indexes
**Problem:**: Create an index on the "employee_id" column of the "employees" table.

SQL Query:

```sql
CREATE INDEX idx_employee_id ON employees(employee_id);
```

## 11. Transactions

****Problem:**:** Implement a transaction that transfers a specified amount from one employee's salary to another.

SQL Query (Pseudo-code, not all databases support explicit transactions in queries):
```sql
BEGIN;
UPDATE employees SET salary = salary - :amount WHERE employee_id = :employee1_id;
UPDATE employees SET salary = salary + :amount WHERE employee_id = :employee2_id;
COMMIT;
```
## 12. Data Integrity and Security
****Problem:**:**: Create a foreign key constraint to ensure that every department has a valid manager.

SQL Query:

```sql
ALTER TABLE departments
ADD CONSTRAINT fk_manager
FOREIGN KEY (manager_id) REFERENCES employees(employee_id);
```
## 13. Stored Procedures and Functions
****Problem:**:**: Create a stored procedure that calculates the average salary of employees in a specific department.

SQL Query:

```sql
DELIMITER //
CREATE PROCEDURE GetAverageSalary(IN dept_id INT)
BEGIN
    SELECT AVG(salary) AS avg_salary
    FROM employees
    WHERE department_id = dept_id;
END //
DELIMITER ;
```
## 14. Performance Optimization
****Problem:**:**: Optimize a query to retrieve the highest-paid employee's details.

SQL Query (Optimized with ORDER BY and LIMIT):

```sql
SELECT *
FROM employees
ORDER BY salary DESC
LIMIT 1;
```
## 15. Advanced SQL Concepts
****Problem:**:**: Retrieve a list of employees along with the number of years they have worked in the company.

SQL Query (Using DATEDIFF to calculate years):

```sql
SELECT employee_id, first_name, last_name,
       DATEDIFF(CURDATE(), hire_date) / 365 AS years_worked
FROM employees;
```

### Recursive Queries

**Problem:** Retrieve a hierarchical structure of employees and managers using a recursive query.

SQL Query:
```sql
WITH RECURSIVE EmployeeHierarchy AS (
    SELECT employee_id, first_name, last_name, manager_id
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    SELECT e.employee_id, e.first_name, e.last_name, e.manager_id
    FROM employees e
    JOIN EmployeeHierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM EmployeeHierarchy;
```
### Pivot and Unpivot Operations
**Problem:**: Pivot the "sales" data to get total sales for each product in different years.

SQL Query:

```sql

SELECT *
FROM sales
PIVOT (
    SUM(sales_amount)
    FOR year IN ([2021], [2022], [2023])
) AS p;
```
### Window Functions
**Problem:**: Rank employees based on their salary and display the rank and salary.

SQL Query:

```sql

SELECT first_name, last_name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```
**Problem:**: Calculate the running total of sales for each year using window functions.

SQL Query:

```sql

SELECT year, sales_amount,
       SUM(sales_amount) OVER (PARTITION BY year ORDER BY month) AS running_total
FROM sales;
```
### Common Table Expressions (CTEs)
**Problem:**: Retrieve the employees who earn more than the average salary.

SQL Query:

```sql

WITH AvgSalary AS (
    SELECT AVG(salary) AS avg_salary
    FROM employees
)
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT avg_salary FROM AvgSalary);
```
### Dynamic SQL
**Problem:**: Create a dynamic SQL query to retrieve employees based on given filters.

SQL Query (Pseudo-code):

```sql

DECLARE @sql NVARCHAR(MAX);

SET @sql = 'SELECT * FROM employees WHERE 1=1';

IF @department_id IS NOT NULL
    SET @sql = @sql + ' AND department_id = ' + CAST(@department_id AS NVARCHAR);

IF @age_min IS NOT NULL
    SET @sql = @sql + ' AND age >= ' + CAST(@age_min AS NVARCHAR);

EXEC sp_executesql @sql;
```
