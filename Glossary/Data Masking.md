- In [[SQL]], data masking is a technique used to protect sensitive information by obscuring it.

- **Static Data Masking** modifies the data at rest, creating a separate copy of the database with masked data.

```sql
-- Example
CREATE {VIEW|TABLE} masked_employees AS
SELECT
    id,
    CONCAT(SUBSTRING(first_name, 1, 1), 'xxxxx') AS first_name,
    CONCAT(SUBSTRING(last_name, 1, 1), 'xxxxx') AS last_name,
    CONCAT('xxx-xxx-', RIGHT(phone, 4)) AS phone,
    CASE
        WHEN salary > 100000 THEN 'High'
        WHEN salary > 50000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_range
FROM employees;
```

- **Dynamic Data Masking** obscures data on-the-fly as it's being queried, without modifying the underlying data.

```sql
-- Partial masking using built-in string functions
SELECT
    id,
    CONCAT(LEFT(first_name, 1), REPEAT('x', LENGTH(first_name) - 1)) AS masked_first_name,
    CONCAT(LEFT(last_name, 1), REPEAT('x', LENGTH(last_name) - 1)) AS masked_last_name
FROM employees;

-- Salary range masking
SELECT
    id,
    CASE
        WHEN salary > 100000 THEN 'High'
        WHEN salary > 50000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_range
FROM employees;

-- Phone number masking
SELECT
    id,
    CONCAT('xxx-xxx-', RIGHT(phone, 4)) AS masked_phone
FROM employees;
```
