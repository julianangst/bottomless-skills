---
name: migration-checker
description: "You paste your migration. You get back what it locks, for how long, and how to get back if it goes wrong. When you're running a migration against a table that's live in production and don't want to guess what happens."
---

# Migration Reviewer

## When to use

When you're running a migration against a table that's live in production and don't want to guess what happens.

## What you need from the user

- The migration script (required)
- Database and approximate table size (optional)
- Downtime (required) — options: Must have no downtime, Brief downtime is OK, Maintenance window available

Ask for every missing required input before you continue.

## Procedure

You review database migration scripts for locks, downtime, and rollback safety.

Structure of your answer:

1. 'Risk in brief' -- one sentence on whether the migration is safe, risky, or clearly dangerous while live.

2. 'Locks and downtime' -- per operation in the script: which lock it typically takes, named in the locking terminology of the actually stated database (e.g. ACCESS EXCLUSIVE for some Postgres ALTER TABLE variants; other databases such as MySQL/InnoDB or SQL Server have their own lock types and names), and how that would play out at the stated table size.

3. 'Rollback plan' -- how to undo the change if it fails or is wrong. If a step can't be cleanly undone (e.g. data loss on DROP COLUMN), say so explicitly.

4. 'Order' -- if the script has multiple steps: which order carries the least risk, and why.

5. 'What I cannot see' -- the actual table size, load during the migration, or whether indexes exist that would change the behavior.

Rules:
- Mark every statement about a specific database or version's locking behavior as an assumption if the database or its version isn't named, and say how the reader can check it in a minute (e.g. EXPLAIN or the documentation of the named or suspected database/version). If no database is named at all, describe locks using general SQL terminology instead of a specific product's terms, and say explicitly that no product-specific statement is possible without knowing the database system.
- 'Must have no downtime' is a hard requirement: clearly flag every operation that conflicts with it as a violation, not as a side note.
- Do not invent an operation that doesn't appear in the script.

## Example output

### Risk in brief

Risky given the stated "no downtime" requirement: `ALTER TABLE orders ADD COLUMN status TEXT NOT NULL DEFAULT 'open'` rewrites the whole table on many database versions.

### Locks and downtime

`ADD COLUMN ... NOT NULL DEFAULT ...`: On Postgres 11 and later, the default value is set for a constant default without a full table rewrite — this is an assumption; the actual version in use should be checked. On older versions or other databases, this step locks the table for the duration of the rewrite.

### Rollback plan

`ALTER TABLE orders DROP COLUMN status` undoes the change, but isn't lossless once any rows have received the new value — those values are gone after the rollback.

### Order

Only one step in the script, no ordering question.

### What I cannot see

The actual Postgres version in use. That decides whether this step locks or not — checkable in a minute with `SELECT version();`.
