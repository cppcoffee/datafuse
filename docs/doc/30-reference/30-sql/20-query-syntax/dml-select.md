---
title: SELECT
---

Retrieves data from a table.

## Syntax

```sql
SELECT
    [ALL | DISTINCT]
    select_expr [[AS] alias], ...
    [INTO variable [, ...]]
    [ FROM table_references
    [AT ...]
    [WHERE expr]
    [GROUP BY {{col_name | expr | col_alias | col_position}, ...
    | extended_grouping_expr}]
    [HAVING expr]
    [ORDER BY {col_name | expr | col_alias | col_position} [ASC | DESC], ...]
    [LIMIT row_count]
    [OFFSET row_count]
    ]
```

:::tip
numbers(N) – A table for test with the single `number` column (UInt64) that contains integers from 0 to N-1.
:::

## SELECT Clause

```sql
SELECT number FROM numbers(3);
+--------+
| number |
+--------+
|      0 |
|      1 |
|      2 |
+--------+
```

## FROM Clause

```sql
SELECT number FROM numbers(3) AS a; 
+--------+
| number |
+--------+
|      0 |
|      1 |
|      2 |
+--------+
```

## AT Clause

The AT clause enables you to query previous versions of your data. For more information, see [AT](./dml-at.md).

## WHERE Clause

```sql
SELECT number FROM numbers(3) WHERE number > 1;
+--------+
| number |
+--------+
|      2 |
+--------+
```

## GROUP BY Clause

```sql
--Group the rows of the result set by column alias
SELECT number%2 as c1, number%3 as c2, MAX(number) FROM numbers(10000) GROUP BY c1, c2;
+------+------+-------------+
| c1   | c2   | MAX(number) |
+------+------+-------------+
|    1 |    2 |        9995 |
|    1 |    1 |        9997 |
|    0 |    2 |        9998 |
|    0 |    1 |        9994 |
|    0 |    0 |        9996 |
|    1 |    0 |        9999 |
+------+------+-------------+

--Group the rows of the result set by column position in the SELECT list
SELECT number%2 as c1, number%3 as c2, MAX(number) FROM numbers(10000) GROUP BY 1, 2;
+------+------+-------------+
| c1   | c2   | MAX(number) |
+------+------+-------------+
|    1 |    2 |        9995 |
|    1 |    1 |        9997 |
|    0 |    2 |        9998 |
|    0 |    1 |        9994 |
|    0 |    0 |        9996 |
|    1 |    0 |        9999 |
+------+------+-------------+

```

## HAVING Clause

```sql
SELECT number%2 as c1, number%3 as c2, MAX(number) as max FROM numbers(10000) GROUP BY c1, c2 HAVING max>9996;
+------+------+------+
| c1   | c2   | max  |
+------+------+------+
|    1 |    0 | 9999 |
|    1 |    1 | 9997 |
|    0 |    2 | 9998 |
+------+------+------+
```

## ORDER BY Clause

```sql
--Sort by column name in ascending order.
SELECT number FROM numbers(5) ORDER BY number ASC;
+--------+
| number |
+--------+
|      0 |
|      1 |
|      2 |
|      3 |
|      4 |
+--------+

--Sort by column name in descending order.
SELECT number FROM numbers(5) ORDER BY number DESC;
+--------+
| number |
+--------+
|      4 |
|      3 |
|      2 |
|      1 |
|      0 |
+--------+

--Sort by column alias.
SELECT number%2 AS c1, number%3 AS c2  FROM numbers(5) ORDER BY c1 ASC, c2 DESC;
+------+------+
| c1   | c2   |
+------+------+
|    0 |    2 |
|    0 |    1 |
|    0 |    0 |
|    1 |    1 |
|    1 |    0 |
+------+------+

--Sort by column position in the SELECT list
SELECT * FROM t1 ORDER BY 2 DESC;
+------+------+
| a    | b    |
+------+------+
|    2 |    3 |
|    1 |    2 |
+------+------+

SELECT a FROM t1 ORDER BY 1 DESC;
+------+
| a    |
+------+
|    2 |
|    1 |
+------+

```

## LIMIT Clause

```sql
SELECT number FROM numbers(1000000000) LIMIT 1;
+--------+
| number |
+--------+
|      0 |
+--------+

SELECT number FROM numbers(100000) ORDER BY number LIMIT 2 OFFSET 10;
+--------+
| number |
+--------+
|     10 |
|     11 |
+--------+
```

## OFFSET Clause

```sql
SELECT number FROM numbers(5) ORDER BY number OFFSET 2;
+--------+
| number |
+--------+
|      2 |
|      3 |
|      4 |
+--------+
```

## Nested Sub-Selects

SELECT statements can be nested in queries.

```
SELECT ... [SELECT ...[SELECT [...]]]
```

```sql
SELECT MIN(number) FROM (SELECT number%3 AS number FROM numbers(10)) GROUP BY number%2;
+-------------+
| min(number) |
+-------------+
|           1 |
|           0 |
+-------------+
```
