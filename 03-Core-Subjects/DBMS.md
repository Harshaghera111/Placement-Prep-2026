# 🗄️ DBMS — Database Management Systems

> DBMS is asked in almost every SDE interview. Focus on SQL, normalization, ACID, and indexing.

---

## 📖 Key Concepts Overview

| Topic | Importance |
|-------|-----------|
| ER Model | ⭐⭐⭐ |
| Normalization (1NF–BCNF) | ⭐⭐⭐⭐⭐ |
| SQL (Joins, Aggregation) | ⭐⭐⭐⭐⭐ |
| ACID Properties | ⭐⭐⭐⭐⭐ |
| Indexing | ⭐⭐⭐⭐ |
| Transactions | ⭐⭐⭐⭐ |
| Concurrency Control | ⭐⭐⭐ |

---

## 1. ER Model (Entity-Relationship)

**Entities**: Real-world objects (Student, Course, Order)
**Attributes**: Properties of entities (name, age, price)
**Relationships**: Associations between entities (Student *enrolls in* Course)

**Cardinality Types:**
- **1:1** — One person has one passport
- **1:N** — One department has many employees
- **M:N** — Many students take many courses (needs junction table)

---

## 2. Normalization

Normalization eliminates **data redundancy** and **update anomalies**.

### Normal Forms

| Normal Form | Rule |
|-------------|------|
| **1NF** | Each column has atomic (single) values. No repeating groups. |
| **2NF** | 1NF + No partial dependency (no attribute depends on part of composite PK). |
| **3NF** | 2NF + No transitive dependency (non-key attributes don't depend on other non-key attributes). |
| **BCNF** | 3NF + Every determinant is a candidate key. |

**Example of Transitive Dependency (violates 3NF):**
```
Student(StudentID, StudentName, DeptID, DeptName)
StudentID → DeptID → DeptName  ← transitive!
Fix: Split into Student(StudentID, StudentName, DeptID) + Dept(DeptID, DeptName)
```

**Quick Interview Answer:** "Normalization removes redundancy. We apply 1NF for atomic values, 2NF for no partial dependencies, 3NF for no transitive dependencies."

---

## 3. SQL — Quick Reference

### Joins

```sql
-- INNER JOIN: only matching rows
SELECT e.name, d.dept_name
FROM Employee e
INNER JOIN Department d ON e.dept_id = d.id;

-- LEFT JOIN: all rows from left + matching right
SELECT e.name, d.dept_name
FROM Employee e
LEFT JOIN Department d ON e.dept_id = d.id;

-- Self Join: employee and their manager
SELECT e.name AS employee, m.name AS manager
FROM Employee e
LEFT JOIN Employee m ON e.manager_id = m.id;
```

### Aggregation & Window Functions

```sql
-- GROUP BY with HAVING
SELECT dept_id, COUNT(*) AS emp_count, AVG(salary) AS avg_salary
FROM Employee
GROUP BY dept_id
HAVING COUNT(*) > 5;

-- Window Function: Rank employees by salary within each dept
SELECT name, salary, dept_id,
    RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rank
FROM Employee;

-- Running total
SELECT name, salary,
    SUM(salary) OVER (ORDER BY name) AS running_total
FROM Employee;
```

### Subqueries

```sql
-- Find employees earning more than average
SELECT name, salary FROM Employee
WHERE salary > (SELECT AVG(salary) FROM Employee);

-- Correlated subquery: employees with highest salary in their dept
SELECT name, salary, dept_id FROM Employee e1
WHERE salary = (
    SELECT MAX(salary) FROM Employee e2
    WHERE e2.dept_id = e1.dept_id
);
```

---

## 4. ACID Properties

| Property | Definition | Example |
|----------|------------|---------|
| **Atomicity** | All operations succeed or all fail | Bank transfer: debit + credit either both happen or neither |
| **Consistency** | DB moves from one valid state to another | Account balance can't go negative |
| **Isolation** | Concurrent transactions don't interfere | Two users booking the last seat see different intermediate states |
| **Durability** | Committed data persists even after crash | After "COMMIT", data survives power outage |

**Isolation Levels (from least to most strict):**
1. **Read Uncommitted** — Dirty reads possible
2. **Read Committed** — No dirty reads; non-repeatable reads possible
3. **Repeatable Read** — No non-repeatable reads; phantom reads possible
4. **Serializable** — Full isolation; performance overhead

---

## 5. Indexing

An **index** is a data structure that speeds up data retrieval at the cost of extra storage and slower writes.

### B-Tree Index (Default in most DBs)
- Used for: `=`, `<`, `>`, `BETWEEN`, `ORDER BY`
- Structure: Balanced tree, O(log n) lookup
- Good for: Range queries

### Hash Index
- Used for: `=` (exact match only)
- Structure: Hash table, O(1) average lookup
- Bad for: Range queries

### Clustered vs Non-Clustered Index
- **Clustered**: Data rows are physically ordered by the index key. Only one per table.
- **Non-Clustered**: Separate index with pointers to data rows. Many per table.

### When NOT to Index
- Small tables (full scan faster)
- Frequently updated columns
- Low cardinality columns (e.g., boolean, gender)

---

## 6. Transactions & Concurrency

### Transaction States
```
Active → Partially Committed → Committed
Active → Failed → Aborted (Rolled Back)
```

### Concurrency Problems

| Problem | Description | Solution |
|---------|-------------|----------|
| **Dirty Read** | Reading uncommitted data from another transaction | Read Committed isolation |
| **Non-Repeatable Read** | Same query gives different results in same transaction | Repeatable Read isolation |
| **Phantom Read** | New rows appear in a query within same transaction | Serializable isolation |
| **Lost Update** | Two transactions update same row; one overwrites the other | Locks / optimistic concurrency |

### Locks
- **Shared Lock (S-lock)**: Read-only; multiple allowed simultaneously
- **Exclusive Lock (X-lock)**: Write; only one allowed; blocks all others
- **Deadlock**: Two transactions wait for each other's locks

---

## 7. Keys

| Key Type | Definition |
|----------|-----------|
| **Primary Key** | Uniquely identifies each row; NOT NULL |
| **Foreign Key** | References PK of another table; enforces referential integrity |
| **Candidate Key** | Can be a PK; minimal superkey |
| **Composite Key** | PK made of multiple columns |
| **Surrogate Key** | System-generated ID (e.g., auto-increment) |
| **Natural Key** | Business-meaningful key (e.g., email, SSN) |

---

## ❓ Frequently Asked Interview Questions

**Q1: Difference between WHERE and HAVING?**
> WHERE filters rows *before* aggregation. HAVING filters groups *after* GROUP BY.

**Q2: Can a table have multiple clustered indexes?**
> No. A table can have only **one clustered index** (because data can only be physically sorted one way) but multiple non-clustered indexes.

**Q3: What is a stored procedure?**
> A precompiled set of SQL statements stored in the database. They're called like functions and improve performance by reducing network round trips.

**Q4: Difference between DELETE, TRUNCATE, and DROP?**

| Command | Removes | WHERE clause? | Rollback? | Resets Identity? |
|---------|---------|---------------|-----------|-----------------|
| DELETE | Rows | Yes | Yes | No |
| TRUNCATE | All rows | No | No (usually) | Yes |
| DROP | Entire table | No | No | — |

**Q5: What is denormalization?**
> Intentionally introducing redundancy to improve read performance. Used in reporting databases and data warehouses where reads >> writes.

---

## ✅ Revision Checklist

- [ ] Can I explain 1NF, 2NF, 3NF with examples?
- [ ] Can I write SQL queries with INNER, LEFT, RIGHT, SELF JOIN?
- [ ] Can I write window functions (RANK, ROW_NUMBER, SUM OVER)?
- [ ] Can I explain all 4 ACID properties with real examples?
- [ ] Can I explain B-Tree vs Hash indexes?
- [ ] Can I explain the 4 isolation levels?
- [ ] Do I know the difference between DELETE, TRUNCATE, DROP?
