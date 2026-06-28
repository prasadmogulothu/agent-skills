---
name: rls-security
description: Generate row-level-security policies plus a cross-user isolation test, so users can only access their own rows. Written for Postgres/Supabase; adaptable to other databases. Use for any per-user or multi-tenant data.
---

# rls-security

Make per-user data isolation enforced at the database layer, not just in app code — and prove it.

## Do
Given a table spec:
1. Create the schema with an owner column (e.g. `user_id`).
2. Enable Row-Level Security and add policies so each authenticated user can only `select`,
   `insert`, `update`, `delete` their own rows. Key the policy off the authenticated user id.
3. Provide typed queries/helpers that always run under the user's identity.
4. Write and run a **cross-user isolation test**: as user A, attempt to read and write user B's
   rows — both must fail. Do not mark done until this test passes.

## Adapting to other databases
The pattern is universal: an owner column + a policy/middleware that scopes every query to the
authenticated principal + a test that proves cross-user access is denied. Swap the RLS syntax for
your DB's equivalent (policies, query scoping, or an ORM guard).

## Done when
The isolation test passes and the security-review skill returns PASS for this table.

## Token tactics
Pass ONLY the table spec, not the repo. Reuse the cached instructions across tables. Route to a
frontier model — a subtle isolation bug is a data breach.
