# Data Engineer Interview Questions

A comprehensive collection of interview questions covering SQL, PySpark, data warehousing, AWS, Databricks, ETL pipelines, performance optimization, real-world scenarios, and behavioral questions.

## Table of Contents

1. [SQL](#1-sql)
2. [PySpark / Apache Spark](#2-pyspark--apache-spark)
3. [Data Warehousing & Modeling](#3-data-warehousing--modeling)
4. [AWS Data Engineering](#4-aws-data-engineering)
5. [Databricks / Delta Lake](#5-databricks--delta-lake)
6. [ETL / ELT & Pipelines](#6-etl--elt--pipelines)
7. [Performance & Optimization](#7-performance--optimization)
8. [Real-World Scenario Questions](#8-real-world-scenario-questions)
9. [Behavioral Questions](#9-behavioral-questions)

---

## 1. SQL — Very High Weightage

1. What is the difference between `WHERE` and `HAVING`?
2. How does `INNER JOIN` differ from `LEFT JOIN`? When should you use each?
3. How do you find duplicate records in a table?
4. How do you write a query to find the Nth-highest salary?
5. What is the difference between SCD Type 1, Type 2, and Type 3?
6. How do you optimize slow SQL queries?
7. What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?
8. Explain window functions: `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()`.
9. How does `COALESCE()` work?
10. How do you find records present in Table A but not in Table B?
11. What is the difference between `UNION` and `UNION ALL`?
12. What is indexing? Explain clustered vs. non-clustered indexes.
13. How does `NULL` behave in joins and aggregations?

## 2. PySpark / Apache Spark

1. What is the difference between RDD, DataFrame, and Dataset?
2. What is lazy evaluation in Spark?
3. Explain wide vs. narrow transformations.
4. What causes shuffles in Spark?
5. What is the difference between `repartition()` and `coalesce()`?
6. How does Spark handle fault tolerance?
7. What is the Catalyst Optimizer?
8. What is the difference between `cache()` and `persist()`?
9. What happens when you use `collect()` on a large dataset?
10. Explain broadcast joins.
11. How do you handle skewed data?
12. What is the difference between `map()`, `flatMap()`, and `filter()`?
13. How do actions trigger execution in Spark?

## 3. Data Warehousing & Modeling

1. What is a fact table?
2. What is the difference between fact and dimension tables?
3. What is the difference between Star and Snowflake schemas?
4. What is a surrogate key?
5. Explain Slowly Changing Dimensions (SCD).
6. What is the grain of a table?
7. What is a data mart?
8. What is the difference between OLTP and OLAP?
9. What is a conformed dimension?
10. When should you use bridge tables?

## 4. AWS Data Engineering

1. What is the difference between Amazon S3, HDFS, and EBS?
2. What is the consistency model of Amazon S3?
3. What is the difference between AWS Glue and Amazon EMR?
4. What is an AWS Glue Crawler?
5. How does Amazon Redshift Spectrum work?
6. What is the difference between `COPY` and `INSERT` in Redshift?
7. What are distribution styles and sort keys in Redshift?
8. How do you optimize Redshift performance?
9. What is Amazon Athena, and when should you use it?
10. How do you handle schema evolution in AWS Glue?

## 5. Databricks / Delta Lake

1. What is Delta Lake?
2. What is the difference between Delta Lake and Parquet?
3. What does ACID compliance mean in Delta Lake?
4. Explain `MERGE INTO` in Delta Lake.
5. What is time travel in Delta Lake?
6. What is the difference between `OPTIMIZE` and `Z-ORDER`?
7. How does Delta Lake handle concurrent writes?
8. Explain the Bronze, Silver, and Gold architecture.
9. How do you handle late-arriving data?
10. What is Auto Loader in Databricks?

## 6. ETL / ELT & Pipelines

1. What is the difference between ETL and ELT?
2. What is an incremental load?
3. How do you design idempotent pipelines?
4. How do you implement data quality checks?
5. How do you handle schema mismatches?
6. How do you reprocess data for a specific date?
7. What is watermarking?
8. How do you handle backfills?
9. What happens when a pipeline job fails partially?
10. How do you monitor data pipelines?

## 7. Performance & Optimization — Very Important

1. How do you optimize Spark jobs?
2. What causes Out-of-Memory (OOM) errors?
3. How do you handle the small-files problem?
4. What is partition pruning?
5. What is the difference between hash joins and sort-merge joins?
6. When should you use broadcast joins?
7. How does predicate pushdown work?
8. How do you optimize data loading from S3 to Redshift using `COPY`?

## 8. Real-World Scenario Questions — Interview Favorites

1. How would you design a data pipeline processing 10 TB of data per day?
2. How do you handle late-arriving events?
3. A job has been running successfully for months but suddenly becomes slow. How would you debug it?
4. How do you ensure exactly-once processing?
5. How do you recover from data corruption?
6. How do you handle schema changes in source systems?
7. How do you design a retry mechanism?
8. How do you ensure data reconciliation between source and target systems?

## 9. Behavioral Questions

1. Explain an end-to-end data pipeline you built.
2. What is the biggest performance issue you have solved?
3. Describe a production failure and how you fixed it.
4. How do you prioritize data accuracy vs. latency?
5. How do you handle stakeholder pressure?

---

## Recommended Preparation Strategy

Prioritize your preparation in the following order:

1. **SQL** — Joins, window functions, ranking, deduplication, and query optimization.
2. **PySpark** — Transformations, shuffles, joins, partitioning, and performance tuning.
3. **Real-world scenarios** — Pipeline design, incremental processing, fault tolerance, and data reconciliation.
4. **ETL / ELT and data modeling** — Idempotency, SCD, dimensional modeling, and data quality.
5. **AWS and Databricks** — S3, Glue, EMR, Redshift, Delta Lake, and Spark optimization.
6. **Behavioral questions** — Prepare practical examples from your own projects and production experience.

**Interview tip:** Practice explaining not only *what* a technology does, but also *why* you would use it, its trade-offs, and how you have applied it in a real project.