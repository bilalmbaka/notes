### Connecting to fresh install

```
> sudo -i -u postgres
> psql

```

### Creating a new role

```
> CREATE ROLE bilal LOGIN PASSWORD '12345678';
```

### View all roles

```
> SELECT rolname FROM pg_roles;
> \du
```

### Giving user admin access to a db.

```
> psql -d databasename
> GRANT ALL PRIVILEGES ON DATABASE my_db TO user_a;
> GRANT ALL PRIVILEGES ON SCHEMA public TO user_a;
> GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO user_a;
> ALTER DEFAULT PRIVILEGES IN SCHEMA public
> GRANT ALL PRIVILEGES ON TABLES TO user_a;
> GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO user_a;
> GRANT ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA public TO user_a;

```

### CHANGING THE OWNER OF A DB.

```
> ALTER TABLE <table_name> OWNER TO <role>;
```
