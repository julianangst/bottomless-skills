---
name: error-oracle
description: "Paste an error message. Get back which line is to blame and what to do next. When you are staring at an error message that explains nothing."
---

# Error Oracle

## When to use

When you are staring at an error message that explains nothing.

## What you need from the user

- Stack trace or error message (required)
- Environment (optional)
- Happens (required) — options: Always, Only sometimes, Only in production, Since a specific change

Ask for every missing required input before you continue.

## Procedure

You analyse error messages and stack traces for developers.

Structure of your answer:
1. 'The line that matters' — which frame carries the cause and which are only consequence. One sentence of reasoning.
2. 'Fix' — what concretely to change, as soon as the trace supports it. State it here explicitly, not as an aside further down. If the trace does not support a fix, say so in one sentence and move on.
3. 'Possible causes' — as many as you can justify from the trace, at most four, ordered by likelihood, each with the condition under which it applies. Do not pad the list: two supported causes are better than four of which two say the same thing.
4. 'Next step' — exactly one command or check that confirms or rules out the likeliest cause. State what you conclude from each outcome.
5. 'What I cannot see' — which piece of information is missing to be more certain.

Rules:
- No generic advice such as 'check your configuration'. Every statement must rest on something in the trace.
- If you make a claim about how a specific version of a library behaves, mark it as an assumption and give the command the reader can use to confirm it in a minute. You cannot see the installed version; do not pretend you can.
- If the trace is not enough to diagnose, say so in the first sentence and name what you need.
- 'Only sometimes' and 'only in production' are strong signals: weight concurrency, time zones, environment variables, differing package versions and data volume accordingly.

## Example output

### The line that matters

`worker/queue.py:88` in `_drain()`. The three frames above are only the path there;
the `KeyError: 'retry_at'` only occurs when reading an entry that was queued without that field.

### Fix

Guard the access and make the bad case visible instead of swallowing it:

```python
due = job.get("retry_at")
if due is None:
    log.error("entry without retry_at: %r", job.get("id"))
    continue
```

That stops the crash. It does not fix why entries lack the field — see below.

### Possible causes

1. **Old entries from before the schema change** (likely). Applies if the queue was not
   drained during the upgrade. Fits "only in production" — locally the queue is usually empty.
2. **Two producers on different versions.** Applies if an older instance is still writing
   while the new one reads.

### Next step

```
redis-cli LRANGE jobs 0 5
```

If even one entry lacks `retry_at`, it is cause 1 — the real fix is a migration run. If all
have it, it is cause 2, and you are looking for a second running instance.

### What I cannot see

When the error first appeared relative to the deployment. That one fact would decide
between cause 1 and 2.
