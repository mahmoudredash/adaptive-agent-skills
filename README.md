# Adaptive Multi-Agent Skills

A configurable, resource-aware agent system for Claude, AGY, OpenCode, and any additional agent/runtime you add.

## Package name
`adaptive-multi-agent-orchestrator`

## What is configurable
- Agent roles and responsibilities.
- The models available to each agent.
- Primary model and ordered fallback models.
- Your explanation of **when** to use each model and what it is good at.
- Whether an agent may take over another agent's role.
- What happens when a provider quota, rate limit, or model availability limit is reached.
- Concurrency and resource limits.

## Important operating principle
You provide the model catalog and explain your intended use for each model. The orchestrator may recommend a model from that catalog, but it must not invent model IDs, silently add models, or override your explicit choices. You can edit the configuration at any time.

## Suggested folder layout
Copy the `skills/` directory into the skill discovery location supported by your agent runtime. Keep `config/agent-model-policy.yaml` in your project repository. Exact installation/discovery paths differ by runtime; this ZIP does not install skills automatically.

```text
project/
├── config/
│   └── agent-model-policy.yaml
└── skills/
    ├── adaptive-multi-agent-orchestrator/
    │   └── SKILL.md
    ├── agent-role-model-routing/
    │   └── SKILL.md
    ├── agy-implementation-workflow/
    │   └── SKILL.md
    ├── opencode-independent-qa/
    │   └── SKILL.md
    └── project-safety-acceptance/
        └── SKILL.md
```

## Setup
1. Copy `config/agent-model-policy.template.yaml` to your project as `config/agent-model-policy.yaml`.
2. Fill in the real model IDs shown by your installed runtimes.
3. For each model, write your own `use_when` explanation: when to choose it, strengths, limitations, and when not to use it.
4. Set each agent's primary model and ordered fallbacks.
5. Set `role_overrides` and `quota_fallback` to define whether another agent may take over implementation, review, coordination, or testing.
6. Ask the orchestrator to validate the config before starting work.

## Quota exhaustion and role takeover
Quota exhaustion is not the same as machine overload:
- **Provider quota/rate limit/model unavailable:** follow that agent's ordered fallback chain.
- **Machine resource pressure:** reduce concurrency or queue work; do not switch models unless the chosen model actually changes resource use and the config permits it.

If Claude or AGY reaches its quota, you can configure OpenCode (or another agent) to take over implementation. The orchestrator must:
1. Save the task state, decisions, changed-file list, tests, and remaining work.
2. Check that the replacement agent has the required tools and model.
3. Explicitly transfer file ownership; never allow concurrent writes to the same files.
4. Preserve the original task scope and acceptance criteria.
5. If the replacement is also the implementer, assign independent QA to a different agent/model. OpenCode cannot be described as an independent reviewer of its own implementation.
6. If no eligible agent/model remains, pause safely and report the blocker.

## Naming convention
Use lowercase kebab-case for skill directory names. Every skill entry file is exactly `SKILL.md`. Keep the package/umbrella skill name stable: `adaptive-multi-agent-orchestrator`.

## Safety
The skills do not authorize destructive Git operations, staging/committing, APK builds, unrelated changes, test weakening, or edits outside the approved scope. Obtain explicit user authorization where required.
