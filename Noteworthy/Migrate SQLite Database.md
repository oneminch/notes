## Export Data

### Schema Only

- From the command line:

```bash
sqlite3 DB_FILE.sqlite '.schema [table_name]' > backup_schema.sql
```

- From the `sqlite3` shell:

```bash
sqlite3 ./my_db.db
```

```bash
sqlite> .output ./backup_schema.sql
sqlite> .schema
sqlite> .exit
```

### All Database Contents

```bash
sqlite3 ./my_db.db
```

```bash
sqlite> .output ./backup_db.sql
sqlite> .dump [table_name]
sqlite> .exit
```

## Import Data

```bash
sqlite3 ./restore.db
```

```bash
sqlite> .read ./backup_db.sql
sqlite> .exit
```
