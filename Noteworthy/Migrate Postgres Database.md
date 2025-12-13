## Export data using `pg_dump`:

```bash
pg_dump -Fc -v -d postgresql://[user]:[password]@[host]:[port]/[database] --schema=public -f data_dump.bak
```

- `-Fc` outputs the dump in custom format, which is compressed and suitable for input into `pg_restore`.
- `-v` enables verbose mode for monitoring the dump operation.
- `-d` is used to specify the connection string for the database.
- `-f` specifies the output file name.
- `--schema=public` specifies the schema to dump.

## Prepare destination database:

```postgresql
CREATE DATABASE db;
```

## Restore data to destination database using `pg_restore`:

- Get database connection string:

```
postgresql://[user]:[password]@[host]/[dbname]
```

```bash
pg_restore -d <db-connection-string> -v --no-owner --no-acl data_dump.bak
```

- `-d` specifies the connection string for the database.
- `-v` enables verbose mode.
- `--no-owner` skips setting the ownership of objects as in the original database.
- `--no-acl` skips restoring access privileges for objects as in the original database.
