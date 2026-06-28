---
name: test-and-fix
description: Run the test suite, read failures, patch, and re-run — looping until green or a precise blocker report. Use for any feature or module that has tests. Caps attempts so it can't run away.
---

# test-and-fix

Close the loop on tests automatically, so cheaper models can self-correct safely.

## Steps
1. Run the relevant suites (unit, integration, e2e, accessibility — whatever the project has).
2. On failure: read the actual error, form a hypothesis, patch the smallest thing, re-run.
3. Stop at **green**, OR after a fixed attempt cap (e.g. 6) → write a precise blocker report
   (the failing test, the error, what you tried) to `tasks.md` and stop.
4. Never delete or skip a failing test to "pass". Never mark done on a partial.

## Done when
All targeted suites pass, or a clear blocker report exists. Coverage on changed code meets your
project threshold.

## Token tactics
Ideal for a cheaper/mid model — the iterative loop is where expensive models waste budget. Cap the
attempts to bound cost. Pass only the failing test + the file under test, not the whole repo.
