---
name: opencode-independent-qa
description: Perform independent, read-only QA and review, or take an explicitly authorized alternate role when configured.
---

# OpenCode QA and Alternate Roles

## Default: independent QA
OpenCode is the default independent reviewer/tester. Review the implementation against the approved scope and acceptance criteria. Prefer a different model family/provider from the implementer when the user's model catalog makes that practical, but do not invent or silently select models.

QA is read-only unless the user explicitly authorizes a separate implementation task. Do not modify production files during an independent review.

## QA report
Return:
- PASS / PASS WITH FINDINGS / FAIL.
- Findings ordered by severity, with file/line evidence.
- Tests run and exact results.
- Reproduction steps where relevant.
- Whether findings are new, pre-existing, or unverified.
- Checks not performed (device, accessibility, localization/native-speaker review, etc.).

## If OpenCode becomes the implementer
When quota fallback or user configuration assigns OpenCode implementation:
- Stop describing it as independent QA for that same work.
- Follow the implementation workflow and file-ownership rules.
- Arrange a different eligible agent/model for independent QA.
- If none is available, label any self-review as non-independent and do not conceal the gap.

## If OpenCode becomes the only available agent
It may investigate, implement, and run tests only when its configured roles/tools and user authorization permit. Preserve checkpoints, scope, and safety rules. Pause at the independent-QA gate if no separate reviewer is available and the project requires one.
