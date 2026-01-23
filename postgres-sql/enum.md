# ENUMS

### Creating a enum

```sql
CREATE TYPE user_role AS ENUM ('admin', 'moderator', 'user');
```

### Viewing all enums

```sql
-- List all enum types
SELECT n.nspname AS schema_name,
       t.typname AS enum_name,
       e.enumlabel AS enum_value
FROM pg_type t
JOIN pg_enum e ON t.oid = e.enumtypid
JOIN pg_catalog.pg_namespace n ON n.oid = t.typnamespace
ORDER BY enum_name, e.enumsortorder;
```

### Delete an anum

```sql
DROP TYPE user_role
```

### Add a new enum value

```sql
ALTER TYPE user_role ADD VALUE 'guest';
```

### Add a new enum value at a desired location

```sql
ALTER TYPE user_role ADD VALUE 'superadmin' BEFORE 'admin';
```

### rename an enum

```sql
ALTER TYPE user_role_new RENAME TO user_role;
```

### Work around to remove value from an enum.

```sql
-- after creating a new enum.
ALTER TABLE users
ALTER COLUMN role TYPE user_role_new
USING role::text::user_role_new;
```
