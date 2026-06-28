---
name: review-gate
description: Use when a task is marked as needing human review. Pause, present the artifact to a human (summary + link), and wait for explicit approval before continuing. Loop on change requests; never proceed on silence.
---

# review-gate

Human-in-the-loop approval as a reusable step. Mark certain tasks as gated; at those, a human
must approve before dependent work continues.

## Steps
1. Confirm the task passes your done-checks.
2. Set the task `Status: BLOCKED_REVIEW` in `tasks.md` and add it to a Review Queue.
3. Notify the human (chat / messaging gateway / PR comment) with:
   - **What** — a 3-bullet summary of what was built or changed.
   - **Where** — a clickable artifact: a git diff/PR, a preview URL, a component catalogue, or a report.
   - **Ask** — "Approve, or request changes?"
4. **Wait.** Do not start any task that depends on this one. You may pick up an independent,
   non-dependent task meanwhile to keep momentum.
5. **Never proceed on silence.** Only an explicit approval advances the gate.

## Change loop
On a change request: set `Status: CHANGES_REQUESTED`, record the request, apply the fix with the
same-tier model, re-run done-checks, re-present, and ask again. Loop until approved, then set
`DONE` and clear it from the Review Queue.

## Recommended always-gated work
Anything risky or irreversible: data-model/security changes, auth, payments, data deletion, and
public deploys — gate these even when automated checks pass.

## Token tactics
Near-zero model cost — runs in the agent's control flow. Because it pauses the workers while a
human reviews, models sit idle (no spend) until approval. Oversight that also saves tokens.
