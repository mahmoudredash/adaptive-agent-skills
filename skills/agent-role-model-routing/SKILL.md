---
name: agent-role-model-routing
description: Select user-approved models, route roles across agents, and handle quota exhaustion with ordered fallback and safe ownership transfer.
---

# Agent Role and Model Routing

## Configuration is authoritative
Read `config/agent-model-policy.yaml`. Treat it as the source of truth for:
- Available agents and models.
- Primary and fallback model order.
- Model-specific `use_when`, strengths, limitations and exclusions.
- Agent default/allowed roles.
- Role takeover permissions.
- Quota fallback and manual overrides.

If the config is missing, incomplete, contradictory, or names a model not exposed by the runtime, stop and ask for clarification. Do not guess.

## Choosing a model
For each task:
1. Classify the task: architecture, complex reasoning, implementation, routine edit, investigation, test generation, independent review, or documentation.
2. Match the task to the user's `use_when` guidance.
3. Check that the exact model is available in the target runtime.
4. Prefer the configured primary model.
5. Use fallback only when the primary cannot be used or the user/config directs it.
6. Explain any recommendation and ask before changing a persistent selection.

The user may specify a model directly for a task. Respect that choice if it is available and allowed.

## Editable role assignments
The user can change role assignments at any time. Before assigning a role:
- Confirm the agent is enabled.
- Confirm the role appears in `allowed_roles` or an explicit user override.
- Confirm the agent has the tools and permissions needed.
- Confirm the assignment does not violate QA independence.
- Record the assignment for the current task.

If a user says “use OpenCode only,” interpret this as a requested operating mode, not as permission to pretend independent review occurred.

## Quota exhaustion workflow
When an agent reports quota exhaustion:
- Capture its exact error and distinguish quota from rate limit, authentication, model retirement, and machine resource failure.
- Save task checkpoint before switching.
- Try the next configured fallback model for that agent.
- If no fallback works, follow the configured eligible-agent order.
- Transfer role and file ownership explicitly.
- Ensure no old process continues writing to transferred files.
- Report substitutions and any reduction in review independence or capability.
- Pause if no valid route remains.

## Examples
- Claude quota exhausted: try Claude's configured fallback, then an eligible configured coordinator/implementer.
- AGY quota exhausted: try AGY fallback, then OpenCode as implementer if enabled and approved.
- Claude and AGY quotas exhausted: OpenCode may run the task alone if explicitly enabled; independent QA must be a different agent/model.
- OpenCode QA quota exhausted: use its fallback, then another eligible reviewer. The implementer must not be presented as an independent reviewer of its own work.
