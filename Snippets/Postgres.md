## Start Postgres Service on Linux

```bash
sudo systemctl start postgresql

# WSL
sudo service postgresql start
```

## Log in using default 'postgres' superuser

- By default, Postgres is configured to use peer authentication, which allows users to log in if their operating system user name matches a Postgres internal name.

```bash
# Switch to the 'postgres' system user and runs psql
sudo -u postgres psql
```

## Data Migration

> [!note]- Migrate Postgres Database
> ![[Migrate Postgres Database]]
