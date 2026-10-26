# SQL & PySpark: NULL-Safe Joins and Duplicate Removal

This guide covers two common data engineering problems:

1. Joining two tables when join columns contain `NULL` values.
2. Removing duplicate records while keeping the latest record using `ROW_NUMBER()` in SQL and PySpark.

## Table of Contents

- [1. Joining Tables with NULL Values](#1-joining-tables-with-null-values)
  - [Sample Tables](#sample-tables)
  - [Normal INNER JOIN](#1-normal-inner-join)
  - [INNER JOIN with NULL Matching](#2-inner-join-with-null-matching)
  - [PySpark NULL-Safe Join](#3-pyspark-dataframe-join-with-null-matching)
  - [Joining on Multiple Columns](#4-joining-on-multiple-columns-with-nulls)
- [2. Removing Duplicates and Keeping the Latest Record](#2-removing-duplicates-and-keeping-the-latest-record)
  - [SQL Solution](#sql-solution)
  - [Duplicates Based on Multiple Columns](#duplicates-based-on-multiple-columns)
  - [PySpark Solution](#pyspark-solution)
  - [Handling Identical Latest Dates](#handling-identical-latest-dates)
  - [Creating a Clean Delta Table](#creating-a-clean-databricks-delta-table)

---

# 1. Joining Tables with NULL Values

When joining two tables, the standard condition `t1.col = t2.col` does not match `NULL` with `NULL`.

In SQL, the expression below evaluates to `UNKNOWN`, not `TRUE`:

```sql
NULL = NULL
```

To match records where both join columns are `NULL`, you need to handle NULL values explicitly or use a null-safe equality operator supported by your database.

## Sample Tables

### Table A — `table_a`

| id | name | city |
|---:|---|---|
| 1 | A | Delhi |
| 2 | B | Mumbai |
| 3 | C | NULL |
| 4 | D | Pune |
| 5 | E | NULL |

### Table B — `table_b`

| id | city | state |
|---:|---|---|
| 101 | Delhi | DL |
| 102 | Mumbai | MH |
| 103 | NULL | Unknown |
| 104 | Jaipur | RJ |

## 1. Normal INNER JOIN

```sql
SELECT *
FROM table_a a
INNER JOIN table_b b
    ON a.city = b.city;
```

### Output

| a_id | name | a_city | b_id | b_city | state |
|---:|---|---|---:|---|---|
| 1 | A | Delhi | 101 | Delhi | DL |
| 2 | B | Mumbai | 102 | Mumbai | MH |

### Problem

Rows where both sides contain `NULL` are not matched.

For example, the following records will not join under the normal equality condition:

**Table A**

| id | name | city |
|---:|---|---|
| 3 | C | NULL |

**Table B**

| id | city | state |
|---:|---|---|
| 103 | NULL | Unknown |

## 2. INNER JOIN with NULL Matching

Use an additional condition to match rows where both join columns are `NULL`.

```sql
SELECT
    a.id AS a_id,
    a.name,
    a.city AS a_city,
    b.id AS b_id,
    b.city AS b_city,
    b.state
FROM table_a a
INNER JOIN table_b b
    ON a.city = b.city
    OR (a.city IS NULL AND b.city IS NULL);
```

### Output

| a_id | name | a_city | b_id | b_city | state |
|---:|---|---|---:|---|---|
| 1 | A | Delhi | 101 | Delhi | DL |
| 2 | B | Mumbai | 102 | Mumbai | MH |
| 3 | C | NULL | 103 | NULL | Unknown |
| 5 | E | NULL | 103 | NULL | Unknown |

This returns:

- Normal matches, such as `Delhi = Delhi`.
- Normal matches, such as `Mumbai = Mumbai`.
- NULL-to-NULL matches.

**Important:** Both NULL rows from `table_a` match the single NULL row from `table_b`. If a join key has multiple NULL rows on both sides, the join can produce multiple combinations.

## 3. PySpark DataFrame Join with NULL Matching

In PySpark, use `eqNullSafe()` for null-safe equality.

```python
from pyspark.sql.functions import col

result = df_a.join(
    df_b,
    df_a.city.eqNullSafe(df_b.city),
    "inner"
)

result.show()
```

### Explanation

- `eqNullSafe()` compares values while treating two NULL values as equal for the comparison.
- `"inner"` returns only matching records.
- Normal non-NULL values are matched as usual.

## 4. Joining on Multiple Columns with NULLs

Suppose you need to join two tables using both `city` and `dept`, and either column can contain NULL values.

### SQL Solution

```sql
SELECT *
FROM table_a a
INNER JOIN table_b b
    ON (
        a.city = b.city
        OR (a.city IS NULL AND b.city IS NULL)
    )
   AND (
        a.dept = b.dept
        OR (a.dept IS NULL AND b.dept IS NULL)
    );
```

Both columns must match, with NULL-to-NULL matches allowed.

### Spark SQL Solution

Spark SQL supports the `<=>` null-safe equality operator.

```sql
SELECT *
FROM table_a a
INNER JOIN table_b b
    ON a.city <=> b.city
   AND a.dept <=> b.dept;
```

### PySpark Solution

```python
result = df_a.join(
    df_b,
    (
        df_a.city.eqNullSafe(df_b.city)
        & df_a.dept.eqNullSafe(df_b.dept)
    ),
    "inner"
)

result.show()
```

**Key takeaway:** Use `eqNullSafe()` in PySpark and `<=>` in Spark SQL when NULL values should match each other.

---

# 2. Removing Duplicates and Keeping the Latest Record

To remove duplicates while retaining the latest record, use the `ROW_NUMBER()` window function with:

- `PARTITION BY` to define which records are duplicates.
- `ORDER BY updated_date DESC` to rank the latest record first.
- `WHERE rn = 1` to keep only the latest record from each group.

## Example Table: `customer_data`

| customer_id | name | city | updated_date |
|---:|---|---|---|
| 101 | A | Delhi | 2024-01-01 |
| 101 | A | Delhi | 2024-02-01 |
| 102 | B | Mumbai | 2024-01-05 |
| 102 | B | Mumbai | 2024-03-10 |
| 103 | C | Pune | 2024-01-15 |

Here, `customer_id` has duplicate records. We want to keep the latest record based on `updated_date`.

## SQL Solution

```sql
WITH cte AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY updated_date DESC
        ) AS rn
    FROM customer_data
)
SELECT *
FROM cte
WHERE rn = 1;
```

### Output

| customer_id | name | city | updated_date | rn |
|---:|---|---|---|---:|
| 101 | A | Delhi | 2024-02-01 | 1 |
| 102 | B | Mumbai | 2024-03-10 | 1 |
| 103 | C | Pune | 2024-01-15 | 1 |

The query retains one record per `customer_id`, selecting the record with the latest `updated_date`.

## Duplicates Based on Multiple Columns

Sometimes, a duplicate is defined using a combination of columns rather than a single key.

For example, duplicates may be identified using:

- `customer_id`
- `email`
- `phone`

In this case, include all three columns in `PARTITION BY`.

```sql
WITH cte AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id, email, phone
            ORDER BY updated_date DESC
        ) AS rn
    FROM customer_data
)
SELECT *
FROM cte
WHERE rn = 1;
```

This retains the latest record for each unique combination of `customer_id`, `email`, and `phone`.

## PySpark Solution

Use the `Window` specification and `row_number()` function to keep the latest record for each customer.

```python
from pyspark.sql.window import Window
from pyspark.sql.functions import row_number, col

w = Window.partitionBy("customer_id").orderBy(
    col("updated_date").desc()
)

latest_df = (
    df.withColumn("rn", row_number().over(w))
      .filter(col("rn") == 1)
      .drop("rn")
)

latest_df.show()
```

### Explanation

1. `Window.partitionBy("customer_id")` groups records by customer.
2. `orderBy(col("updated_date").desc())` places the latest record first.
3. `row_number()` assigns a sequential number to records within each group.
4. `.filter(col("rn") == 1)` retains the first record.
5. `.drop("rn")` removes the temporary ranking column.

## PySpark for Multiple Duplicate Columns

If duplicates are identified by `customer_id`, `email`, and `phone`, use all three columns in the window definition.

```python
from pyspark.sql.window import Window
from pyspark.sql.functions import row_number, col

w = (
    Window.partitionBy("customer_id", "email", "phone")
          .orderBy(col("updated_date").desc())
)

latest_df = (
    df.withColumn("rn", row_number().over(w))
      .filter(col("rn") == 1)
      .drop("rn")
)

latest_df.show()
```

This retains the latest record for each unique combination of the three columns.

## Handling Identical Latest Dates

If two duplicate records have the same `updated_date`, the ordering is not deterministic unless you specify an additional tie-breaker.

For example, use `load_timestamp` as a secondary sorting column.

### SQL Solution

```sql
WITH cte AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY
                updated_date DESC,
                load_timestamp DESC
        ) AS rn
    FROM customer_data
)
SELECT *
FROM cte
WHERE rn = 1;
```

The query first selects the latest `updated_date`, then uses the latest `load_timestamp` to break ties.

For fully deterministic results, ensure the ordering columns uniquely identify the preferred record when ties are possible.

## Creating a Clean Databricks Delta Table

If you want to save the deduplicated data as a Delta table, use `CREATE OR REPLACE TABLE`.

```sql
CREATE OR REPLACE TABLE customer_data_latest AS
WITH cte AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY
                updated_date DESC,
                load_timestamp DESC
        ) AS rn
    FROM customer_data
)
SELECT * EXCLUDE (rn)
FROM cte
WHERE rn = 1;
```

This example uses Databricks SQL's `SELECT * EXCLUDE (rn)` syntax to omit the temporary ranking column from the output.

If you do not need the additional tie-breaker, you can order only by `updated_date DESC`. If you need to support deterministic selection, include a suitable tie-breaker.

**Important:** `CREATE OR REPLACE TABLE` replaces the existing target table. Confirm the target table and permissions before running it.

---

## Quick Reference

| Requirement | SQL / PySpark approach |
|---|---|
| Match NULL values in SQL | `col1 = col2 OR (col1 IS NULL AND col2 IS NULL)` |
| Match NULL values in Spark SQL | `col1 <=> col2` |
| Match NULL values in PySpark | `col1.eqNullSafe(col2)` |
| Keep latest record in SQL | `ROW_NUMBER() OVER (PARTITION BY ... ORDER BY date DESC)` |
| Keep latest record in PySpark | `row_number()` with a `Window` specification |
| Deduplicate using multiple columns | Include all identifying columns in `PARTITION BY` |
| Resolve ties between latest records | Add a deterministic tie-breaker to `ORDER BY` |
| Save deduplicated data in Databricks | `CREATE OR REPLACE TABLE ... AS SELECT ...` |

## Final Takeaways

- Standard SQL equality does not match NULL to NULL.
- Use null-safe equality when NULL values should be considered equal.
- Use `ROW_NUMBER()` to retain one record per duplicate group.
- Use a deterministic secondary sort column when timestamps can tie.
- Choose partition columns carefully: they define what counts as a duplicate.
- Review the target table before replacing it in a production environment.

# SQL & PySpark: NULL Counts and Joins Explained

This guide covers three important data engineering topics:

1. Column-wise and row-wise NULL counts.
2. SQL join types with examples.
3. Join behavior when tables contain duplicate keys and NULL values.

## Table of Contents

- [1. Counting NULL Values](#1-counting-null-values)
  - [Column-Wise NULL Count in SQL](#column-wise-null-count-in-sql)
  - [Row-Wise NULL Count in SQL](#row-wise-null-count-in-sql)
  - [Column-Wise NULL Count in PySpark](#column-wise-null-count-in-pyspark)
  - [Row-Wise NULL Count in PySpark](#row-wise-null-count-in-pyspark)
- [2. SQL Join Types](#2-sql-join-types)
  - [Sample Tables](#sample-tables)
  - [INNER JOIN](#1-inner-join)
  - [LEFT JOIN](#2-left-join--left-outer-join)
  - [RIGHT JOIN](#3-right-join--right-outer-join)
  - [FULL OUTER JOIN](#4-full-outer-join)
  - [CROSS JOIN](#5-cross-join)
  - [NULL-Safe Joins](#null-safe-joins)
  - [PySpark Join Syntax](#pyspark-join-syntax)
- [3. Joins with Duplicate Keys and NULL Values](#3-joins-with-duplicate-keys-and-null-values)
  - [INNER JOIN](#inner-join-with-duplicate-keys)
  - [LEFT JOIN](#left-join-with-duplicate-keys)
  - [RIGHT JOIN](#right-join-with-duplicate-keys)
  - [FULL OUTER JOIN](#full-outer-join-with-duplicate-keys)
  - [CROSS JOIN](#cross-join-with-duplicate-keys)
  - [NULL-Safe INNER JOIN](#null-safe-inner-join)
- [4. Interview Summary](#4-interview-summary)

---

# 1. Counting NULL Values

NULL counting can be performed in two ways:

- **Column-wise:** Count NULL values in each column across all rows.
- **Row-wise:** Count how many columns contain NULL in each row.

## Column-Wise NULL Count in SQL

Suppose the table is named `employee` and contains the columns `id`, `name`, and `city`.

Use `COUNT(*) - COUNT(column_name)` to count NULL values in each column.

```sql
SELECT
    COUNT(*) - COUNT(id) AS id_null_count,
    COUNT(*) - COUNT(name) AS name_null_count,
    COUNT(*) - COUNT(city) AS city_null_count
FROM employee;
```

Alternatively, use `SUM()` with `CASE WHEN`:

```sql
SELECT
    SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END) AS id_null_count,
    SUM(CASE WHEN name IS NULL THEN 1 ELSE 0 END) AS name_null_count,
    SUM(CASE WHEN city IS NULL THEN 1 ELSE 0 END) AS city_null_count
FROM employee;
```

**How it works:**

- `COUNT(*)` counts all rows.
- `COUNT(column_name)` counts only non-NULL values in that column.
- Their difference gives the number of NULL values.

## Row-Wise NULL Count in SQL

To count the number of NULL columns in each row, add multiple `CASE` expressions.

```sql
SELECT
    *,
    (
        CASE WHEN id IS NULL THEN 1 ELSE 0 END +
        CASE WHEN name IS NULL THEN 1 ELSE 0 END +
        CASE WHEN city IS NULL THEN 1 ELSE 0 END
    ) AS row_null_count
FROM employee;
```

The `row_null_count` column shows how many of the specified columns are NULL for each employee.

## Column-Wise NULL Count in PySpark

Use `when()`, `sum()`, and a list comprehension to generate a NULL-count expression for every column.

```python
from pyspark.sql.functions import col, when, sum as spark_sum

df.select([
    spark_sum(
        when(col(c).isNull(), 1).otherwise(0)
    ).alias(c + "_null_count")
    for c in df.columns
]).show()
```

This produces one output row containing a NULL count for each column.

## Row-Wise NULL Count in PySpark

Use `withColumn()` to add a new column that counts NULL values across each row.

```python
from pyspark.sql.functions import col, when

df = df.withColumn(
    "row_null_count",
    sum(
        when(col(c).isNull(), 1).otherwise(0)
        for c in df.columns
    )
)

df.show()
```

The new column `row_null_count` contains the number of NULL values across the original columns.

**Note:** In this example, the count is calculated across all columns present in `df` before `row_null_count` is added.

---

# 2. SQL Join Types

SQL joins combine records from two tables based on a specified join condition.

## Sample Tables

### `employees`

| emp_id | emp_name | dept_id |
|---:|---|---:|
| 1 | A | 10 |
| 2 | B | 20 |
| 3 | C | 30 |
| 4 | D | NULL |

### `departments`

| dept_id | dept_name |
|---:|---|
| 10 | HR |
| 20 | IT |
| 40 | Finance |
| NULL | Unknown |

## 1. INNER JOIN

An `INNER JOIN` returns only records that match in both tables.

```sql
SELECT *
FROM employees e
INNER JOIN departments d
    ON e.dept_id = d.dept_id;
```

### Output

| emp_id | emp_name | e.dept_id | d.dept_id | dept_name |
|---:|---|---:|---:|---|
| 1 | A | 10 | 10 | HR |
| 2 | B | 20 | 20 | IT |

**Why are the other records excluded?**

- Employee C has `dept_id = 30`, which does not exist in `departments`.
- Employee D has `dept_id = NULL`, which does not match the NULL department row under normal equality.

## 2. LEFT JOIN / LEFT OUTER JOIN

A `LEFT JOIN` returns all records from the left table and matching records from the right table.

If no match exists, the right-side columns are filled with NULL.

```sql
SELECT *
FROM employees e
LEFT JOIN departments d
    ON e.dept_id = d.dept_id;
```

### Output

| emp_id | emp_name | e.dept_id | d.dept_id | dept_name |
|---:|---|---:|---:|---|
| 1 | A | 10 | 10 | HR |
| 2 | B | 20 | 20 | IT |
| 3 | C | 30 | NULL | NULL |
| 4 | D | NULL | NULL | NULL |

## 3. RIGHT JOIN / RIGHT OUTER JOIN

A `RIGHT JOIN` returns all records from the right table and matching records from the left table.

If no match exists, the left-side columns are filled with NULL.

```sql
SELECT *
FROM employees e
RIGHT JOIN departments d
    ON e.dept_id = d.dept_id;
```

### Output

| emp_id | emp_name | e.dept_id | d.dept_id | dept_name |
|---:|---|---:|---:|---|
| 1 | A | 10 | 10 | HR |
| 2 | B | 20 | 20 | IT |
| NULL | NULL | NULL | 40 | Finance |
| NULL | NULL | NULL | NULL | Unknown |

## 4. FULL OUTER JOIN

A `FULL OUTER JOIN` returns all records from both tables:

- Matching rows are combined.
- Unmatched rows from the left table are retained.
- Unmatched rows from the right table are retained.

```sql
SELECT *
FROM employees e
FULL OUTER JOIN departments d
    ON e.dept_id = d.dept_id;
```

### Output

| emp_id | emp_name | e.dept_id | d.dept_id | dept_name |
|---:|---|---:|---:|---|
| 1 | A | 10 | 10 | HR |
| 2 | B | 20 | 20 | IT |
| 3 | C | 30 | NULL | NULL |
| 4 | D | NULL | NULL | NULL |
| NULL | NULL | NULL | 40 | Finance |
| NULL | NULL | NULL | NULL | Unknown |

The NULL-looking rows represent different unmatched records: one from `employees` and one from `departments`.

## 5. CROSS JOIN

A `CROSS JOIN` returns the Cartesian product of two tables. Every row in the first table is combined with every row in the second table.

```sql
SELECT *
FROM employees e
CROSS JOIN departments d;
```

If each table contains four rows:

\[
4 \times 4 = 16 \text{ rows}
\]

### Example Output

| emp_id | emp_name | e.dept_id | d.dept_id | dept_name |
|---:|---|---:|---:|---|
| 1 | A | 10 | 10 | HR |
| 1 | A | 10 | 20 | IT |
| 1 | A | 10 | 40 | Finance |
| 1 | A | 10 | NULL | Unknown |
| 2 | B | 20 | 10 | HR |
| ... | ... | ... | ... | ... |

## OUTER JOIN Meaning

`OUTER JOIN` is a general term for joins that preserve unmatched rows.

| Join type | Meaning |
|---|---|
| `LEFT OUTER JOIN` | All left rows and matching right rows |
| `RIGHT OUTER JOIN` | All right rows and matching left rows |
| `FULL OUTER JOIN` | All rows from both tables |

The following are equivalent:

- `LEFT JOIN` = `LEFT OUTER JOIN`
- `RIGHT JOIN` = `RIGHT OUTER JOIN`
- `FULL JOIN` = `FULL OUTER JOIN`

## NULL-Safe Joins

Normal equality does not match NULL values.

```sql
ON e.dept_id = d.dept_id
```

To match NULL to NULL in SQL, explicitly handle NULL values:

```sql
ON e.dept_id = d.dept_id
OR (
    e.dept_id IS NULL
    AND d.dept_id IS NULL
)
```

In Spark SQL and Databricks, use the null-safe equality operator `<=>`:

```sql
ON e.dept_id <=> d.dept_id
```

This treats two NULL values as equal for the join condition.

## PySpark Join Syntax

Assume the DataFrames are `df_emp` and `df_dept`.

```python
# Inner join
df_emp.join(df_dept, "dept_id", "inner")

# Left join
df_emp.join(df_dept, "dept_id", "left")

# Right join
df_emp.join(df_dept, "dept_id", "right")

# Full outer join
df_emp.join(df_dept, "dept_id", "outer")

# Cross join
df_emp.crossJoin(df_dept)
```

### PySpark NULL-Safe Join

```python
result = df_emp.join(
    df_dept,
    df_emp.dept_id.eqNullSafe(df_dept.dept_id),
    "inner"
)

result.show()
```

**Important:** Joining with the shared column name `"dept_id"` uses normal equality; it does not automatically match NULL to NULL. Use `eqNullSafe()` when NULL-safe matching is required.

## SQL Join Summary

| Join type | What it returns |
|---|---|
| `INNER JOIN` | Only matching rows |
| `LEFT JOIN` | All left rows plus matching right rows |
| `RIGHT JOIN` | All right rows plus matching left rows |
| `FULL OUTER JOIN` | All rows from both tables |
| `CROSS JOIN` | Every combination of rows |
| `OUTER JOIN` | General term for left, right, and full outer joins |

---

# 3. Joins with Duplicate Keys and NULL Values

Duplicate join keys can produce more rows than expected. Understanding this behavior is important when validating SQL results and building data pipelines.

## Sample Tables

### Table A

| id |
|---:|
| 1 |
| 1 |
| 1 |
| NULL |
| NULL |

Table A contains five rows: three with `id = 1` and two with `id = NULL`.

### Table B

| id |
|---:|
| 1 |
| 1 |
| NULL |
| NULL |

Table B contains four rows: two with `id = 1` and two with `id = NULL`.

### Important Rule

With a normal join condition:

```sql
ON A.id = B.id
```

- `1` matches `1`.
- `NULL` does not match `NULL`.

When a key occurs multiple times in both tables, the matching rows form all possible combinations.

For a key that appears \(m\) times in Table A and \(n\) times in Table B, the join produces:

\[
m \times n
\]

matching rows for that key.

## INNER JOIN with Duplicate Keys

```sql
SELECT *
FROM A
INNER JOIN B
    ON A.id = B.id;
```

### Matching Logic

- Table A has three occurrences of `1`.
- Table B has two occurrences of `1`.
- The matching rows are \(3 \times 2 = 6\).
- NULL values do not match.

### Output

| A.id | B.id |
|---:|---:|
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |

**Total: 6 rows**

## LEFT JOIN with Duplicate Keys

```sql
SELECT *
FROM A
LEFT JOIN B
    ON A.id = B.id;
```

### Matching Logic

- Three `1` values from A match two `1` values from B, producing six rows.
- Two NULL rows from A do not match any row in B.
- Because this is a left join, both unmatched rows from A are retained.

### Output

| A.id | B.id |
|---:|---:|
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| NULL | NULL |
| NULL | NULL |

**Total: 8 rows**

The last two rows represent unmatched records from Table A. Their `B.id` values are NULL because no right-side row matched.

## RIGHT JOIN with Duplicate Keys

```sql
SELECT *
FROM A
RIGHT JOIN B
    ON A.id = B.id;
```

### Matching Logic

- The matching `1` values produce six rows.
- Two NULL rows from B do not match any row in A.
- Because this is a right join, both unmatched rows from B are retained.

### Output

| A.id | B.id |
|---:|---:|
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| NULL | NULL |
| NULL | NULL |

**Total: 8 rows**

The last two rows represent unmatched records from Table B. They look identical to the unmatched left-side rows, but their origins differ.

## FULL OUTER JOIN with Duplicate Keys

```sql
SELECT *
FROM A
FULL OUTER JOIN B
    ON A.id = B.id;
```

### Matching Logic

- Matching `1` values: \(3 \times 2 = 6\) rows.
- Unmatched NULL rows from A: 2 rows.
- Unmatched NULL rows from B: 2 rows.

Total:

\[
6 + 2 + 2 = 10 \text{ rows}
\]

### Output

| A.id | B.id |
|---:|---:|
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| 1 | 1 |
| NULL | NULL |
| NULL | NULL |
| NULL | NULL |
| NULL | NULL |

**Total: 10 rows**

Although the four unmatched rows look identical, two originate from A and two originate from B.

## CROSS JOIN with Duplicate Keys

```sql
SELECT *
FROM A
CROSS JOIN B;
```

A cross join combines every row in A with every row in B.

- Table A has 5 rows.
- Table B has 4 rows.

\[
5 \times 4 = 20 \text{ rows}
\]

**Total: 20 rows**

No join condition is used, so NULL values do not prevent rows from being combined.

## NULL-Safe INNER JOIN

If NULL should match NULL, use null-safe equality in Spark SQL or Databricks:

```sql
SELECT *
FROM A
INNER JOIN B
    ON A.id <=> B.id;
```

### Matching Logic

- The three `1` values in A match the two `1` values in B: \(3 \times 2 = 6\).
- The two NULL values in A match the two NULL values in B: \(2 \times 2 = 4\).

Total:

\[
6 + 4 = 10 \text{ rows}
\]

**NULL-safe INNER JOIN result: 10 rows**

The same logic can be implemented in PySpark:

```python
result = df_a.join(
    df_b,
    df_a.id.eqNullSafe(df_b.id),
    "inner"
)

result.show()
```

## Join Row Count Summary

| Join type | Normal equality (`=`) | NULL-safe equality |
|---|---:|---:|
| INNER JOIN | 6 | 10 |
| LEFT JOIN | 8 | 10 |
| RIGHT JOIN | 8 | 10 |
| FULL OUTER JOIN | 10 | 10 |
| CROSS JOIN | 20 | 20 |

For the NULL-safe left, right, and full outer joins, the counts are all 10 because the NULL keys match each other, leaving no unmatched NULL rows.

---

# 4. Interview Summary

### How do you count NULL values?

- **Column-wise in SQL:** Use `COUNT(*) - COUNT(column_name)` or `SUM(CASE WHEN column_name IS NULL THEN 1 ELSE 0 END)`.
- **Row-wise in SQL:** Add `CASE` expressions for the columns being checked.
- **Column-wise in PySpark:** Generate `when()` expressions dynamically and aggregate them with `sum()`.
- **Row-wise in PySpark:** Add the per-column NULL indicators for each row.

### How do different SQL joins work?

- **INNER JOIN:** Only matching rows.
- **LEFT JOIN:** All left-side rows and matching right-side rows.
- **RIGHT JOIN:** All right-side rows and matching left-side rows.
- **FULL OUTER JOIN:** All rows from both tables.
- **CROSS JOIN:** Every possible row combination.

### Why can joins create duplicate rows?

If a key appears multiple times in both tables, each occurrence on one side matches every occurrence on the other side. For example, three occurrences on the left and two on the right produce six matches.

### How do you join NULL values?

Use explicit NULL checks in SQL or null-safe equality:

```sql
-- Explicit NULL handling
ON A.id = B.id
OR (A.id IS NULL AND B.id IS NULL)
```

```sql
-- Spark SQL / Databricks
ON A.id <=> B.id
```

```python
# PySpark
df_a.id.eqNullSafe(df_b.id)
```

**Final takeaway:** Always check for duplicate join keys and understand how NULL values are treated before interpreting join row counts or validating source-to-target reconciliation.