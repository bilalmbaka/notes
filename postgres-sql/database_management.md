### Creating a database

```sql
> CREATE DATABASE <db_name>
```

### View all database

```sql
> SELECT datname FROM pg_database;
> \l
```

### Create db for someone else and make them owner

```sql
> CREATE DATABASE dbname OWNER rolename;
```

```sql
CREATE DATABASE trash_app_admin
    WITH
    OWNER = bilal
    ENCODING = 'UTF8'
    LC_COLLATE = 'en_US.UTF-8'
    LC_CTYPE = 'en_US.UTF-8'
    LOCALE_PROVIDER = 'libc'
    TABLESPACE = pg_default
    CONNECTION LIMIT = -1
    IS_TEMPLATE = False;

GRANT TEMPORARY, CONNECT ON DATABASE trash_app_admin TO PUBLIC;

GRANT ALL ON DATABASE trash_app_admin TO bilal;
```

### Creating a db from a template

```sql
> CREATE DATABASE dbname TEMPLATE template0;
> createdb -T template0 dbname
```

### Destroying a database

```sql
> DROP DATABASE name;
> dropdb dbname
```
