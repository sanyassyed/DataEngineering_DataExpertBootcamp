# Data Engineering Notes

---

## Topic 1 – Kickoff

*(No content yet – placeholder for introduction)*

---

## Topic 2 – Why Pick Data Engineering

*(No content yet – can add reasons like high demand, scalable career, ability to work with big data pipelines, etc.)*

---

## Topic 3 – SQL Basics

### Lecture 1 – Introduction to SQL 101

**Why Learn SQL**

* SQL is the standard language for interacting with relational databases.
* Useful for querying, analyzing, and managing data in databases, data warehouses, and data lakes.

**When to Use SQL**

* When working with structured data already stored in databases.
* For reporting, data cleaning, aggregation, and analytics tasks.

**Core SQL Commands**

* `SELECT`, `WHERE`, `COUNT()`, `JOIN` (INNER, LEFT, RIGHT, FULL)
* `INSERT`, `UPDATE`, `DELETE`
* SQL is for data **already in the database**.

---

### SQL Command Execution Hierarchy

1. `FROM` / `JOIN`
2. `WHERE`
3. `GROUP BY` / `HAVING`
4. `SELECT`
5. `QUALIFY` (Only available in some platforms eg: snowflake
6. `ORDER BY`
7. `LIMIT`

---

### QUALIFY
* Executes after SELECT statement
* Columns with window funcions can be filtered here
  
```sql
SELECT player_name, 
       RANK() OVER (ORDER BY pts DESC) rnk
FROM bootcamp.nba_players
QUALIFY rnk = 1;
```

### SQL Rules & Best Practices

* Filter data as early as possible to minimize processing (`WHERE` before `SELECT`).
* Use subqueries only when they return a single scalar value.
* Avoid `SELECT *` in production queries.
* Use indexes for faster filtering and joining.
* Backup data before performing `DELETE` or `UPDATE`.

---

### EXPLAIN Keyword

* AST - Abstract Syntax Tree is where the query is compiled. And its the AST that is executed by the machine. This is what is shown in the explain command how the query is compiled in the AST
* Explains how the query will execute (query plan).
* Example:

```sql
EXPLAIN WITH cte AS (
    SELECT id,
           name,
           RANK() OVER (ORDER BY age ASC) AS age_rnk
    FROM Employee
)
SELECT *
FROM Employee e
JOIN cte c ON e.id = c.id
WHERE age_rnk = 1;
```

* Some databases support `EXPLAIN or ANALYZE` for runtime execution details.

---

## Lab 1 – GROUP BY, JOIN, and Common Table Expressions (CTEs)

### Partitioning Example

```sql
-- View table creation command
SHOW CREATE TABLE zachwilson.nba_players_ranked;

-- Create partitioned table
CREATE TABLE zachwilson.nba_players_ranked_partitioned (
    player_name VARCHAR,   -- e.g., 'LeBron James'
    pts DECIMAL,           -- e.g., 3.7 or 888.2
    rank BIGINT,           -- Max: 9,223,372,036,854,775,807
    season SMALLINT        -- Max: 32,767 (SMALLINT)
)
WITH (
    partitioning = ARRAY['season']
);

-- Insert values into partitioned table
INSERT INTO zachwilson.nba_players_ranked_partitioned
WITH players_ranked AS (
    SELECT player_name,
           pts,
           RANK() OVER (ORDER BY pts DESC) AS rank,
           season
    FROM bootcamp.nba_player_seasons
)
SELECT *
FROM players_ranked
WHERE season = 2007;

-- Test query
SELECT *
FROM zachwilson.nba_players_ranked_partitioned
WHERE season = 2007 AND rank = 1;

-- Delete all records
DELETE FROM zachwilson.nba_players_ranked_partitioned;
```

---

### CTE vs Temporary Table

| Feature     | CTE                                    | Temporary Table                 |
| ----------- | -------------------------------------- | ------------------------------- |
| Execution   | Executes at every call (like View)     | Executes once; result is stored |
| Use Case    | Simplifying queries, modularity        | Storing intermediate results    |
| Performance | Can be slower if reused multiple times | Faster for repeated use         |

---

### SQL DATA OBJECTS
- Table / Temporary Table: This 15 a materialized result set, The only difference between table and temp table is that tables exist after the query executes.
- Subquery : Do not nest them. Mostly just use if you are returning a single number or result (scalar subquery)
- View: This is like a CTE with a name, use when you need to share query logic. Keep in mind that it executes every single time
- Common Table Expression - This is your go to whenever you need to build a complex query, The only time you want to use something else is when you need to materialize 

### SQL Commands for Database & Schema Management

| Task                             | Snowflake Command                                       | Trino Command                                             |
| -------------------------------- | ------------------------------------------------------- | --------------------------------------------------------- |
| List all databases               | `SHOW DATABASES;`                                       | `SHOW CATALOGS;`                                          |
| Current database                 | `SELECT CURRENT_DATABASE();`                            | `SHOW SESSION LIKE 'catalog';`                            |
| List all schemas in a database   | `SHOW SCHEMAS IN DATABASE my_database;`                 | `SHOW SCHEMAS FROM catalog_name;`                         |
| List schemas in current database | `SHOW SCHEMAS;`                                         | `SHOW SCHEMAS;` *(needs default catalog)*                 |
| Current schema                   | `SELECT CURRENT_SCHEMA();`                              | `SHOW SESSION LIKE 'schema';`                             |
| Use database                     | `USE DATABASE my_database;`                             | `USE catalog_name;` *(sets default catalog)*              |
| Use schema                       | `USE SCHEMA my_schema;` *(after setting database)*      | `USE catalog_name.schema_name;` *(sets catalog + schema)* |
| List tables/views in schema      | `SHOW TABLES IN SCHEMA my_database.my_schema;`          | `SHOW TABLES FROM catalog_name.schema_name;`              |
| Describe table structure         | `DESCRIBE TABLE my_database.my_schema.my_table;`        | `DESCRIBE catalog_name.schema_name.my_table;`             |
| Show columns                     | `SHOW COLUMNS IN TABLE my_database.my_schema.my_table;` | `SHOW COLUMNS FROM catalog_name.schema_name.my_table;`    |
| Drop database                    | `DROP DATABASE my_database;`                            | `DROP SCHEMA catalog_name.schema_name;` *(drops schema)*  |
| Drop schema                      | `DROP SCHEMA my_database.my_schema;`                    | `DROP SCHEMA catalog_name.schema_name;`                   |
| Drop table                       | `DROP TABLE my_database.my_schema.my_table;`            | `DROP TABLE catalog_name.schema_name.my_table;`           |

**Key Difference**

* Snowflake: `Database → Schema → Table`
* Trino: `Catalog → Schema → Table` (Catalog ≈ Database in Snowflake terms)

---

### Example Queries

```sql
-- List all catalogs (databases)
SHOW CATALOGS;

-- Show current catalog
SHOW SESSION LIKE 'catalog';

-- View schemas in current catalog
SHOW SCHEMAS;

-- View schemas from a specific catalog
SHOW SCHEMAS FROM academy;
SHOW SCHEMAS LIKE '%boot%';

-- Query all schemas in catalog 'academy' containing 'boot'
SELECT schema_name
FROM academy.information_schema.schemata
WHERE schema_name LIKE '%boot%';

-- View tables in schema 'bootcamp'
SHOW TABLES FROM bootcamp;
```

---

## Python

**Use Case in Data Engineering**

* Bring data from external sources into a datalake, database, or data warehouse.
* Common libraries: `pandas`, `sqlalchemy`, `pyodbc`, `psycopg2`.

---

## SQL + Python (PySpark)

* Combine Python and SQL for big data processing.
* Example:

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("SQL+Python").getOrCreate()

# Load data
df = spark.read.csv("data.csv", header=True, inferSchema=True)

# SQL query
df.createOrReplaceTempView("my_table")
result = spark.sql("SELECT col1, COUNT(*) FROM my_table GROUP BY col1")
result.show()
```

* Benefits:

  * Handle large datasets efficiently.
  * Apply Python transformations on SQL query results.
  * Integrate with Spark DataFrames for ETL pipelines.

---

### Tips & Best Practices

* Always filter data early (`WHERE`) to reduce computation.
* Use CTEs for readability; temp tables for repeated use.
* Avoid `SELECT *` in production queries.
* Backup data before `DELETE` or `UPDATE`.
* Use `EXPLAIN` to optimize query performance.

---
