# Trigger and functions.

### Create the function<br/>

```sql
CREATE OR REPLACE FUNCTION set_updated_at()
--This function will be used as a trigger, Trigger functions must return trigger,
--Starts the function body
--Used so you don’t have to escape quotes inside the function
RETURNS trigger AS $$
BEGIN
  --NEW = the row being updated
  --NEW = the row being updated
  --If you returned OLD, the update would be ignored
  --If you returned NULL, the update would be cancelled
  NEW.updated_at = now();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### Attach the trigger to a table

```sql
CREATE TRIGGER trigger_set_updated_at
BEFORE UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION set_updated_at();
```

### View all user-created functions

```sql
SELECT
  n.nspname AS schema,
  p.proname AS function_name,
  pg_get_function_identity_arguments(p.oid) AS args,
  pg_get_function_result(p.oid) AS returns
FROM pg_proc p
JOIN pg_namespace n ON n.oid = p.pronamespace
WHERE n.nspname NOT IN ('pg_catalog', 'information_schema')
ORDER BY schema, function_name;

--psql shortcut
\df
\df public.*
```

### View all triggers

```sql
SELECT
  event_object_schema AS schema,
  event_object_table AS table,
  trigger_name,
  action_timing AS timing,
  event_manipulation AS event
FROM information_schema.triggers
ORDER BY schema, table;

--psql shortcut
\dS users
```

### View triggers and their functions together.

```sql
SELECT
  t.tgname AS trigger_name,
  c.relname AS table,
  p.proname AS function
FROM pg_trigger t
JOIN pg_class c ON c.oid = t.tgrelid
JOIN pg_proc p ON p.oid = t.tgfoid
WHERE NOT t.tgisinternal;
```

### View the source code of a function

```sql
SELECT pg_get_functiondef('set_updated_at()'::regprocedure);
```

### Delete functions and triggers

```sql
DROP TRIGGER trigger_set_updated_at ON users;
DROP FUNCTION set_updated_at();
```
