# SQL Notes

> Personal notes on SQL — queries, concepts, and interview-relevant topics.

---

## Core Concepts

- **DDL** — CREATE, ALTER, DROP, TRUNCATE
- **DML** — SELECT, INSERT, UPDATE, DELETE
- **DCL** — GRANT, REVOKE
- **TCL** — COMMIT, ROLLBACK, SAVEPOINT

---

## Joins

| Type | Description |
|---|---|
| INNER JOIN | Returns matching rows from both tables |
| LEFT JOIN | All rows from left + matching from right |
| RIGHT JOIN | All rows from right + matching from left |
| FULL OUTER JOIN | All rows from both tables |
| CROSS JOIN | Cartesian product |
| SELF JOIN | Join a table with itself |

---

## Important Clauses

- `WHERE` — filters rows before grouping
- `HAVING` — filters groups after `GROUP BY`
- `ORDER BY` — sorts results
- `GROUP BY` — aggregates rows
- `DISTINCT` — removes duplicates
- `LIMIT / OFFSET` — pagination

---

## Aggregate Functions

- `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`

---

## Subqueries

```sql
-- Scalar subquery
SELECT name FROM employees WHERE salary = (SELECT MAX(salary) FROM employees);

-- Correlated subquery
SELECT name FROM employees e1 WHERE salary > (SELECT AVG(salary) FROM employees e2 WHERE e1.dept = e2.dept);
```

---

## Window Functions

```sql
ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC)
RANK() OVER (ORDER BY salary DESC)
DENSE_RANK() OVER (ORDER BY salary DESC)
LAG(salary, 1) OVER (ORDER BY hire_date)
LEAD(salary, 1) OVER (ORDER BY hire_date)
```

---

## Indexing

- **Primary Index** — on primary key, automatically created
- **Secondary Index** — on non-primary columns
- **Composite Index** — on multiple columns
- **Clustered Index** — physically reorders data
- **Non-Clustered Index** — logical ordering via pointer

---

## Normalization (Quick Ref)

| Form | Rule |
|---|---|
| 1NF | Atomic values, no repeating groups |
| 2NF | 1NF + no partial dependencies |
| 3NF | 2NF + no transitive dependencies |
| BCNF | 3NF + every determinant is a candidate key |

---

## Common Interview Questions

- Difference between `WHERE` and `HAVING`?
- What is a correlated subquery?
- Difference between `RANK()` and `DENSE_RANK()`?
- How does indexing improve query performance?
- What is the difference between clustered and non-clustered index?
- Write a query to find the 2nd highest salary.

```sql
-- 2nd highest salary
SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees);

-- Using DENSE_RANK
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk FROM employees
) t WHERE rnk = 2;
```

---

*Add notes as you learn. Keep it simple.*
