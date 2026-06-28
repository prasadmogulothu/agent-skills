---
name: monorepo-bootstrap
description: Scaffold a Turborepo + pnpm monorepo with shared config, a testing harness, and CI in one pass. Use at the start of a new multi-package project. Adaptable to Nx / other tooling.
---

# monorepo-bootstrap

Stand up a clean multi-package project foundation so shared code is built once and reused.

## Do
1. Scaffold a Turborepo + pnpm workspace with `apps/` (deployables) and `packages/` (shared code).
2. Add a shared config package: TypeScript config, lint/format presets, and any UI/style preset.
3. Set up a testing harness (unit + e2e + accessibility) with one passing sample test.
4. Add CI (build + typecheck + lint + test on PR), build caching, and a pre-commit hook.
5. Establish the rule: apps consume shared packages by workspace name — never copy/fork shared
   code into an app.

## Adapting
The pattern holds for Nx, Lerna, or plain pnpm workspaces — what matters is: shared packages,
one config source, a test harness, and CI gating every change. Swap the tool, keep the shape.

## Done when
`build`, `test`, and `lint` pass across the empty workspace; CI is green on a trivial PR.

## Token tactics
Mechanical — route to a cheap/mid model; verify once. Record the conventions you applied to
`MEMORY.md` so later tasks don't re-explain the project.
