# PostgreSQL Data Types Reference

| Type                   | Storage / Size | Notes                                                          | Example                                              |
| ---------------------- | -------------- | -------------------------------------------------------------- | ---------------------------------------------------- |
| **Small Integer**      | 2 bytes        | Range: -32,768 to 32,767                                       | `smallint` / `int2`                                  |
| **Integer**            | 4 bytes        | Range: -2,147,483,648 to 2,147,483,647                         | `integer` / `int` / `int4`                           |
| **Big Integer**        | 8 bytes        | Range: -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | `bigint` / `int8`                                    |
| **Decimal / Numeric**  | variable       | Exact numeric with precision and scale                         | `numeric(10,2)`                                      |
| **Real**               | 4 bytes        | Approximate floating-point                                     | `real` / `float4`                                    |
| **Double Precision**   | 8 bytes        | Approximate floating-point                                     | `double precision` / `float8`                        |
| **Serial / Bigserial** | 4 / 8 bytes    | Auto-increment integer                                         | `serial`, `bigserial`                                |
| **Character (fixed)**  | n bytes        | Fixed-length string, padded                                    | `char(10)` / `character(10)`                         |
| **Character Varying**  | n bytes        | Variable-length string up to n                                 | `varchar(50)` / `character varying(50)`              |
| **Text**               | variable       | Unlimited-length string                                        | `text`                                               |
| **Boolean**            | 1 byte         | TRUE / FALSE / NULL                                            | `boolean` / `bool`                                   |
| **Date**               | 4 bytes        | Calendar date                                                  | `date`                                               |
| **Time**               | 8 bytes        | Time of day                                                    | `time [ (p) ] [ without time zone ]`                 |
| **Time with TZ**       | 12 bytes       | Time of day with timezone                                      | `timetz`                                             |
| **Timestamp**          | 8 bytes        | Date + time                                                    | `timestamp [ (p) ] [ without time zone ]`            |
| **Timestamp with TZ**  | 8 bytes        | Date + time with timezone                                      | `timestamptz`                                        |
| **Interval**           | 16 bytes       | Duration                                                       | `interval '1 day 2 hours'`                           |
| **Binary / Bytea**     | variable       | Raw bytes                                                      | `bytea`                                              |
| **UUID**               | 16 bytes       | Universally unique identifier                                  | `uuid`                                               |
| **CIDR / INET**        | 7–19 bytes     | IP network / host                                              | `inet '192.168.1.0/24'`                              |
| **MAC Address**        | 6 / 8 bytes    | MAC addresses                                                  | `macaddr`, `macaddr8`                                |
| **Array**              | variable       | List of values of a type                                       | `integer[]`, `text[]`                                |
| **JSON / JSONB**       | variable       | JSON data (text / binary)                                      | `json`, `jsonb`                                      |
| **Range Types**        | variable       | Inclusive / exclusive ranges                                   | `int4range`, `daterange`, `tsrange`                  |
| **Geometric Types**    | variable       | Geometric objects                                              | See below                                            |
| `point`                | 16 bytes       | Geometric point `(x, y)`                                       | `point '(1,2)'`                                      |
| `line`                 | 32 bytes       | Infinite line                                                  | `line '{1,2,3}'`                                     |
| `lseg`                 | 32 bytes       | Line segment                                                   | `lseg '[(1,2),(3,4)]'`                               |
| `box`                  | 32 bytes       | Rectangular box                                                | `box '((1,2),(3,4))'`                                |
| `path`                 | variable       | Geometric path (open/closed)                                   | `path '[(1,2),(3,4),(5,6)]'`                         |
| `polygon`              | variable       | Polygon                                                        | `polygon '((1,2),(3,4),(5,6))'`                      |
| `circle`               | 24 bytes       | Circle (center + radius)                                       | `circle '<(1,2),3>'`                                 |
| **XML**                | variable       | XML data                                                       | `xml '<tag>value</tag>'`                             |
| **Full-Text Search**   | variable       | Text search support                                            | `tsvector`, `tsquery`                                |
| **Bit / Varbit**       | n / variable   | Fixed or variable-length bit string                            | `bit(8)`, `varbit(16)`                               |
| **pg_lsn**             | 8 bytes        | PostgreSQL log sequence number                                 | `pg_lsn '0/16B6C50'`                                 |
| **Composite**          | variable       | User-defined multiple fields                                   | `CREATE TYPE address AS (street text, city text)`    |
| **Enum**               | variable       | Enumerated list of values                                      | `CREATE TYPE mood AS ENUM ('happy','sad')`           |
| **Domain**             | variable       | Custom type with constraints                                   | `CREATE DOMAIN positive_int AS int CHECK(VALUE > 0)` |

---

**Notes:**

- Sizes are approximate; some types depend on actual data stored.
- Arrays, JSON, ranges, geometric, and composite types are variable in size.
- For production, schema design often uses `text`, `numeric`, `uuid`, `jsonb`, and arrays extensively.
    