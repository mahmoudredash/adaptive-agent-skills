# Adaptive Multi-Agent Skills

A configurable, resource-aware agent system for Claude, AGY, OpenCode, and additional agents/runtimes you choose.

## Package name

`adaptive-multi-agent-orchestrator`


## Install with npx skills

Requires Node.js 22.20.0 or later (per the current Skills CLI requirements).

Install all skills to the agents detected by the CLI in the current project:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --all
```

List available skills before installing:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --list
```

Install selected skills to selected agents:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- \
  --skill adaptive-multi-agent-orchestrator \
  --skill agent-role-model-routing \
  --agent claude-code \
  --agent opencode
```

Use `--global` to install for the current user across projects:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --all --global
```

Update installed skills with `npx skills check` and `npx skills update`. See the [Skills CLI documentation](https://github.com/vercel-labs/skills/blob/main/docs/commands.md).

**Important:** The Skills CLI installs the `SKILL.md` skills; it does not automatically copy this repository's project policy file. Copy `.config/agent-model-policy.yaml` separately into the target project's `.config/` directory as described below.

## العربية

اقرأ [README بالعربية](README.ar.md) للحصول على شرح التثبيت والإعداد باللغة العربية.

## Project configuration location

The policy file is deliberately stored under a **hidden, project-local `.config/` directory**:

`<your-project>/.config/agent-model-policy.yaml`

This avoids collisions with an existing `config/` directory used by the application, framework, or build system. Do not put this agent policy in the project's normal `config/` folder.

The repository includes a ready-to-edit example at [`.config/agent-model-policy.yaml`](.config/agent-model-policy.yaml). Copy it into the root of each project where you use these skills, then customize it:

```bash
mkdir -p .config
cp /path/to/adaptive-agent-skills-/.config/agent-model-policy.yaml .config/agent-model-policy.yaml
```

If you are already at the root of a clone of this skills repository, the copy command can be:

```bash
mkdir -p .config
cp .config/agent-model-policy.yaml /path/to/your-project/.config/agent-model-policy.yaml
```

Keep the file in the project root's `.config/` folder. The skills search for this exact path relative to the active project root.

## How to edit the policy

You own the model catalog, model descriptions, agent roles, primary models, fallback order, and takeover permissions. The orchestrator may recommend options from your catalog, but must not invent model IDs or silently change your choices.

### 1. Add the models you want to use

In `.config/agent-model-policy.yaml`, find `model_catalog`. Add one item for every model you want to make available. Use the **exact model ID shown by the relevant runtime**.

Example (illustrative placeholders only; replace them with real IDs):

```yaml
model_catalog:
  - id: "YOUR_CLAUDE_MODEL_ID"
    runtime: "Claude Code"
    provider: "Anthropic"
    use_when: |
      Use for architecture, complex reasoning, planning, and difficult integration decisions.
    strengths:
      - "Architecture and long-context reasoning"
    limitations:
      - "Quota may be limited"
    avoid_when:
      - "Routine small edits that a faster coding model can handle"

  - id: "YOUR_AGY_MODEL_ID"
    runtime: "AGY"
    provider: "YOUR_PROVIDER"
    use_when: |
      Use for implementing approved page-level tasks and routine code changes.
    strengths:
      - "Focused implementation"
    limitations:
      - "Escalate cross-cutting architecture decisions"
    avoid_when:
      - "Independent review of its own changes"

  - id: "YOUR_OPENCODE_MODEL_ID"
    runtime: "OpenCode"
    provider: "YOUR_PROVIDER"
    use_when: |
      Use for independent read-only QA; allow implementation only as an explicit fallback.
    strengths:
      - "Independent review and test investigation"
    limitations:
      - "Must have the required tools for implementation takeover"
    avoid_when:
      - "Claiming independent QA on code it implemented"
```

The descriptions are your instructions to the orchestrator. Explain when you want a model used, not just what it is called.

### 2. Choose a primary model and fallback order for each agent

Edit the `agents` section:

```yaml
agents:
  claude:
    enabled: true
    default_role: technical_lead
    primary_model: "YOUR_CLAUDE_MODEL_ID"
    fallback_models:
      - "YOUR_SECOND_CHOICE_FOR_CLAUDE"

  agy:
    enabled: true
    default_role: implementer
    primary_model: "YOUR_AGY_MODEL_ID"
    fallback_models:
      - "YOUR_SECOND_CHOICE_FOR_AGY"

  opencode:
    enabled: true
    default_role: independent_qa
    primary_model: "YOUR_OPENCODE_MODEL_ID"
    fallback_models:
      - "YOUR_SECOND_CHOICE_FOR_OPENCODE"
```

Fallback models are tried in the order listed. Only use model IDs that are actually available in that agent's runtime.

### 3. Change agent roles and quota fallback

Roles are editable. For example, if AGY's quota is exhausted, you can permit OpenCode to take over implementation:

```yaml
quota_fallback:
  agy:
    on_quota_exhausted:
      eligible_agents:
        - opencode
        - claude
      require_user_approval_for_role_change: true

manual_overrides:
  allow_opencode_only_mode: true
  opencode_only_mode_roles:
    - implementer
    - investigator
    - tester
  allow_single_agent_to_self_review_as_independent: false
```

This means OpenCode may become the implementer when the configured fallback path is invoked and the required approval is granted. It **must not** then be described as an independent reviewer of its own implementation. Assign another eligible agent/model for independent QA; if none is available, report that limitation and pause at the QA/acceptance gate.

You can similarly change Claude's, AGY's, or OpenCode's allowed roles and the ordered fallback agents. Make sure the target agent has the required tools and permissions.

### 4. Validate before starting

Ask the orchestrator to:
1. Read `.config/agent-model-policy.yaml`.
2. Check that every referenced model ID exists in the intended runtime.
3. Check that each agent's primary/fallback models belong to its available model catalog.
4. Check that role assignments and takeover paths are allowed.
5. Summarize the effective routing and ask you to resolve missing or contradictory settings.

Do not start parallel implementation while required configuration is incomplete.

## What is configurable

- Agent roles and responsibilities.
- Model catalog and the intended use of every model.
- Primary model and ordered fallback models per agent.
- Whether one agent may take over another agent's role.
- What happens when provider quota/rate limits are reached.
- Adaptive concurrency based on actual machine capacity.

## Quota exhaustion is not machine overload

- **Provider quota/rate limit/model unavailable:** follow the configured model fallback, then the ordered agent takeover policy.
- **Machine resource pressure:** reduce concurrency or queue work. Do not assume changing model solves machine pressure.

Before any takeover, save task state, decisions, changed files, test results, and next steps. Transfer file ownership explicitly; never allow concurrent writes to the same files. Preserve scope and acceptance criteria.

## Skills included

- `adaptive-multi-agent-orchestrator`
- `agent-role-model-routing`
- `agy-implementation-workflow`
- `opencode-independent-qa`
- `project-safety-acceptance`

Skill directory names use lowercase kebab-case, and each skill entry point is named `SKILL.md`.

## Safety

The skills do not authorize destructive Git operations, staging/committing, APK builds, unrelated changes, test weakening, or edits outside the approved scope. Obtain explicit user authorization where required. Shared workspace is the default; worktrees/clones require your explicit request.
