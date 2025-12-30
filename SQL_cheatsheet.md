# SQL Cheat Sheet (Pareto Edition)
## The Essential 20% That Covers 80% of Real-World Usage

---

## 🔷 1. BASIC QUERIES (Daily Bread)

```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT first_name, last_name, salary FROM employees;

-- Unique values only
SELECT DISTINCT department FROM employees;

-- Limit results
SELECT * FROM employees LIMIT 10;

-- Skip rows (pagination)
SELECT * FROM employees LIMIT 10 OFFSET 20;
```

---

## 🔷 2. FILTERING (WHERE Clause)

```sql
-- Basic comparison
SELECT * FROM employees WHERE salary > 50000;

-- Multiple conditions
SELECT * FROM employees WHERE department = 'IT' AND salary > 60000;
SELECT * FROM employees WHERE department = 'HR' OR department = 'Finance';

-- Negation
SELECT * FROM employees WHERE NOT department = 'IT';

-- Pattern matching (% = any chars, _ = single char)
SELECT * FROM employees WHERE first_name LIKE 'J%';
SELECT * FROM employees WHERE email LIKE '%@gmail.com';

-- List of values
SELECT * FROM employees WHERE department IN ('HR', 'Finance', 'IT');

-- Range
SELECT * FROM employees WHERE salary BETWEEN 50000 AND 80000;

-- NULL handling
SELECT * FROM employees WHERE manager_id IS NULL;
SELECT * FROM employees WHERE manager_id IS NOT NULL;
```

---

## 🔷 3. SORTING & ORDERING

```sql
-- Ascending (default)
SELECT * FROM employees ORDER BY salary;
SELECT * FROM employees ORDER BY salary ASC;

-- Descending
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple columns
SELECT * FROM employees ORDER BY department ASC, salary DESC;
```

---

## 🔷 4. AGGREGATE FUNCTIONS (The Big 5)

```sql
-- Count rows
SELECT COUNT(*) FROM employees;
SELECT COUNT(DISTINCT department) FROM employees;

-- Sum values
SELECT SUM(salary) FROM employees;

-- Average
SELECT AVG(salary) FROM employees;
SELECT ROUND(AVG(salary), 2) AS avg_salary FROM employees;

-- Min/Max
SELECT MIN(salary), MAX(salary) FROM employees;
```

---

## 🔷 5. GROUP BY & HAVING

```sql
-- Group and aggregate
SELECT department, COUNT(*) AS emp_count, AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

-- Filter groups (HAVING = WHERE for groups)
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 55000;

-- Complete pattern
SELECT department, COUNT(*) AS count
FROM employees
WHERE hire_date >= '2020-01-01'    -- Filter rows BEFORE grouping
GROUP BY department
HAVING COUNT(*) > 5                -- Filter groups AFTER grouping
ORDER BY count DESC;
```

---

## 🔷 6. JOINS (Combining Tables)

```sql
-- INNER JOIN: Only matching rows from both tables
SELECT e.first_name, e.last_name, d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;

-- LEFT JOIN: All from left + matching from right (NULLs if no match)
SELECT e.first_name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;

-- RIGHT JOIN: All from right + matching from left
SELECT e.first_name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;

-- Multiple joins
SELECT o.order_id, c.customer_name, p.product_name
FROM orders o
JOIN customers c ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id;

-- Self join (employee with their manager)
SELECT e.first_name AS employee, m.first_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

### Join Visual Reference:
```
INNER: Only intersection (∩)
LEFT:  All left + intersection
RIGHT: All right + intersection
FULL:  Everything from both
```

---

## 🔷 7. SUBQUERIES

```sql
-- In WHERE (single value)
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- In WHERE with IN (multiple values)
SELECT * FROM employees
WHERE department_id IN (SELECT id FROM departments WHERE location = 'NYC');

-- In FROM (derived table)
SELECT dept_avg.department, dept_avg.avg_salary
FROM (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) AS dept_avg
WHERE avg_salary > 60000;

-- Correlated subquery (references outer query)
SELECT e.first_name, e.salary, e.department
FROM employees e
WHERE salary > (
    SELECT AVG(salary) FROM employees WHERE department = e.department
);

-- EXISTS (check if subquery returns any rows)
SELECT * FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.id
);
```

---

## 🔷 8. CTEs (Common Table Expressions)

```sql
-- Basic CTE (cleaner than subqueries)
WITH high_earners AS (
    SELECT * FROM employees WHERE salary > 70000
)
SELECT department, COUNT(*) FROM high_earners GROUP BY department;

-- Multiple CTEs
WITH 
dept_stats AS (
    SELECT department, AVG(salary) AS avg_sal FROM employees GROUP BY department
),
high_paying_depts AS (
    SELECT department FROM dept_stats WHERE avg_sal > 60000
)
SELECT e.* FROM employees e
WHERE e.department IN (SELECT department FROM high_paying_depts);

-- Recursive CTE (hierarchies, sequences)
WITH RECURSIVE org_chart AS (
    -- Base case: top-level managers
    SELECT id, first_name, manager_id, 1 AS level
    FROM employees WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: employees who report to someone in org_chart
    SELECT e.id, e.first_name, e.manager_id, oc.level + 1
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.id
)
SELECT * FROM org_chart ORDER BY level, first_name;
```

---

## 🔷 9. WINDOW FUNCTIONS (Analytics Powerhouse)

```sql
-- ROW_NUMBER: Unique sequential number
SELECT first_name, department, salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS rank
FROM employees;

-- RANK: Same rank for ties, gaps after
-- DENSE_RANK: Same rank for ties, no gaps
SELECT first_name, salary,
       RANK() OVER (ORDER BY salary DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;

-- Partition by (rank within groups)
SELECT first_name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;

-- Running totals
SELECT order_date, amount,
       SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;

-- Moving average (last 3 rows)
SELECT order_date, amount,
       AVG(amount) OVER (ORDER BY order_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM orders;

-- LAG/LEAD (previous/next row values)
SELECT order_date, amount,
       LAG(amount, 1) OVER (ORDER BY order_date) AS prev_amount,
       LEAD(amount, 1) OVER (ORDER BY order_date) AS next_amount
FROM orders;
```

---

## 🔷 10. DATA MODIFICATION (CRUD)

```sql
-- INSERT single row
INSERT INTO employees (first_name, last_name, salary)
VALUES ('John', 'Doe', 55000);

-- INSERT multiple rows
INSERT INTO employees (first_name, last_name, salary)
VALUES 
    ('Jane', 'Smith', 60000),
    ('Bob', 'Johnson', 52000);

-- INSERT from SELECT
INSERT INTO archive_employees
SELECT * FROM employees WHERE status = 'inactive';

-- UPDATE
UPDATE employees SET salary = 58000 WHERE id = 1;
UPDATE employees SET salary = salary * 1.10 WHERE department = 'IT';

-- DELETE
DELETE FROM employees WHERE id = 1;
DELETE FROM employees WHERE status = 'inactive';

-- UPSERT (INSERT or UPDATE) - syntax varies by DB
-- PostgreSQL:
INSERT INTO employees (id, first_name, salary)
VALUES (1, 'John', 55000)
ON CONFLICT (id) DO UPDATE SET salary = EXCLUDED.salary;

-- MySQL:
INSERT INTO employees (id, first_name, salary)
VALUES (1, 'John', 55000)
ON DUPLICATE KEY UPDATE salary = VALUES(salary);
```

---

## 🔷 11. TABLE OPERATIONS (DDL)

```sql
-- CREATE TABLE
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,  -- MySQL
    -- id SERIAL PRIMARY KEY,           -- PostgreSQL
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    salary DECIMAL(10,2) DEFAULT 0,
    department_id INT,
    hire_date DATE DEFAULT CURRENT_DATE,
    FOREIGN KEY (department_id) REFERENCES departments(id)
);

-- ALTER TABLE
ALTER TABLE employees ADD COLUMN phone VARCHAR(20);
ALTER TABLE employees DROP COLUMN phone;
ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12,2);  -- MySQL
ALTER TABLE employees ALTER COLUMN salary TYPE DECIMAL(12,2);  -- PostgreSQL

-- DROP TABLE
DROP TABLE IF EXISTS employees;

-- CREATE INDEX (speed up queries)
CREATE INDEX idx_emp_dept ON employees(department_id);
CREATE INDEX idx_emp_name ON employees(last_name, first_name);
```

---

## 🔷 12. CASE EXPRESSIONS (Conditional Logic)

```sql
-- Simple CASE
SELECT first_name, salary,
    CASE 
        WHEN salary < 40000 THEN 'Low'
        WHEN salary < 70000 THEN 'Medium'
        ELSE 'High'
    END AS salary_tier
FROM employees;

-- CASE in aggregation
SELECT department,
    COUNT(CASE WHEN salary > 60000 THEN 1 END) AS high_earners,
    COUNT(CASE WHEN salary <= 60000 THEN 1 END) AS others
FROM employees
GROUP BY department;

-- COALESCE (first non-NULL value)
SELECT first_name, COALESCE(phone, email, 'No contact') AS contact
FROM employees;

-- NULLIF (return NULL if equal)
SELECT amount / NULLIF(quantity, 0) AS unit_price  -- Avoids divide by zero
FROM order_items;
```

---

## 🔷 13. STRING FUNCTIONS

```sql
-- Case conversion
SELECT UPPER(first_name), LOWER(last_name) FROM employees;

-- Concatenation
SELECT first_name || ' ' || last_name AS full_name FROM employees;  -- Standard/PostgreSQL
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;  -- MySQL

-- Substring
SELECT SUBSTR(phone, 1, 3) AS area_code FROM employees;  -- First 3 chars
SELECT SUBSTRING(email FROM '@(.+)$') AS domain FROM employees;  -- PostgreSQL regex

-- Length
SELECT first_name, LENGTH(first_name) AS name_length FROM employees;

-- Trim whitespace
SELECT TRIM('  hello  ') AS trimmed;
SELECT LTRIM(name), RTRIM(name) FROM employees;

-- Replace
SELECT REPLACE(phone, '-', '') AS phone_digits FROM employees;
```

---

## 🔷 14. DATE FUNCTIONS

```sql
-- Current date/time
SELECT CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP;
SELECT NOW();  -- PostgreSQL/MySQL

-- Extract parts
SELECT EXTRACT(YEAR FROM hire_date) AS year FROM employees;
SELECT EXTRACT(MONTH FROM hire_date) AS month FROM employees;

-- Date arithmetic
SELECT hire_date + INTERVAL '1 year' FROM employees;  -- PostgreSQL
SELECT DATE_ADD(hire_date, INTERVAL 1 YEAR) FROM employees;  -- MySQL

-- Date formatting
SELECT TO_CHAR(hire_date, 'YYYY-MM-DD') FROM employees;  -- PostgreSQL
SELECT DATE_FORMAT(hire_date, '%Y-%m-%d') FROM employees;  -- MySQL

-- Date difference
SELECT hire_date, CURRENT_DATE - hire_date AS days_employed FROM employees;  -- PostgreSQL
SELECT DATEDIFF(CURRENT_DATE, hire_date) AS days_employed FROM employees;  -- MySQL
```

---

## 🔷 15. USEFUL PATTERNS

### Pagination
```sql
SELECT * FROM products ORDER BY id LIMIT 20 OFFSET 40;  -- Page 3, 20 per page
```

### Deduplication
```sql
-- Keep first occurrence
DELETE FROM employees e1
USING employees e2
WHERE e1.id > e2.id AND e1.email = e2.email;

-- Or with CTE
WITH duplicates AS (
    SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM employees
)
DELETE FROM employees WHERE id IN (SELECT id FROM duplicates WHERE rn > 1);
```

### Top N per group
```sql
WITH ranked AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rn
    FROM employees
)
SELECT * FROM ranked WHERE rn <= 3;  -- Top 3 earners per department
```

### Running totals by category
```sql
SELECT category, order_date, amount,
       SUM(amount) OVER (PARTITION BY category ORDER BY order_date) AS category_running_total
FROM orders;
```

---

## 🔷 QUERY EXECUTION ORDER

Remember: SQL doesn't execute in the order you write it!

```
1. FROM / JOIN     -- Get the tables
2. WHERE           -- Filter rows
3. GROUP BY        -- Create groups
4. HAVING          -- Filter groups
5. SELECT          -- Choose columns
6. DISTINCT        -- Remove duplicates
7. ORDER BY        -- Sort results
8. LIMIT/OFFSET    -- Limit output
```

This is why you can't use column aliases in WHERE (it runs before SELECT)!

---

## 🔷 QUICK TIPS

| Tip | Why |
|-----|-----|
| Use `EXPLAIN` before slow queries | See execution plan |
| Index columns in WHERE/JOIN | Speed up lookups |
| Avoid `SELECT *` in production | Fetch only what you need |
| Use parameterized queries | Prevent SQL injection |
| `COALESCE` for NULL defaults | Cleaner than CASE |
| CTEs over nested subqueries | Much more readable |
| `EXISTS` over `IN` for large lists | Often faster |

---

*Sources: GeeksforGeeks, Dataquest, LearnSQL.com, SQLTutorial.org, DataCamp*
