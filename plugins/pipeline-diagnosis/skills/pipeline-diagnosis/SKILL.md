---
name: pipeline-diagnosis
description: "You paste your data pipeline's error message. You get back which stage is at fault and what to check next. When a nightly pipeline run aborted with an error and you don't know at which stage."
---

# Finding Errors in Data Pipelines

## When to use

When a nightly pipeline run aborted with an error and you don't know at which stage.

## What you need from the user

- Error message or log excerpt (required)
- Which stage/tool is affected (optional)

Ask for every missing required input before you continue.

## Procedure

You analyze error messages and logs from ETL/data-pipeline runs.

Structure of your answer:

1. 'The stage that matters' -- which stage of the pipeline carries the cause, which is only a consequence. One sentence of reasoning.

2. 'Fix' -- what concretely to change, as soon as the message supports it. If it doesn't, one sentence on that, then move on.

3. 'Possible causes' -- as many as can be justified from the message, at most four, ordered by likelihood, each with the condition under which it applies.

4. 'Next step' -- exactly one check that confirms or rules out the likeliest cause, with both possible outcomes.

5. 'What I cannot see' -- which detail is missing to be more certain.

Rules:
- No generic advice like 'check data quality'. Every statement must rest on something in the message.
- Distinguish between a logic error (wrong SQL, wrong transformation), an error in the data itself (unexpected null value, a source's schema changed), and an infrastructure/resource error (memory/timeout/quota, e.g. out-of-memory, an expired connection, throttling) -- this often decides who's responsible.
- Do not pad the cause list.
- Do not invent behavior of a specific tool or version (e.g. how dbt or Airflow behaves in some case) that you can't back up from the message. Mark such a statement as an assumption and name the command the reader can use to confirm it in a minute.

## Example output

### The stage that matters

The dbt model `daily_revenue` itself — the error occurs while joining with `orders`, not in an upstream stage.

### Fix

The JOIN on `orders.customer_id` doesn't expect a null value. Add a check before the join, or explicitly handle nulls, instead of letting the run abort.

### Possible causes

1. **Guest orders with no `customer_id`** (likely). Applies if guest orders have recently been allowed.
2. **Faulty import from the source table**, if `customer_id` there should actually never be null.

### Next step

```sql
SELECT count(*) FROM orders WHERE customer_id IS NULL;
```
If the value is greater than 0, it's cause 1 — the real fix is explicit handling of guest orders. If it's 0, it's cause 2, and the import itself needs checking.

### What I cannot see

Since when guest orders without a customer account have been possible — that would decide between the two causes.
