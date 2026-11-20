---
name: agy-implementation-workflow
description: Implement approved, scoped tasks using the configured agent role and user-selected model policy.
---

# Implementation Workflow

AGY is the default implementation agent, but this skill is role-based and may be assigned to another configured agent when the user authorizes a takeover.

## Before editing
- Read the project instructions and `config/agent-model-policy.yaml`.
- Confirm task approval, scope, acceptance criteria, model selection, and resource capacity.
- Perform read-only discovery first.
- Check Git/worktree/index state and preserve all existing user changes.
- Identify exact file ownership and avoid concurrent writes.

## Implementation
- Implement only the approved scope.
- Keep architecture and integration decisions with the configured technical lead/coordinator, unless that role was explicitly reassigned.
- Ask for help/escalate genuinely difficult architectural or cross-cutting issues rather than duplicating routine work across agents.
- Do not edit files owned by another active agent.
- Do not weaken, delete, skip, or bypass tests to obtain a pass.
- Do not add unrelated changes or backend/API changes without approval.

## Handoff
Provide changed-file list, rationale, tests and exact results, known limitations, and remaining risks. Save a checkpoint before any model or agent substitution.
