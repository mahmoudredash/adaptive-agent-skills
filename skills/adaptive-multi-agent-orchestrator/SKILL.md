---
name: adaptive-multi-agent-orchestrator
description: Orchestrate multiple coding agents with adaptive concurrency, user-controlled model selection, configurable roles, quota-aware fallback, and safe task handoffs.
---

# Adaptive Multi-Agent Orchestrator

## First action
Locate and read the project configuration at `.config/agent-model-policy.yaml`. If it does not exist, use the template only as a schema and ask the user to complete it. Never invent available agents, model IDs, quota details, or runtime capabilities.

## User owns model policy
The user supplies:
- The models they want to use.
- Exact model IDs as exposed by each runtime.
- When each model should be used.
- Strengths, limitations, and exclusions.
- Primary model and ordered fallback preferences.

You may analyze the task and recommend a model from the supplied catalog. You must explain the reason using the user's `use_when` guidance. Do not silently replace the user's selected primary model, add an unlisted model, or reorder the fallback chain. Ask before changing persistent preferences.

## Configurable roles
Roles are assignments, not permanent identities. The user may change an agent's role or allow an agent to take over another role. Respect `allowed_roles`, `may_take_over_roles`, and explicit manual overrides.

Default role suggestions (editable):
- Claude: technical lead, coordinator, architecture and integration accountability.
- AGY: primary implementation agent.
- OpenCode: independent QA, review and testing.

These are defaults, not hard-coded restrictions. Do not assume that OpenCode can only test or that Claude/AGY must always retain their usual roles.

## Adaptive task scheduling
Estimate resource demand per task and agent using available evidence: RAM, memory pressure, CPU, disk space, active builds/tests, and task complexity. Start only as many tasks as can run safely with a conservative reserve. Queue the rest. Reassess when a task completes or machine conditions change. Never kill unrelated or running agents automatically.

Use the shared workspace by default. Worktrees, clones, containers, or equivalent isolation require an explicit user request. Assign file ownership before parallel edits; one write-capable agent per file at a time.

## Quota-aware fallback
Distinguish:
1. Model/provider quota exhausted, rate limited, or model unavailable.
2. Machine capacity insufficient.
3. Tool/runtime failure unrelated to quota.

For provider quota exhaustion:
1. Save a checkpoint: task goal, current status, decisions, files touched, tests run/results, blockers, and exact next steps.
2. Try the same agent's next configured fallback model, if available and permitted.
3. If its fallback chain is exhausted, consult `quota_fallback` and `manual_overrides`.
4. If configured, transfer the role/task to an eligible agent such as OpenCode-only implementation mode.
5. Obtain user approval when configuration requires it.
6. Transfer file ownership explicitly and ensure the former agent is no longer writing those files.
7. Preserve the original scope and acceptance criteria.
8. Report which agent/model changed and why.

Never assume a quota can be bypassed by changing model. Never claim a fallback is available until the runtime confirms it.

## Single-agent / OpenCode-only mode
If Claude and/or AGY are unavailable, the user may authorize OpenCode to perform implementation or coordination roles if its configured capabilities permit. In this mode:
- OpenCode follows the same implementation, test, safety, and evidence requirements.
- Do not call OpenCode an independent reviewer of code it authored.
- Assign independent QA to a different eligible agent/model, or explicitly report that independent QA is unavailable and pause before acceptance.
- If no independent reviewer exists, offer a clearly labeled self-check only; it is not equivalent to independent QA.

## Completion gates
Each task must pass implementation checks and independent QA where available, then be presented for user acceptance. Do not start dependent work or the next page before acceptance when the project workflow requires it.
