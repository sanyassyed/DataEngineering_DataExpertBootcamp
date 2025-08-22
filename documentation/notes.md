# Notes
## Topic 1 - Kickoff 

---

## Topic 2 - Why should you pick DE

---

## Topic 3 - SQL Basic
### Intro to SQL 101
* Why learn SQL
* When to use SQL
* Count, Joins, 
* SELECT, WHERE
* INSERT
* DELETE
* UPDATE
* SQL is for data already `IN` the database, datawarehouse etc

### SQL Command Hierarchy
1. FROM / JOIN
1. WHERE
1. GROUP BY / HAVING
1. SELECT
1. ORDER BY
1. LIMIT

### EXPLAIN
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

This is explain how the query is executed

### GROUP BY, JOIN and Common Table Expression Lab


### Python 
* Bring in data from other sources into the datalake, database, datawarehouse etc.

### SQL + Python
* `Pyspark` : Lets you combine python and sql
