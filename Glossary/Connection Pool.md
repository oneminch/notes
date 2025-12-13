- A method of maintaining a cache of database connections that can be reused when future requests to the database are required.
    - Instead of opening and closing a new connection for each database operation, connection pooling allows applications to reuse existing connections.
- Used to manage and optimize database connections in applications.
- **Benefits**:
    - _Improved Performance_ - Reduces the overhead of repeatedly opening and closing connections.
    - _Resource Efficiency_ - Minimizes the number of active connections, conserving server resources.
    - _Better Scalability_ - Allows applications to handle more concurrent users with fewer resources.
- Many ORMs and database drivers provide built-in connection pooling.

## How It Works

- When an application starts, the connection pool creates a set of database connections and keeps them ready for use.
- When a client needs to perform a database operation, it requests a connection from the pool.
- After the operation is complete, instead of closing the connection, it is returned to the pool for future use.
- The pool manages the lifecycle of connections, including creating new ones when needed and closing idle connections.

```js
const { Pool } = require('pg');

const pool = new Pool({
    user: 'my_username',
    host: 'localhost',
    database: 'my_database',
    password: 'my_password',
    port: 5432,
    max: 10,
    idleTimeoutMillis: 30000,
    connectionTimeoutMillis: 2000
});

app.get('/employees', (req, res) => {
    const { id } = req.query;
    const sqlQuery = 'SELECT name FROM employees WHERE id = $1';

    try {
        const res = await pool.query(query, [id]);
        if (res.rows.length > 0) {
            return res.rows[0].name;
        } else {
            return null;
        }
    } catch (err) {
        console.error('Error executing query', err.stack);
        throw err;
    }
});
```

---

## Further

### Reads 📄

- [Connection Pooling - Prisma's Data Guide](https://www.prisma.io/dataguide/database-tools/connection-pooling)
