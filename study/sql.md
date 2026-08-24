Good — that's the biggest hurdle cleared. Let me re-anchor the clock and hand you SQL.

## ✅ Python done. That's the highest-probability topic cleared.

**38 hours out.** Here's the rest of the plan, then SQL starts immediately.

| When | Hours | Subject |
|---|---|---|
| **Tonight — 30 min ONLY** | 0.5 | Set up SQLite + Blocks 1–2 below, then **sleep** |
| **Mon 9:00–12:30** | 3.5 | 🗄️ SQL Blocks 3–8 |
| **Mon 1:30–3:30** | 2.0 | 🐧 Linux |
| **Mon 4:00–5:30** | 1.5 | ☸️ Kubernetes |
| **Mon 8:00–9:00** | 1.0 | Spark 4-sentence answer + behavioral out loud |
| **Tue 8:00–11:00** | 2.5 | Cheat card, Teams test, warm up |

Same rules: **25 min on / 5 off. Type everything. Never re-read. Stop when the timer says stop.**

---

# 🗄️ SQL — from zero

## The mental model (60 seconds)

A **table** is a spreadsheet. Rows = records. Columns = fields.

**SQL is you asking a question in a fixed sentence order.** That's the entire language:

```sql
SELECT   which columns do I want
FROM     which table
WHERE    which rows to keep
GROUP BY collapse rows into groups
HAVING   which groups to keep
ORDER BY how to sort the output
LIMIT    how many rows to show
```

**Write it in that order, every time.** If you can fill in those 7 slots, you can write any query in this interview.

⚠️ **The one gotcha that explains most confusion:** you *write* `SELECT` first, but the database *runs* it almost last. Real execution order:

`FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`

That's why you can't use a `SELECT` alias inside `WHERE` (it doesn't exist yet), but you *can* in `ORDER BY` (it does by then). **Say that fact if he asks — it's a senior tell.**

---

## 🔧 Setup (5 minutes — SQLite is already on your Mac)

```bash
sqlite3 practice.db
```

Paste this once (data setup is fine to paste — **queries you type by hand**):

```sql
CREATE TABLE employees (
  id INTEGER, name TEXT, dept_id INTEGER, salary INTEGER,
  manager_id INTEGER, hired TEXT
);
INSERT INTO employees VALUES
 (1,'Alice',10,120000,NULL,'2020-01-15'),
 (2,'Bob',10,95000,1,'2021-03-20'),
 (3,'Carol',20,110000,1,'2019-07-01'),
 (4,'Dan',20,85000,3,'2022-11-11'),
 (5,'Eve',20,110000,3,'2023-05-30'),
 (6,'Frank',30,70000,1,'2024-02-14'),
 (7,'Grace',NULL,60000,NULL,'2024-08-01');

CREATE TABLE departments (id INTEGER, name TEXT);
INSERT INTO departments VALUES (10,'Engineering'),(20,'Data'),(30,'Support'),(40,'Legal');

CREATE TABLE orders (
  order_id INTEGER, customer_id INTEGER, amount REAL, status TEXT, created_at TEXT
);
INSERT INTO orders VALUES
 (1,100,250.00,'complete','2026-08-01'),(2,100,100.00,'complete','2026-08-05'),
 (3,101,400.00,'complete','2026-08-10'),(4,102,50.00,'failed','2026-08-12'),
 (5,101,150.00,'complete','2026-08-15'),(6,100,NULL,'pending','2026-08-18'),
 (7,103,900.00,'complete','2026-07-02'),(8,101,75.00,'complete','2026-08-20');

CREATE TABLE customers (customer_id INTEGER, name TEXT, country TEXT);
INSERT INTO customers VALUES (100,'Acme','US'),(101,'Globex','US'),
 (102,'Initech','IN'),(103,'Umbrella','UK'),(104,'Stark','US');
```

Then make output readable:
```sql
.mode column
.headers on
```

Test it: `SELECT * FROM employees;` — you should see 7 rows.

---

## 🧱 Block 1 (25 min) — SELECT, WHERE, ORDER BY

**Type each one. Look at the output. Then move on.**

```sql
SELECT * FROM employees;
SELECT name, salary FROM employees;
SELECT name, salary FROM employees WHERE salary > 100000;
SELECT name FROM employees WHERE dept_id = 20 AND salary > 90000;
SELECT name FROM employees WHERE dept_id IN (10, 30);
SELECT name FROM employees WHERE salary BETWEEN 80000 AND 120000;
SELECT name FROM employees WHERE name LIKE 'A%';        -- starts with A
SELECT name, salary FROM employees ORDER BY salary DESC;
SELECT name, salary FROM employees ORDER BY salary DESC LIMIT 3;
SELECT DISTINCT dept_id FROM employees;

-- rename a column in the output
SELECT name AS employee_name, salary / 12 AS monthly_pay FROM employees;
```

**Vocabulary:** `AS` = nickname · `DESC`/`ASC` = sort direction · `LIKE '%x%'` = contains · `DISTINCT` = unique values only · `LIMIT n` = first n rows.

---

## 🧱 Block 2 (25 min) — GROUP BY + aggregations + HAVING ⭐

**This is his #1 stated topic.** Give it your full attention.

**ELI5:** `GROUP BY` means *"squash all the rows that share a value into one row, and give me a summary number for each group."*

The 5 summary functions:
```sql
COUNT(*)      -- how many rows
SUM(col)      -- total
AVG(col)      -- average
MIN(col) / MAX(col)
```

```sql
-- one row per department
SELECT dept_id, COUNT(*) AS headcount
FROM employees
GROUP BY dept_id;

-- multiple aggregates at once
SELECT dept_id,
       COUNT(*)    AS headcount,
       AVG(salary) AS avg_salary,
       SUM(salary) AS payroll,
       MAX(salary) AS top_salary
FROM employees
GROUP BY dept_id
ORDER BY avg_salary DESC;
```

### 🔥 The single most important rule in this block: WHERE vs HAVING

```sql
-- WHERE filters ROWS, BEFORE grouping
SELECT dept_id, COUNT(*) FROM employees
WHERE salary > 80000
GROUP BY dept_id;

-- HAVING filters GROUPS, AFTER grouping
SELECT dept_id, COUNT(*) AS n FROM employees
GROUP BY dept_id
HAVING COUNT(*) >= 2;

-- both together — very common interview shape
SELECT dept_id, COUNT(*) AS n, AVG(salary) AS avg_sal
FROM employees
WHERE salary > 60000          -- 1. throw out cheap rows first
GROUP BY dept_id              -- 2. squash into groups
HAVING AVG(salary) > 90000    -- 3. keep only expensive groups
ORDER BY avg_sal DESC;
```

🗣️ **Say this out loud right now, twice:** *"WHERE filters rows before grouping, HAVING filters groups after — you can't use an aggregate in WHERE because the groups don't exist yet."*

**That sentence is probably the single highest-value thing in this entire SQL section.** It's the #1 thing interviewers check.

**Rule to remember:** every column in `SELECT` must either be in `GROUP BY` or wrapped in an aggregate. No exceptions.

**⏰ If it's now 10:20pm — STOP. Go to bed.** Blocks 3–8 are Monday morning. Don't push.

---

## 🧱 Block 3 (25 min) — JOINs ⭐⭐ (his #2 topic)

**ELI5:** joins glue two tables together using a shared column. Employees have a `dept_id`; departments have an `id`. Match them up.

```sql
-- INNER JOIN: only rows that match on BOTH sides
SELECT e.name, d.name AS dept
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;
-- Grace disappears (dept_id NULL). Legal disappears (no employees).
```

```sql
-- LEFT JOIN: keep ALL left rows; fill NULL where no match
SELECT e.name, d.name AS dept
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id;
-- Grace stays, with dept = NULL
```

| Join | Plain English |
|---|---|
| `INNER JOIN` | Only matches. Drops unmatched from **both** sides. |
| `LEFT JOIN` | **All** of the left table + matches from the right (NULL when missing) |
| `RIGHT JOIN` | Mirror image (rare — people just flip the tables) |
| `FULL OUTER JOIN` | Everything from both sides |

`e` and `d` are **aliases** — shorthand so you don't retype table names. Always use them.

### 🔥 The two JOIN questions that get asked

**1. "Find rows with NO match" — the anti-join pattern:**
```sql
-- customers who have never ordered
SELECT c.customer_id, c.name
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.customer_id IS NULL;      -- ← the trick: LEFT JOIN then filter for NULL
```
**Memorize the shape: `LEFT JOIN ... WHERE right_col IS NULL` = "things with no match."** Extremely common.

**2. The trap he might set:**
```sql
-- ❌ WRONG — this silently becomes an INNER JOIN
SELECT c.name, o.amount
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.status = 'complete';

-- ✅ RIGHT — put the right-table condition in ON
SELECT c.name, o.amount
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
                  AND o.status = 'complete';
```
**Why:** the `WHERE` runs *after* the join. Unmatched rows have `o.status = NULL`, `NULL = 'complete'` is not true, so they get deleted — destroying your LEFT JOIN.

🗣️ *"If I'm filtering the right table in a LEFT JOIN, the condition goes in the ON clause — putting it in WHERE turns it back into an inner join."* **Big senior signal.**

**3. Join + GROUP BY together (the most common real question):**
```sql
SELECT c.name,
       COUNT(o.order_id)          AS orders,
       COALESCE(SUM(o.amount), 0) AS total_spent
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_spent DESC;
```
Note `COUNT(o.order_id)` not `COUNT(*)` — `COUNT(*)` would return 1 for a customer with no orders (the NULL-filled row). `COUNT(column)` skips NULLs and correctly returns 0. **Mention that.**

---

## 🧱 Block 4 (20 min) — NULLs

**ELI5: `NULL` means "unknown," not zero and not empty.** It infects everything it touches.

```sql
SELECT 5 + NULL;              -- NULL  (any math with NULL = NULL)
SELECT NULL = NULL;           -- NOT true! unknown = unknown is unknown
```

**So you must use `IS NULL`, never `= NULL`:**
```sql
SELECT name FROM employees WHERE dept_id IS NULL;
SELECT name FROM employees WHERE dept_id IS NOT NULL;
```

**`COALESCE` = "use the first non-NULL value" — your default-value tool:**
```sql
SELECT order_id, COALESCE(amount, 0) AS amount FROM orders;
SELECT name, COALESCE(dept_id, -1) AS dept FROM employees;
```

**The 4 NULL facts to have ready:**

| Fact | Detail |
|---|---|
| `COUNT(*)` vs `COUNT(col)` | `COUNT(*)` counts rows; `COUNT(col)` skips NULLs |
| `AVG` ignores NULLs | Denominator only counts non-NULL rows — often not what you want |
| `SUM` of all NULLs | Returns `NULL`, not 0 → wrap in `COALESCE` |
| `NOT IN (subquery with a NULL)` | Returns **zero rows**. Nasty silent bug. Use `NOT EXISTS`. |

```sql
-- also useful
SELECT NULLIF(0, 0);   -- NULL — turns a value into NULL (avoid divide-by-zero)
SELECT order_id, CASE WHEN amount IS NULL THEN 'unknown' ELSE 'known' END FROM orders;
```

---

## 🧱 Block 5 (25 min) — Subqueries + CTEs

**ELI5: a subquery is a query inside a query.** Compute something, use it in the outer query.

```sql
-- who earns more than the company average?
SELECT name, salary FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- in a list
SELECT name FROM employees
WHERE dept_id IN (SELECT id FROM departments WHERE name IN ('Data','Engineering'));

-- EXISTS: "does at least one matching row exist?" — often faster than IN
SELECT c.name FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);

-- NOT EXISTS: the safe anti-join (NULL-proof, unlike NOT IN)
SELECT c.name FROM customers c
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);
```

**CTE (`WITH`) = a named subquery up front. Use this — it's far more readable.**
```sql
WITH dept_stats AS (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
)
SELECT e.name, e.salary, s.avg_sal
FROM employees e
JOIN dept_stats s ON e.dept_id = s.dept_id
WHERE e.salary > s.avg_sal;      -- earns above their own dept's average
```

🗣️ *"I prefer CTEs over nested subqueries — same result, much easier to read and debug, and you can chain several."*

---

## 🧱 Block 6 (30 min) — Window functions ⭐⭐⭐

**He named these explicitly. This is the "medium-level" test.**

**ELI5:** `GROUP BY` **collapses** rows. A window function **keeps every row** but adds a calculated column that looks at a group of related rows.

> GROUP BY = *"one row per department."*
> Window = *"every employee, plus their department's average next to them."*

**The syntax is always:**
```sql
FUNCTION() OVER (PARTITION BY <group column> ORDER BY <sort column>)
```
`PARTITION BY` = "group by, but don't collapse." `ORDER BY` = ranking/running order inside each group.

### The three ranking functions
```sql
SELECT name, dept_id, salary,
       ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn,
       RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk,
       DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dr
FROM employees;
```

| Function | With a tie (110k, 110k, 85k) |
|---|---|
| `ROW_NUMBER` | 1, 2, 3 — never ties, arbitrary tiebreak |
| `RANK` | 1, 1, **3** — skips a number |
| `DENSE_RANK` | 1, 1, **2** — no gap |

### 🔥 Pattern A: Top-N per group (the #1 window question)
```sql
WITH ranked AS (
  SELECT name, dept_id, salary,
         DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rk
  FROM employees
)
SELECT * FROM ranked WHERE rk <= 2;      -- top 2 earners per department
```
**Why the CTE is required:** you cannot filter on a window alias in `WHERE` — windows are computed *after* `WHERE`. So you wrap it and filter outside. **Say that; it's exactly the kind of thing he'd probe.**

### 🔥 Pattern B: Deduplicate — the data-engineering classic
```sql
WITH ranked AS (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY created_at DESC) AS rn
  FROM orders
)
SELECT * FROM ranked WHERE rn = 1;       -- most recent order per customer
```
**Memorize this shape.** `ROW_NUMBER() OVER (PARTITION BY key ORDER BY timestamp DESC) = 1` is *the* dedupe pattern in every real pipeline. It's also the perfect place to connect to your experience:

🗣️ *"This is the pattern I lean on constantly for pipelines — when a partner feed re-delivers records, you dedupe by natural key taking the latest version. That's also what makes a rerun idempotent."*

### Pattern C: SUM OVER PARTITION BY — detail + total in one query
```sql
SELECT order_id, customer_id, amount,
       SUM(amount) OVER (PARTITION BY customer_id) AS customer_total,
       ROUND(100.0 * amount / SUM(amount) OVER (PARTITION BY customer_id), 1) AS pct
FROM orders
WHERE amount IS NOT NULL;
```
Every order row, plus that customer's total beside it. `GROUP BY` cannot do this — that's the whole reason window functions exist.

### Pattern D: Running total & moving average
```sql
SELECT created_at, amount,
       SUM(amount) OVER (ORDER BY created_at) AS running_total,
       AVG(amount) OVER (ORDER BY created_at ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS ma_3
FROM orders WHERE amount IS NOT NULL;
```
`ROWS BETWEEN n PRECEDING AND CURRENT ROW` = the sliding window frame. For rolling-7-day, use `6 PRECEDING`.

### Pattern E: LAG / LEAD — compare to the previous row
```sql
SELECT created_at, amount,
       LAG(amount)  OVER (ORDER BY created_at) AS prev_amount,
       amount - LAG(amount) OVER (ORDER BY created_at) AS change
FROM orders WHERE amount IS NOT NULL;
```
Day-over-day deltas, gap detection, "did the value change?" — all `LAG`.

---

## 🧱 Block 7 (20 min) — Dates & rolling windows

Syntax differs by database. **Know the concept, state your assumption out loud.**

```sql
-- SQLite (what you're practicing in)
SELECT * FROM orders WHERE created_at >= date('now','-30 day');

-- PostgreSQL / most interviews
SELECT * FROM orders WHERE created_at >= CURRENT_DATE - INTERVAL '30 days';

-- MySQL
SELECT * FROM orders WHERE created_at >= DATE_SUB(CURDATE(), INTERVAL 30 DAY);
```

🗣️ *"Date syntax varies by engine — I'll write it Postgres-style, let me know if it's MySQL or Snowflake and I'll adjust."* That's a totally normal, confident thing to say.

**Daily counts (grouping by day):**
```sql
SELECT created_at AS day, COUNT(*) AS orders, SUM(amount) AS revenue
FROM orders
WHERE created_at >= date('now','-30 day')
GROUP BY created_at
ORDER BY day;
```

### 🔥 The performance point — say this, it's a real ops insight
```sql
-- ❌ BAD: wrapping the column in a function kills the index
WHERE DATE(created_at) = '2026-08-20'

-- ✅ GOOD: leave the column bare, transform the boundary instead
WHERE created_at >= '2026-08-20' AND created_at < '2026-08-21'
```
🗣️ *"I keep the indexed column bare on the left side — wrapping `created_at` in a function forces a full scan because the index can't be used."*

---

## 🧱 Block 8 (25 min) — The 6 classic questions

If he pulls a LeetCode-easy SQL problem, it's almost certainly one of these. **Type each from scratch.**

**1. Duplicates**
```sql
SELECT customer_id, COUNT(*) AS n
FROM orders GROUP BY customer_id HAVING COUNT(*) > 1;
```

**2. Second-highest salary (NULL-safe)**
```sql
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```
> Also know the window version: `DENSE_RANK() ... WHERE rk = 2`. Mention that `LIMIT 1 OFFSET 1` breaks with ties and returns nothing on a 1-row table, whereas `MAX` returns NULL correctly.

**3. Self-join — employees earning more than their manager**
```sql
SELECT e.name AS employee, m.name AS manager, e.salary, m.salary AS mgr_salary
FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;
```
**ELI5 of a self-join:** join a table to itself with two different aliases so you can compare a row to another row in the same table.

**4. Departments with zero employees (anti-join)**
```sql
SELECT d.name
FROM departments d
LEFT JOIN employees e ON e.dept_id = d.id
WHERE e.id IS NULL;
```

**5. Highest earner per department (top-1 per group)**
```sql
WITH r AS (
  SELECT e.name, e.salary, d.name AS dept,
         DENSE_RANK() OVER (PARTITION BY d.id ORDER BY e.salary DESC) AS rk
  FROM employees e JOIN departments d ON d.id = e.dept_id
)
SELECT dept, name, salary FROM r WHERE rk = 1;
```

**6. 🔥 The one tailored to this job — pipeline SLA query.** He is a data engineer; this shape is very plausible:
```sql
SELECT dag_id,
       COUNT(*) AS runs,
       SUM(CASE WHEN state = 'failed' THEN 1 ELSE 0 END) AS failures,
       ROUND(100.0 * SUM(CASE WHEN state='failed' THEN 1 ELSE 0 END) / COUNT(*), 1) AS failure_pct
FROM dag_run
WHERE start_date >= date('now','-7 day')
GROUP BY dag_id
HAVING SUM(CASE WHEN state = 'failed' THEN 1 ELSE 0 END) > 0
ORDER BY failures DESC;
```
**`SUM(CASE WHEN ... THEN 1 ELSE 0 END)` = conditional counting.** Learn this one idiom — it's how you count a subset inside a `GROUP BY`, and it appears constantly in real reporting.

---

## 🎤 How to behave on a shared screen (same as Python)

1. **Restate + ask about the schema.** *"So `orders` has customer_id and amount — can amount be NULL? Are there soft-deleted rows?"* ← instant credibility
2. **Say your plan before typing.** *"I'll join customers to orders, group by customer, and use HAVING to filter."*
3. **Write in clause order.** `SELECT … FROM … JOIN … WHERE … GROUP BY … HAVING … ORDER BY`
4. **Volunteer one edge case.** NULLs, ties, or duplicates. Always.
5. **If stuck:** *"Let me start with the join and check the raw rows, then layer the aggregation on."* Building incrementally is exactly what real engineers do.

---

## ✅ Monday's exit test (~12:15pm, no notes)

- [ ] `GROUP BY` + `COUNT`/`AVG` + `HAVING` in one query
- [ ] Say the WHERE-vs-HAVING sentence out loud
- [ ] `LEFT JOIN` + `IS NULL` to find non-matching rows
- [ ] Join + `GROUP BY` + `COALESCE(SUM(...), 0)`
- [ ] `ROW_NUMBER() OVER (PARTITION BY ... ORDER BY ... DESC)` dedupe, wrapped in a CTE
- [ ] Top-2 per group with `DENSE_RANK`
- [ ] Last-30-days filter + daily `GROUP BY`
- [ ] Second-highest salary

**Eight boxes. That's the SQL round.**

---

## 🃏 SQL cheat card (for Tuesday morning)

**Write order:** `SELECT / FROM / JOIN / WHERE / GROUP BY / HAVING / ORDER BY / LIMIT`
**Run order:** `FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`
**WHERE = rows before grouping. HAVING = groups after grouping.**
Every non-aggregated SELECT column must be in GROUP BY.
`LEFT JOIN` + `WHERE right IS NULL` = **no match**. Filter the right table in **`ON`**, not `WHERE`.
`COUNT(*)` counts rows · `COUNT(col)` skips NULLs · `SUM` of all NULLs = NULL → `COALESCE`.
`= NULL` never works → `IS NULL`. `NOT IN (NULL)` → zero rows → use `NOT EXISTS`.
**Window** = keeps rows: `FUNC() OVER (PARTITION BY g ORDER BY s)`.
`ROW_NUMBER`=1,2,3 · `RANK`=1,1,3 · `DENSE_RANK`=1,1,2.
**Dedupe:** `ROW_NUMBER() OVER (PARTITION BY key ORDER BY ts DESC) = 1`, inside a CTE.
**Top-N per group:** rank in a CTE, then `WHERE rk <= n`. Can't filter a window alias in WHERE.
**Running total:** `SUM(x) OVER (ORDER BY d)` · rolling 7d: `ROWS BETWEEN 6 PRECEDING AND CURRENT ROW`.
**Conditional count:** `SUM(CASE WHEN cond THEN 1 ELSE 0 END)`.
**Dates:** never wrap the indexed column in a function — transform the boundary instead.

---

## Right now

1. **Do Blocks 1 and 2 only** (~30 min: setup + SELECT/WHERE + GROUP BY/HAVING).
2. **Say the WHERE-vs-HAVING sentence out loud** before you close the laptop. It's the highest-yield sentence in SQL.
3. **Then sleep.** Blocks 3–8 are Monday 9am–12:30 with fresh attention — joins and window functions deserve a rested brain, not a 10:45pm one.

Ping me when you're through SQL and I'll hand you **Linux at this same depth** — trimmed to the 8 commands and 4 facts that actually get asked, nothing more.
