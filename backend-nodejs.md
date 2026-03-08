# Table of Contents

* [Package management](#package)
* [PostgreSQL](#postgresql)
  




<br/>
<br/>
<br/>
<br/>
## Package

Importing a local package / project in another project. <br/>
In project a create an index.ts that will export all the required files for the importing
package so see them.

* add this to package.json
```json
{
  "name": "auth-lib",
  "version": "1.0.0",
  "main": "dist/index.js", //The entry point JavaScript file of the package, When someone imports auth-lib, Node loads this file.
  "types": "dist/index.d.ts" //The TypeScript type definitions entry point, allows TypeScript projects to get types and autocompletion.
}
```

* add the project to the importing project.
```bash
npm install ../path-to-auth-project
```

which produces.
```json
"auth-lib": "file:../yirapay-shared-resources"
```





<br/>
<br/>
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

