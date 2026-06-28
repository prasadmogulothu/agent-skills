# agent-skills

**Reusable, token-optimized skills for autonomous coding agents — work with any agent, any model.**

A *skill* here is a small, structured Markdown file that teaches a coding agent how to do one
job well: manage its own context window, pause for human review, audit security, control LLM
cost, and more. They're framework-agnostic — use them with Hermes, Claude Code, Cursor, OpenCode,
Aider, Cline, or by pasting them into any LLM chat.

These encode patterns that are easy to get wrong and rarely shipped as drop-in artifacts:
checkpoint-and-resume context management, human-in-the-loop review gates, and a server-side
LLM cost proxy, alongside solid security and compliance recipes.

> No product logic, no secrets — just the engineering patterns. MIT licensed; use freely.

---

## The skills

| Skill | What it does | Use it when |
|---|---|---|
| **context-checkpoint** | Treat the context window like a budget: finish the current task, checkpoint progress to a task file, then clear and resume in a fresh, lean session — *before* the window fills. | Long, multi-step autonomous runs that would otherwise degrade as context bloats. |
| **review-gate** | Pause at defined checkpoints, post a short summary + an artifact link (diff / preview / report), and wait for explicit human approval. Never proceeds on silence; loops on change requests. | You want oversight on risky or high-value steps without babysitting every line. |
| **test-and-fix** | Run the test suite, read failures, patch, and re-run — looping until green or a precise blocker report (capped attempts so it can't run away). | Any feature with tests; lets cheaper models self-correct safely. |
| **security-review** | A read-only audit (ships with a reviewer-agent definition) of auth, data isolation, secret handling, payments, and input validation. Reports severity; blocks on critical/high. | Before merging or deploying anything sensitive. |
| **ai-proxy-guard** | Build a server-side LLM proxy with response caching, rate-limiting, context caps, and per-call cost logging — keeps API keys off the client. | Any app that calls an LLM and needs predictable cost + key safety. |
| **rls-security** | Generate row-level-security policies plus a cross-user isolation test, so users can only access their own rows (Postgres/Supabase; adaptable to other DBs). | Any multi-tenant or per-user data store. |
| **consent-compliance** | Granular, logged, withdrawable consent + data export & hard-delete (DSAR), for GDPR/DPDP-style compliance. | Any app collecting personal data. |
| **monorepo-bootstrap** | Scaffold a Turborepo + pnpm monorepo with shared config, a testing harness, and CI in one pass. | Starting a new multi-package project. |

Each skill is **token-optimized**: tightly scoped, capped in turns, cache-friendly, and routed to
the cheapest capable model (see *Model routing* below).

---

## What a skill looks like

Every skill is a `SKILL.md` with simple front-matter and a body:

```markdown
---
name: review-gate
description: Pause for human approval at a checkpoint; present the artifact and wait. Use on risky or high-value steps.
---

# review-gate

## Do
... step-by-step instructions the agent follows ...

## Token tactics
... how to keep this cheap ...

## Done when
... the success / stop condition ...
```

That's it — plain Markdown. Any agent that can read a file or accept instructions can run it.
The format is compatible with the [agentskills.io](https://agentskills.io) open standard.

---

## How to use them — with any agent

Pick whichever matches your setup. The idea is the same everywhere: **make the skill's
instructions part of the agent's context, then ask it to apply the skill.**

### Hermes Agent (native skills)
```bash
git clone https://github.com/prasadmogulothu/agent-skills.git
# point Hermes at the skills folder, then:
#   "Load the skills in ./agent-skills and apply review-gate after each risky task."
```
Hermes loads them as first-class skills and can self-improve them over time.

### Claude Code
Drop a skill into your project rules or reference it directly:
```bash
# option A: reference in CLAUDE.md so it's always in context
echo "Follow ./agent-skills/context-checkpoint/SKILL.md for session management." >> CLAUDE.md

# option B: invoke for a single task (print mode)
claude -p "Apply ./agent-skills/rls-security/SKILL.md to the users table." \
  --allowedTools 'Read,Edit,Bash'
```
The included `security-reviewer.md` agent can be copied into `.claude/agents/`.

### Cursor
Add the skill as a rule:
```bash
mkdir -p .cursor/rules
cp agent-skills/review-gate/SKILL.md .cursor/rules/review-gate.md
```
Then reference it in chat: *"Apply the review-gate rule before deploying."*

### OpenCode / Aider / Cline / others
Paste or reference the skill file in your prompt:
```bash
opencode run "$(cat agent-skills/test-and-fix/SKILL.md)  Now run it on the auth module."
```

### Any LLM chat (no special tooling)
Copy the contents of a `SKILL.md` into the chat as instructions, then give it the task.
The skills are written to be self-contained.

---

## Model routing (why these stay cheap)

The skills assume a simple, cost-saving split. Route most work to cheap models; reserve a
frontier model for the few risky tasks.

| Tier | Use for | Skills |
|---|---|---|
| **Frontier** (e.g. Claude Opus) | Security, data isolation, payments, anything where a subtle bug is costly | `rls-security`, `consent-compliance`, `security-review`, `ai-proxy-guard` |
| **Mid** (any capable mid model) | Standard build + iteration | `test-and-fix`, `monorepo-bootstrap` |
| **Orchestration** (near-zero model cost) | Control flow, runs in the agent itself | `context-checkpoint`, `review-gate` |

Tip: validate any cheaper model on one representative task before trusting it with a tier.

---

## Repo structure

```
agent-skills/
  context-checkpoint/SKILL.md
  review-gate/SKILL.md
  test-and-fix/SKILL.md
  security-review/SKILL.md        (+ security-reviewer agent definition)
  ai-proxy-guard/SKILL.md
  rls-security/SKILL.md
  consent-compliance/SKILL.md
  monorepo-bootstrap/SKILL.md
  LICENSE
  README.md
```

---

## Adapting a skill

These ship with sensible defaults (Postgres/Supabase, Turborepo, Vercel-style deploys). To adapt:
swap the stack-specific nouns for your own, keep the **Do / Token tactics / Done when** structure,
and keep the success condition concrete (a passing test, a green build, an explicit approval).
The structure is the reusable part.

---

## Contributing

Issues and PRs welcome — especially new skills that follow the same format, and adaptations for
other stacks or agents. Keep skills single-purpose, token-conscious, and ending on a clear,
verifiable stop condition.

## License

MIT — see [LICENSE](LICENSE). Use them in commercial and personal projects freely.

---

*Built from a real autonomous, multi-model build pipeline (Hermes orchestrating Claude Code +
OpenCode). The product specifics are stripped out — what's left are the patterns worth reusing.*
