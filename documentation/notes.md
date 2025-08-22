# Notes
## Topic 1 - Kickoff 

---

## Topic 2 - Why should you pick DE

---

## Topic 3 - SQL Basic

### Lecture 1 - Intro to SQL 101
* Why learn SQL
* When to use SQL
* Count, Joins, 
* SELECT, WHERE
* INSERT
* DELETE
* UPDATE
* SQL is for data already `IN` the database, datawarehouse etc


#### SQL Command Hierarchy
1. FROM / JOIN
1. WHERE
1. GROUP BY / HAVING
1. SELECT
1. ORDER BY
1. LIMIT


#### SQL RULES
* FILTER as soon as you can so you process least amout of data


#### EXPLAIN Keyword
This command explains how the query is executed

```sql
EXPLAIN WITH cte 
AS
(SELECT id,
        name,
        RANK() OVER (ORDER BY age ASC) age_rnk
FROM Employee)
SELECT *
FROM Employee e,
     cte c
WHERE e.id = c.id AND age_rnk = 1;
```

---

### Lab 1 - GROUP BY, JOIN and Common Table Expression Lab

#### CTE vs Temporary Table
* CTE : Is executed at every call (Similar to Views)
* Temporary Tables: Are executed and the result is stored (Similar to Materialized Views)

#### Partitioning
```sql
-- View the table creation command
SHOW CREATE TABLE zachwilson.nba_players_ranked;

-- Create table with partition
CREATE TABLE zachwilson. nba_players_ranked_partitioned (
player_name VARCHAR, -- 'LeBron James', 'jsoewiurwerlj', ''
pts DECIMAL, -- 3.7 or 888.2
rank BIGINT, -- Only numbers up to this big for BIGINT 9,223,372,036,854, 775,807 (9.2 quintillion) 2^64 , integer up to 2 BILLION, 2^32
season SMALLINT -- this table will only work for the next 2 billion seasons, TINYINT (only up to 255), smallint (32,000)
) 
WITH (
     partitioning = ARRAY['season']
     )

-- Insert values into the above table 
INSERT INTO zachwilson.nba_players_ranked_partitioned
WITH players_ranked 
AS 
(
SELECT player_name,
        pts,
        RANK() OVER (ORDER BY pts DESC) AS RANK,
        season
FROM bootcamp. nba_player_seasons
)
SELECT * FROM players_ranked
WHERE season = 2007

-- Test
SELECT * FROM zachwilson.nba_players_ranked_partitioned WHERE season = 2007 AND rank = 1
DELETE FROM zachwilson.nba_players_ranked_partitioned
```

#### Query Syntax

Perfect 👍 I expanded your table to include **commands for selecting/using databases & schemas** and a few other useful ones for managing objects. Here’s a more complete reference:

---

| Task                                                 | **Snowflake Command**                                   | **Trino Command**                                                     |
| ---------------------------------------------------- | ------------------------------------------------------- | --------------------------------------------------------------------- |
| **List all databases**                               | `SHOW DATABASES;`                                       | `SHOW CATALOGS;`                                                      |
| **What is the current database**                     | `SELECT CURRENT_DATABASE();`                            | `SHOW SESSION LIKE 'catalog';`                                        |
| **List all schemas in a database**                   | `SHOW SCHEMAS IN DATABASE my_database;`                 | `SHOW SCHEMAS FROM catalog_name;`                                     |
| **List all schemas in the current database**         | `SHOW SCHEMAS;`                                         | `SHOW SCHEMAS;` *(works only if a default catalog is set)*            |
| **What is the current schema**                       | `SELECT CURRENT_SCHEMA();`                              | `SHOW SESSION LIKE 'schema';`                                         |
| **Use a particular database**                        | `USE DATABASE my_database;`                             | `USE catalog_name;` *(sets default catalog for session)*              |
| **Use a particular schema**                          | `USE SCHEMA my_schema;` *(after setting database)*      | `USE catalog_name.schema_name;` *(sets both catalog + schema)*        |
| **List all data objects (tables/views) in a schema** | `SHOW TABLES IN SCHEMA my_database.my_schema;`          | `SHOW TABLES FROM catalog_name.schema_name;`                          |
| **Describe a table structure**                       | `DESCRIBE TABLE my_database.my_schema.my_table;`        | `DESCRIBE catalog_name.schema_name.my_table;`                         |
| **Show columns of a table**                          | `SHOW COLUMNS IN TABLE my_database.my_schema.my_table;` | `SHOW COLUMNS FROM catalog_name.schema_name.my_table;`                |
| **Drop a database**                                  | `DROP DATABASE my_database;`                            | `DROP SCHEMA catalog_name.schema_name;` *(drops schema, not catalog)* |
| **Drop a schema**                                    | `DROP SCHEMA my_database.my_schema;`                    | `DROP SCHEMA catalog_name.schema_name;`                               |
| **Drop a table**                                     | `DROP TABLE my_database.my_schema.my_table;`            | `DROP TABLE catalog_name.schema_name.my_table;`                       |

---

⚡ Key differences:

* **Snowflake** → `Database → Schema → Table`
* **Trino** → `Catalog → Schema → Table` (catalog ≈ database in Snowflake terms)

#### Example Queries
```sql
-- List the databases
SHOW CATALOGS; -- academy, snowflake etc
-- View the current database
-- SHOW SESSION LIKE 'catalog'; 

--View the schemas in the current database
SHOW SCHEMAS; -- bootcamp, bootcamp_de etc

-- View schemas from a particular database
SHOW SCHEMAS FROM academy;
SHOW SCHEMAS LIKE '%boot%';

-- View all the schemas in the database academy with the word 'boot' in it
SELECT schema_name
FROM academy.information_schema.schemata
WHERE schema_name LIKE '%boot%';

-- View the tables in the schema named bootcamp
SHOW TABLES FROM bootcamp;

```

---

## Python 
* Bring in data from other sources into the datalake, database, datawarehouse etc.

## SQL + Python
* `Pyspark` : Lets you combine python and sql
