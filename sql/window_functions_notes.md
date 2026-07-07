# SQL Window Functions

## Purpose

This note explains SQL window functions and how they are used in data engineering tasks such as ranking, deduplication, running totals, previous-row comparison, and latest-record selection.

Window functions are commonly used in ETL/ELT transformations, reporting queries, data quality checks, and analytics-ready table preparation.

---

## What It Is

A window function performs a calculation across a group of related rows while keeping the original row-level detail.

In simple terms:

```text
Window function = Calculation across related rows without collapsing the result set
```

Unlike `GROUP BY`, window functions do not reduce multiple rows into one summary row. Instead, they add calculated values beside each row.

---

## Why It Matters

Window functions are important in data engineering because many real-world datasets require row-level analysis within a group.

Common use cases include:

- Finding the latest record per customer, claim, account, or transaction
- Ranking products, employees, providers, or customers
- Calculating running totals
- Comparing current and previous records
- Removing duplicates
- Detecting changes over time
- Preparing analytics-ready datasets
- Supporting Slowly Changing Dimension logic

---

## Basic Syntax

A window function uses the `OVER()` clause.

```sql
FUNCTION_NAME() OVER (
    PARTITION BY column_name
    ORDER BY column_name
)
```

The main parts are:

| Clause | Purpose |
|---|---|
| `PARTITION BY` | Divides rows into groups |
| `ORDER BY` | Defines the sequence inside each group |
| Window function | Performs the calculation |

Example:

```sql
ROW_NUMBER() OVER (
    PARTITION BY customer_id
    ORDER BY order_date DESC
)
```

This means:

```text
For each customer, sort orders by latest date and assign row numbers.
```

---

## GROUP BY vs Window Functions

## GROUP BY

`GROUP BY` summarizes rows and reduces the result set.

Example:

```sql
SELECT
    customer_id,
    SUM(order_amount) AS total_order_amount
FROM orders
GROUP BY customer_id;
```

Result:

```text
One row per customer
```

This is useful for summary reports, but individual order records are no longer visible.

---

## Window Function

A window function keeps row-level detail and adds a calculated column.

Example:

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    SUM(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS running_total
FROM orders;
```

Result:

```text
Each order row remains visible, and the running total appears beside each row.
```

---

## Sample Table

The examples in this note use the following `orders` table.

| order_id | customer_id | order_date | order_amount |
|---:|---:|---|---:|
| 1001 | 1 | 2026-01-01 | 250 |
| 1002 | 1 | 2026-01-03 | 400 |
| 1003 | 1 | 2026-01-05 | 150 |
| 1004 | 2 | 2026-01-02 | 800 |
| 1005 | 2 | 2026-01-04 | 300 |
| 1006 | 2 | 2026-01-06 | 300 |

---

## ROW_NUMBER()

## What It Does

`ROW_NUMBER()` assigns a unique sequential number to rows within each partition.

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    ROW_NUMBER() OVER (
        PARTITION BY customer_id
        ORDER BY order_date DESC
    ) AS row_num
FROM orders;
```

## Practical Explanation

This query assigns a row number to each order for each customer. Since the rows are ordered by `order_date DESC`, the latest order gets row number `1`.

## Common Use Case

Find the latest order per customer.

```sql
WITH ranked_orders AS (
    SELECT
        order_id,
        customer_id,
        order_date,
        order_amount,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY order_date DESC
        ) AS row_num
    FROM orders
)
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount
FROM ranked_orders
WHERE row_num = 1;
```

## Data Engineering Context

This pattern is commonly used to select the most recent record from source systems, CDC feeds, transaction tables, claim status history, or customer profile updates.

---

## RANK()

## What It Does

`RANK()` assigns a rank to rows within each partition. If there is a tie, rows receive the same rank and the next rank is skipped.

```sql
SELECT
    customer_id,
    order_id,
    order_amount,
    RANK() OVER (
        PARTITION BY customer_id
        ORDER BY order_amount DESC
    ) AS amount_rank
FROM orders;
```

Example ranking behavior:

```text
1, 1, 3
```

## Practical Explanation

If two rows have the same value, they share the same rank. The next rank leaves a gap.

## Common Use Case

`RANK()` is useful when tie positions should be preserved, such as sales leaderboards or competition-style rankings.

---

## DENSE_RANK()

## What It Does

`DENSE_RANK()` assigns a rank to rows within each partition. If there is a tie, rows receive the same rank, but the next rank is not skipped.

```sql
SELECT
    customer_id,
    order_id,
    order_amount,
    DENSE_RANK() OVER (
        PARTITION BY customer_id
        ORDER BY order_amount DESC
    ) AS dense_amount_rank
FROM orders;
```

Example ranking behavior:

```text
1, 1, 2
```

## RANK vs DENSE_RANK

| Function | Tie Behavior | Example |
|---|---|---|
| `RANK()` | Leaves gaps after ties | 1, 1, 3 |
| `DENSE_RANK()` | Does not leave gaps after ties | 1, 1, 2 |

## Common Use Case

`DENSE_RANK()` is commonly used when selecting top N records per group.

Example:

```text
Top 3 products per category
Top 5 providers by claim count
Top 10 customers by revenue
```

---

## Running Total Using SUM()

## What It Does

A running total calculates a cumulative value across rows in a specific order.

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    SUM(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM orders;
```

## Practical Explanation

This query calculates the cumulative order amount for each customer over time.

The window frame:

```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```

means:

```text
Start from the first row in the partition and calculate up to the current row.
```

## Data Engineering Context

Running totals are used in:

- Customer lifetime spending
- Account balance calculation
- Revenue accumulation
- Payment tracking
- Inventory movement analysis

---

## LAG()

## What It Does

`LAG()` returns a value from the previous row.

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    LAG(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS previous_order_amount
FROM orders;
```

## Practical Explanation

This query compares each order with the previous order for the same customer.

## Example: Difference From Previous Order

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    LAG(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS previous_order_amount,
    order_amount - LAG(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS amount_difference
FROM orders;
```

## Data Engineering Context

`LAG()` is commonly used for:

- Month-over-month comparison
- Day-over-day trend analysis
- Previous transaction comparison
- Change detection
- Status transition analysis

---

## LEAD()

## What It Does

`LEAD()` returns a value from the next row.

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    LEAD(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS next_order_amount
FROM orders;
```

## Practical Explanation

This query allows the current row to compare itself with the next row in the sequence.

## Data Engineering Context

`LEAD()` is useful when analyzing future-state comparisons, next transaction behavior, next status changes, or time gaps between events.

---

## FIRST_VALUE()

## What It Does

`FIRST_VALUE()` returns the first value in a window.

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    FIRST_VALUE(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS first_order_amount
FROM orders;
```

## Practical Explanation

This query shows each customer's first order amount while keeping every order row visible.

## Common Use Case

Examples include:

- First purchase amount
- First claim status
- First login date
- First transaction amount

---

## LAST_VALUE()

## What It Does

`LAST_VALUE()` returns the last value in a window.

```sql
SELECT
    order_id,
    customer_id,
    order_date,
    order_amount,
    LAST_VALUE(order_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_order_amount
FROM orders;
```

## Practical Explanation

The window frame is important for `LAST_VALUE()`.

```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
```

This frame allows SQL to look across the full partition and return the actual last value.

Without the correct frame, `LAST_VALUE()` may return the current row instead of the final row in the partition.

---

## Common Window Functions

| Function | Purpose | Common Use Case |
|---|---|---|
| `ROW_NUMBER()` | Assigns a unique sequence | Latest record per group |
| `RANK()` | Ranking with gaps | Ranking with tied positions |
| `DENSE_RANK()` | Ranking without gaps | Top N per group |
| `SUM() OVER` | Cumulative total | Running total |
| `AVG() OVER` | Average across a window | Moving average |
| `LAG()` | Previous row value | Period-over-period comparison |
| `LEAD()` | Next row value | Current vs next comparison |
| `FIRST_VALUE()` | First value in a group | First transaction or first status |
| `LAST_VALUE()` | Last value in a group | Latest value across full partition |

---

## Common Use Cases

Window functions are commonly used for the following patterns.

| Pattern | Function Commonly Used |
|---|---|
| Latest record per customer | `ROW_NUMBER()` |
| Remove duplicates | `ROW_NUMBER()` |
| Top 3 products per category | `DENSE_RANK()` |
| Running total | `SUM() OVER` |
| Month-over-month comparison | `LAG()` |
| Compare current row with next row | `LEAD()` |
| First event per user | `FIRST_VALUE()` |
| Latest status across full history | `LAST_VALUE()` or `ROW_NUMBER()` |

---

## Data Engineering Context

Window functions are usually applied in the transformation layer of a data pipeline.

```text
Raw Data
   ↓
Cleaning and Standardization
   ↓
Transformation Layer
   ↓
Window Function Logic
   ↓
Analytics-Ready Dataset
```

Examples:

- Deduplicate customer records before loading a dimension table
- Select the latest claim status from a claim history table
- Rank providers by claim volume
- Calculate running payment totals
- Compare current and previous transactions
- Build latest snapshot tables from historical data
- Prepare reporting tables for Power BI or dashboards

---

## Real-World Example: Latest Claim Status

A healthcare claims table may contain multiple status updates for the same claim.

| claim_id | status | updated_at |
|---|---|---|
| C001 | Submitted | 2026-01-01 |
| C001 | Approved | 2026-01-03 |
| C001 | Paid | 2026-01-05 |
| C002 | Submitted | 2026-01-02 |
| C002 | Denied | 2026-01-04 |

To find the latest status per claim:

```sql
WITH ranked_claims AS (
    SELECT
        claim_id,
        status,
        updated_at,
        ROW_NUMBER() OVER (
            PARTITION BY claim_id
            ORDER BY updated_at DESC
        ) AS row_num
    FROM claims
)
SELECT
    claim_id,
    status,
    updated_at
FROM ranked_claims
WHERE row_num = 1;
```

Expected result:

| claim_id | status | updated_at |
|---|---|---|
| C001 | Paid | 2026-01-05 |
| C002 | Denied | 2026-01-04 |

## Practical Explanation

The query partitions the data by `claim_id`, sorts each claim's status history by latest update time, assigns row numbers, and keeps only the latest row for each claim.

This pattern is useful when building current-state tables from historical event data.

---

## Common Mistakes

- Using `GROUP BY` when row-level detail must be preserved
- Forgetting `PARTITION BY` when calculations should reset by group
- Forgetting `ORDER BY` for ranking or time-based calculations
- Confusing `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()`
- Using `LAST_VALUE()` without defining the correct window frame
- Assuming window functions reduce the number of rows
- Not handling ties correctly
- Ordering only by date when duplicate dates exist
- Not adding a deterministic tie-breaker such as `order_id` or `updated_at`

---

## Key Takeaway

Window functions are used when a calculation needs to compare or summarize related rows while keeping the original row-level detail.

The most important patterns to understand are:

```text
Latest record per group
Top N per group
Running total
Previous-row comparison
Deduplication
Ranking
```

The core mental model is:

```text
PARTITION BY = split rows into groups
ORDER BY     = define the sequence inside each group
FUNCTION     = calculate across the window
```