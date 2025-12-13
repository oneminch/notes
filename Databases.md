---
alias: DB
---

## Introduction

- Databases
    - are structured digital information stores
    - build indices to efficiently query data
    - define relationships between datasets
- Every database has a way of structuring / organizing and interacting with this stored information: Database Management Systems (DBMS)
    - Fundamental ways of interacting with databases:
        - **C**reate
        - **R**ead
        - **U**pdate
        - **D**elete

## Relational Databases

- Organize data in tabular form with columns (categories / attributes) and rows (entries)
- Uses a structure that allows us to identify and access data in relation to another piece of data in the database.
- Often, data is organized into tables.
- Highly structured
- Strict data types
- Great for complex datasets
- [[SQL]] is used to interact with them
- A [[Database Schema|schema]] needs to be defined before putting anything into a database
- Commonly used DBMSs: MySQL & PostgreSQL.

### Key Aspects

- **Row** / **Record**
    - A single entry in a table.
- **Column** / **Field**
    - The smallest entity of a table.
- **Constraint**
    - A restriction on the type or value that can be assigned to a `column`.
    - Enforce data integrity and maintain consistency within a relational database.
    - **Primary Key**
        - A field which uniquely identifies each row/record in a database table.
    - **Foreign Key**
        - aka _referencing key_.
        - Used to link two tables together.
        - A column or combination of columns whose values match a primary key in a different table.

### Limitations

- **Complexity**
    - Implementing and maintaining an RDBMS can be challenging, particularly for larger systems, as it demands specialized expertise to effectively manage, fine-tune, and enhance database performance.
- **Cost**
    - RDBMSs can be expensive, both in terms of the computational and storage resources they require and associated licensing fees.
- **Fixed Schema**
    - RDBMSs operate on a rigid schema structure, which can make changes to the data model time-intensive and complicated.
- **Handling of Unstructured Data**
    - RDBMSs are not suitable for handling unstructured data like multimedia files, social media posts, and sensor data, as their relational structure is optimized for structured data.
- **Horizontal Scalability**
    - Unlike NoSQL databases, RDBMSs are not as easily horizontally scalable.
    - Adding more machines to the system can be both costly and complex.

### Database Design

#### Relationships / Multiplicity

##### One-to-One (1:1)

- Each record in one table is related to exactly one record in another table.
- Foreign key can be placed in either table.
- Foreign key column typically has a `UNIQUE` constraint.
- Often implemented by splitting what could be one table into two.
- e.g. Each user has one shipping address.

```sql
-- Creating
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);

CREATE TABLE shipping_addresses (
    address_id INT PRIMARY KEY,
    user_id INT UNIQUE,  -- Enforces 1:1
    street VARCHAR(100) NOT NULL,
    city VARCHAR(50) NOT NULL,
    country VARCHAR(50) NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Querying
SELECT u.username, s.street, s.city, s.country
FROM users u
JOIN shipping_addresses s ON u.user_id = s.user_id
WHERE u.user_id = 1;
```

##### One-to-Many (1:N)

- One record in a table can be associated with multiple records in another table.
- Foreign key always goes in the "many" side table.
- No `UNIQUE` constraint on foreign key (allows duplicates).
- The most common type of relationship.
- e.g. One user can place many orders.

```sql
-- Creating
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Querying
SELECT o.order_id, o.order_date, o.total_amount
FROM orders o
JOIN users u ON o.user_id = u.user_id
WHERE u.user_id = 1
ORDER BY o.order_date DESC;
```

##### Many-to-Many (M:N)

- Multiple records in one table are related to multiple records in another table.
- Requires a junction table that has a foreign key to both tables.
- e.g. Many products can belong to many categories, and vice versa.

```sql
-- Creating
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL
);

CREATE TABLE categories (
    category_id INT PRIMARY KEY,
    category_name VARCHAR(50) NOT NULL
);

CREATE TABLE product_categories (
    product_id INT,
    category_id INT,
    PRIMARY KEY (product_id, category_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id),
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);

-- Querying
-- All Products in a specific category
SELECT p.product_name, p.price
FROM products p
JOIN product_categories pc ON p.product_id = pc.product_id
JOIN categories c ON pc.category_id = c.category_id
WHERE c.category_id = 1
ORDER BY p.price ASC;
```

##### Self-Referencing Relationship

- Occurs when a table has a foreign key that references its own primary key.
- e.g. Employees with managers (who are also employees).

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
);

-- Querying
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;
```

#### Entity-Relationship (ER) Modeling

> [!quote]- Cardinality (Simple Example)
> ![Cardinality](databases.erd-cardinality.png)
>
> ![Example](assets/images/databases.erd-example.png)
>
> **Source**: Lucid Software

> [!quote]- ERD (Example)
> ![Crow's Foot ERD](assets/images/databases.crows-foot-erd.png)
> **Source**: ConceptDraw

#### Normalization

![[Normalization]]

##### First Normal Form (1NF)

- Each table cell should contain a single value (and not a list of values).
- Each record needs to be unique.
- Conclusive of a relational database.
- For a database to be considered relational, all relations in the database are in 1NF.
    - A database is considered relational if all the fields in the tables are atomic, every column is a unique attribute, and a unique identifier or primary key is used.

##### Second Normal Form (2NF)

- Deals with the elimination of circular dependencies from a relation.
- A relation is in 2NF if it is in 1NF and if every non-key attribute is completely dependent only on the Primary Key.
- e.g. A single table with `OrderID`, `ProductID`, `ProductName`, `CustomerID` and `CustomerName` fields violates 2NF because `CustomerName` depends on `CustomerID`, not the full primary key (`OrderID`, `ProductID`).
    - The table can be split into two:
        - Orders (`OrderID`, `ProductID` and `CustomerID`)
        - Customers (`CustomerID` and `CustomerName`)

> [!note]
> A non-key attribute is any column that cannot be used to uniquely identify the table.

##### Third Normal Form (3NF)

- Deals with the elimination of non-key attributes that do not describe the Primary Key.
- For a relation to be in 3NF, the relationship between any two non-key attributes, or groups of non-key attributes, must not be in a one-to-one relation.
- Attributes should be mutually independent which means, none of the attributes should be functionally dependent on any combination of attributes.
    - This ensures that any update on the individual attribute will not affect other attributes in a row.
- e.g. A table that contains `EmployeeID`, `DepartmentID`, `DepartmentName` and `ManagerID` violates 3NF because `DepartmentName` is transitively dependent on `EmployeeID` through `DepartmentID`.
    - The table can be split into two:
        - Employees (`EmployeeID` and `DepartmentID`)
        - Departments (`DepartmentID`, `DepartmentName` and `ManagerID`)

##### Boyce-Codd Normal Form (BCNF)

- An extension to the third normal form, and is also known as 3.5 Normal Form.
- A table should follow these two conditions to satisfy BCNF:
    - It should be in 3NF.
    - And, for any dependency A → B, A should be a super key. which means that A should be a non-key attribute if B is a key attribute.

#### Denormalization

![[Denormalization]]

#### Data Integrity

- **Entity Integrity**
    - Ensures that each row in a table is uniquely identifiable.
    - Typically achieved through the use of primary keys.
    - e.g., In an employee database, each employee record might have a unique "Employee ID" as the primary key.
        - This prevents duplicate entries and ensures each employee can be distinctly identified.

- **Referential Integrity**
    - Used to build and maintain logical relationships between tables to avoid logical corruption of data.
    - Made up of the combination of a primary key and a foreign key.
    - ==Requires that if a foreign key in one table references a primary key in another table, the referenced value must exist.==
        - e.g., if an "Orders" table has a customer ID foreign key, that ID must exist in the "Customers" table.
    - Helps prevent orphaned records (records with foreign key values that don't match any primary key).
    - In such relationships, typically, the table with a Primary key is considered as a parent table and the table with a foreign key is considered a child table.
    - Updating and Deletion in primary key table and Insertion and Updating in the foreign key table could violate the referential integrity.
    - Actions on violation:
        - Prevents deletion of referenced data.
        - Deletes or updates related records automatically.
        - Sets the foreign key to null or a default value if the referenced record is deleted.
    - To avoid violating Referential Integrity, `ON DELETE CASCADE`, set the foreign key to NULL, or set foreign key to default are implemented.

- **Domain Integrity**
    - Enforces valid entries for a given column by specifying acceptable values or data types.
    - e.g., A "Date of Birth" column might be restricted to accept only valid dates within a reasonable range, preventing entries like "February 30" or future dates.

- **Physical Integrity**
    - Safeguards data against physical issues during storage and retrieval.
    - Ensures that data remains intact despite hardware failures or environmental factors.
    - e.g., Implementing redundant storage systems like RAID

### Performance

#### Indexing

- An index is a database object that provides a fast and efficient way to look up and retrieve data from a table.
    - Typically a data structure that stores a sorted or hashed subset of a table's data.
    - By default, tables are scanned for data row by row.
    - Indexes improve the performance of `SELECT` queries by reducing the amount of data that needs to be scanned.
    - They can also enhance the performance of join operations.
    - DML operations can be slower because indexes need to be updated along with the data.
        - Indexes should be limited on frequently updated tables.
    - Indexes also consume additional space.
- A covering index includes all columns needed for a query so that SQL can retrieve data directly from the index without accessing the underlying table.
    - e.g. a composite index with the required columns in the query.

```postgresql
-- Non-Clustered Index on 'Users'
CREATE INDEX idx_UserID ON Users(user_id);
-- Useful if users are frequently queried by their id.

-- Composite Index on 'Users'
CREATE INDEX idx_City_Balance ON Users(city, balance);

-- Clustered Index on 'Accounts'
CREATE INDEX idx_AccountID ON Accounts(account_id);

CLUSTER Accounts USING idx_AccountID;
```

> [!important]
> The order of operations is crucial.

#### Materialized Views

![[SQL#Materialized Views|Materialized Views]]

### Transactions

- Ensure that a series of database operations are treated as a single unit of work, maintaining data integrity and [[Database Consistency|consistency]], even in the face of system failures or concurrent access.
- Follow the [[ACID]] properties.

```sqlite
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

COMMIT;
-- or ROLLBACK; if there's an error
```

```sqlite
-- With Error Handling
BEGIN TRANSACTION;

BEGIN TRY
    UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
    COMMIT;
END TRY
BEGIN CATCH
    ROLLBACK;
    -- Log error or handle it appropriately
END CATCH
```

```js
// Using Node.js
const { Pool } = require("pg");
const pool = new Pool();

const client = await pool.connect();

try {
	await client.query("BEGIN");
	const queryText = "INSERT INTO users(name) VALUES($1) RETURNING id";
	const res = await client.query(queryText, ["brianc"]);
	await client.query("COMMIT");
} catch (e) {
	await client.query("ROLLBACK");
	throw e;
} finally {
	client.release();
}
```

- Savepoints can be used to create intermediate points within a transaction.

```sqlite
BEGIN TRANSACTION;

INSERT INTO orders (customer_id, product_id) VALUES (1, 101);
SAVE TRANSACTION SavePoint1;

INSERT INTO order_items (order_id, item_id) VALUES (SCOPE_IDENTITY(), 1);

IF ERROR
    ROLLBACK TRANSACTION SavePoint1;
ELSE
    COMMIT;
```

- Deadlocks can occur when multiple transactions are waiting for each other to release resources. To minimize the risk of deadlocks:
    - Keep transactions short and fast
    - Access objects in a consistent order
    - Use appropriate isolation levels

- **Isolation Levels**
    - Define how transactions interact with each other, affecting data consistency and performance.
    - Each level offers different balances between concurrency and data integrity.
    - **`READ UNCOMMITTED`**
        - Lowest isolation level
        - Allows dirty reads (reading uncommitted changes from other transactions)
        - No shared locks issued; no exclusive locks honored
        - Highest concurrency, but lowest data consistency
        - Rarely used due to potential data integrity issues
    - **`READ COMMITTED`**
        - Default isolation level in most databases
        - Prevents dirty reads
        - Shared locks are held only while data is being read
        - Allows non-repeatable reads and phantom reads
        - Balances concurrency and consistency
    - **`REPEATABLE READ`**
        - Prevents dirty reads and non-repeatable reads
        - Shared locks are held for the duration of the transaction
        - Still allows phantom reads
        - Lower concurrency than READ COMMITTED, but higher data consistency
    - **`SERIALIZABLE`**
        - Highest isolation level
        - Prevents dirty reads, non-repeatable reads, and phantom reads
        - Places a range lock on the data, preventing other transactions from modifying or inserting data that would affect the current transaction
        - Lowest concurrency, but highest data consistency

```postgresql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
BEGIN TRANSACTION;
-- operations here
COMMIT;
```

> [!note]- Related Terminology
>
> - _Dirty Read_ - Reading data that has been modified by another transaction but not yet committed.
> - _Non-repeatable Read_ - Getting different results when reading the same data multiple times within a transaction due to updates by other transactions.
> - _Phantom Read_ - When a transaction re-executes a query returning a set of rows that satisfy a search condition and finds that the set of rows has changed due to another recently committed transaction.

### OLTP vs. OLAP

- **Online Transaction Processing (OLTP)**
    - Designed to handle real-time transactional data processing.
    - Excel at managing and executing a large number of small, fast transactions in real-time.
    - Optimized for a high volume of quick, efficient, and frequent queries (`INSERT`, `UPDATE`, `DELETE` operations).
    - Response time is measured in milliseconds.
    - e.g. A retailer's point-of-sale system which handles real-time transactions as customers make purchases. A single purchase can include:
        - updating the inventory to reflect changes in stock
        - updating the customer's loyalty points
        - incrementing the daily sales total
- **Online Analytical Processing (OLAP)**
    - Designed for complex data analysis, business intelligence and reporting.
    - Focus on processing large volumes of historical and aggregated data to support decision-making and identify trends.
    - Handles complex queries on large datasets.
    - Response time can range from seconds to hours.
    - e.g. A retailer can use an OLAP system to analyze sales data and make strategic decisions:
        - comparing performance across different store locations
        - identifying top-selling products by category and season
        - evaluating the effectiveness of marketing campaigns

## Non-relational (NoSQL) Databases

- Generally more flexible and less strict
- Creating a schema isn't necessary
- Ideal for
    - decentralized / distributed networks of data.
    - dealing with large amounts of unstructured data.
- Commonly used databases: MongoDB, Redis.
- **Types**:
    - Graph
    - Key-Value
    - Document

## Scaling

### Sharding

- Enhances the scalability and performance of databases by distributing data across multiple servers or nodes.
- Involves partitioning a large database into smaller, more manageable pieces called "shards," which can be stored on different machines.
    - Each shard contains a subset of the data, allowing for parallel processing and improved response times.
- **Horizontal Sharding**
    - Divides a database table into smaller tables (shards) where each shard contains unique rows but shares the same schema.
        - It could be implemented alphabetically, geographically, etc.
    - e.g. customer records could be split based on geographic regions.
- **Vertical Sharding**
    - A table's columns are divided into different tables.
    - It is useful when certain queries only require specific columns, allowing for more efficient data retrieval.

## Security

### Row Level Security (RLS)

- Allows to define policies that restrict which rows a user can access in a table.
- Useful for multi-tenant databases or applications where users should only see their own data.

```postgresql
ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;
```

- Policies define the conditions under which rows can be accessed.
    - Logical operators can be used to create multiple policies on a table.

```postgresql
-- Allow users to see only their own data
CREATE POLICY user_data_policy ON users
    USING (user_id = current_user);

-- Allow managers to see data for their department
CREATE POLICY dept_data_policy ON employees
    USING (department_id IN (
        SELECT department_id
        FROM managers
        WHERE manager_id = current_user
    ));
```

### Access Control

- By default, only the creator of the database or a superuser has access to its objects.
- Roles can be used to group privileges and can be assigned to users.

```postgresql
-- Creating roles
CREATE ROLE readonly;
CREATE ROLE readwrite;

-- Granting privileges to roles
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO readwrite;

-- Assigning roles to users
GRANT readonly TO user1;
GRANT readwrite TO user2;
```

#### Role-Based Access Control (RBAC)

- Users are assigned roles, and permissions are granted to roles rather than individual users.

```postgresql
CREATE ROLE data_analyst;
GRANT SELECT ON sales_data TO data_analyst;
GRANT data_analyst TO jane_doe;
```

#### Discretionary Access Control (DAC)

- The owner of an object grants permissions to other users or roles.

```postgresql
GRANT UPDATE ON customers TO john_smith;
```

#### Column-Level Security

- Restrict access to specific columns.

```postgresql
GRANT SELECT (public_col1, public_col2) ON sensitive_table TO public_role;
GRANT SELECT ON sensitive_table TO admin_role;
```

#### Function Security

```postgresql
-- Control which users can execute specific functions
REVOKE ALL ON FUNCTION sensitive_function() FROM PUBLIC;
GRANT EXECUTE ON FUNCTION sensitive_function() TO admin_role;
```

#### Schema Security

```postgresql
-- Control access at the schema level
REVOKE ALL ON SCHEMA private FROM PUBLIC;
GRANT USAGE ON SCHEMA private TO authorized_role;
```

### Data Masking

![[Data Masking]]

### Encryption

- **Column-Level Encryption** encrypts specific columns within a database table, ideal for sensitive fields like credit card numbers.
    - PostgreSQL's `pgcrypto` extension provides cryptographic functions that can be used for column-level encryption.

```postgresql
-- Simple Example
CREATE EXTENSION pgcrypto;

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50),
    password_encrypted BYTEA
);

INSERT INTO users (username, password_encrypted)
VALUES ('john_doe', pgp_sym_encrypt('secret_password', 'encryption_key'));

SELECT username, pgp_sym_decrypt(password_encrypted::bytea, 'encryption_key') AS decrypted_password
FROM users;
```

- **Transparent Data Encryption (TDE)** encrypts the entire database, including backups and transaction logs, without requiring changes to applications

## Best Practices

- Use [[Connection Pool|connection pools]] to reduce connection overhead.
    - Configure to optimize resource utilization
- Normalize databases to minimize redundancy and maintain data integrity.
- Choose the right data types for fields for both performance and data integrity.
- Utilize lazy loading, eager loading and batch processing for efficient data retrieval.
- Consider denormalization for read-heavy workloads.
- Avoid unnecessary joins.
- Set up database replication for redundancy and improved read performance.
- Utilize sharding.
- Use indexes to improve query performance, but judiciously as they add overhead during insertions and updates.
- Specify only the columns you need with `SELECT`.
    - Avoid `SELECT *`.
- Use `LIMIT` to control the number of rows returned by a query for large datasets.
- Analyze query execution plans using `EXPLAIN` to identify potential bottlenecks.
- Avoid using functions in `WHERE` clauses.

### Security

- Use the principle of least privilege: Grant only the necessary permissions.
- Regularly audit and review access controls and RLS policies.
- Use roles to group privileges for easier management.
- Implement application-level security in addition to database-level security.
- Use SSL/TLS for encrypted connections to the database.

---

## Further

### Books 📚

- Seven Databases in Seven Weeks (Eric Redmond)

### Resources 🧩

- [Prisma's Data Guide](https://www.prisma.io/dataguide) ⭐

### Videos 🎥

![How do Databases Work? (YouTube)](https://www.youtube.com/watch?v=FnsIJAaGRk4)

![7 Database Paradigms - Fireship (YouTube)](https://youtube.com/watch?v=W2Z7fbCLSTw)

![How Does Role-Level Security Protect Your Data in SQL (YouTube)](https://www.youtube.com/watch?v=mGgGmdWbvtQ)

![What is Data Modelling?](https://www.youtube.com/watch?v=CUR6rKrIEGc)
