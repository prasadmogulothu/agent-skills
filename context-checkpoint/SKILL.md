---
name: context-checkpoint
description: Use whenever a session's context approaches its working limit OR the current atomic task completes. Cleanly checkpoint progress to a task file, commit, and end the session so a fresh one can resume — preventing quality loss from a bloated context window.
---

# context-checkpoint

Treat the context window like a budget. One task ≈ one session. Checkpoint and resume cleanly
instead of letting a session drift toward its limit mid-task. Progress lives in a shared
`tasks.md`, so any session can stop and a fresh one can pick up with no lost work.

## Thresholds
- Set a **soft ceiling** (e.g. ~70-80% of your model's context) and a **hard ceiling** you never
  cross. When you approach the soft ceiling OR finish the current atomic unit, checkpoint and end.
- If you can't read token usage directly, estimate from files read + edits + tool output, and
  checkpoint early. Early is always safe — resuming is cheap.

## Checkpoint sequence (run at end of every session)
1. Reach a safe stopping point — a complete function / file / passing test, not a half edit.
2. Run your project's done-checks for what's complete (build, typecheck, lint, relevant tests).
3. `git commit` with a message referencing the task id (`wip:` if intentionally partial).
   Uncommitted work is lost work.
4. Update `tasks.md` for the current task: set Status (`DONE` / `BLOCKED` / `IN_PROGRESS`);
   if not done, record a **Checkpoint** (what's complete) and **Resume notes** (exact next step,
   files in play, decisions made). Append a line to a Session Log.
5. Append durable learnings to a `MEMORY.md` (short bullets) so future sessions don't re-derive them.
6. End / clear the session. Do not keep working in the same context.

## Resume sequence (new session)
1. Read `tasks.md` + `MEMORY.md` and only the files the next task needs — not the whole repo.
2. Pick the first task whose dependencies are met. Set `IN_PROGRESS`, record the session id, work.
3. Checkpoint again when near the soft ceiling or the task completes.

## Splitting a too-large task
If a task won't fit one session, split it into independently completable sub-tasks in `tasks.md`,
carry over dependencies, note why, and do the first sub-task. Never carry a half-done task across a
session boundary without a written checkpoint.

## Token tactics
Near-zero model cost — this is bookkeeping the agent runs itself. Keeping sessions small is the
single biggest quality + cost win: a fresh lean context beats a bloated one every time.
