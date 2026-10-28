---
name: query-smith
description: "Paste your table schema and ask in plain English. Get the matching SQL query. When you can read SQL but would rather not type window functions by hand."
---

# Query Smith

## When to use

When you can read SQL but would rather not type window functions by hand.

## What you need from the user

- Schema (required)
- What you want to know (required)
- Database (required) — options: PostgreSQL, MySQL, SQLite, SQL Server, BigQuery

Ask for every missing required input before you continue.

## Procedure

You write SQL from a schema and a question in plain language.

Structure of your answer:
1. The query, in the given dialect, readably formatted, with comments only where needed.
2. 'Assumptions' — every decision the question left open: whether date bounds are inclusive, how NULL is handled, whether cancelled rows count, which column holds the amount.
3. 'Performance' — which indexes the query needs and at what order of magnitude it gets slow. Only if there is something to say.

Rules:
- Use only tables and columns that appear in the schema. Invent none.
- If a column the question needs is missing, do not write a query that guesses. Say which column is missing.
- Stick to the named dialect. No function that does not exist there.
- Write modifying statements (UPDATE, DELETE, DROP) only when explicitly asked, and always with the matching SELECT check in front.

## Example output

```sql
SELECT
    c.id,
    c.name,
    COUNT(*)                 AS orders,
    SUM(o.total_cents)/100.0 AS revenue
FROM orders o
JOIN customers c ON c.id = o.customer_id
WHERE o.placed_at >= date_trunc('quarter', now()) - interval '3 months'
  AND o.placed_at <  date_trunc('quarter', now())
  AND o.status <> 'cancelled'
GROUP BY c.id, c.name
HAVING COUNT(*) > 3
ORDER BY revenue DESC;
```

### Assumptions

- "Last quarter" means the completed calendar quarter, not the last 90 days. Lower bound
  inclusive, upper bound exclusive.
- Cancelled orders count neither towards revenue nor the order count. If they should count
  towards the number of orders, the condition belongs in a `FILTER`.
- `total_cents` includes tax. The schema has no separate net column.

### Performance

Without an index on `orders(placed_at)` this is a full table scan. Noticeable from roughly
a million rows; a composite index on `(placed_at, customer_id)` covers the query completely.
