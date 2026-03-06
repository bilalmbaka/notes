# Table of Contents

1. [PostgreSQL](#postgresql)
   * [Tables](#tables)
   * [Relations](#relations)

<br/>
<br/>
## PostgreSQL

### Tables
- To list all tables
```sql
\d
```
- To see the schema of a given table
```sql
\d <table_name>
```

- Adding a new column to a table.
```sql
ALTER TABLE users
	ADD COLUMN role role NOT NULL DEFAULT 'user';
```

- Inserting a new record into the table
```sql
INSERT INTO recyclables (name, icon, price, weight_classification) 
VALUES (
  'aluminium', 
  'https://res.cloudinary.com/dxv10krxo/image/upload/v1772807670/aluminium_k3msbg.png', 
  1000, 
  'kg'
);
```

### Relations

#### Many-to-Many
Many-to-many is a relation where A contains multiple instances of B, 
and B contains multiple instances of A.

@JoinTable() is required for @ManyToMany relations. You must put 
@JoinTable on one (owning) side of relation.

