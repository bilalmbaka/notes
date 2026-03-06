# Table of Contents

1. [PostgreSQL](#postgresql)
   * [Tables](#tables)
   * [Relations](#relations)

<br/>
<br/>
## PostgreSQL

### Tables
- To list all tables
```bash
\d
```
- To see the schema of a given table
```bash
\d <table_name>
```

### Relations

#### Many-to-Many
Many-to-many is a relation where A contains multiple instances of B, 
and B contains multiple instances of A.

@JoinTable() is required for @ManyToMany relations. You must put 
@JoinTable on one (owning) side of relation.

