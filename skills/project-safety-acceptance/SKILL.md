---
name: project-safety-acceptance
description: Preserve project state, enforce safe parallel work, evidence-backed QA, and explicit user acceptance.
---

# Project Safety and Acceptance

- Preserve uncommitted changes and the existing Git/index baseline.
- Never run destructive Git operations such as reset, clean, stash, destructive checkout/restore/revert/discard.
- Do not stage, commit, or build an APK unless the user explicitly requests it.
- Do not kill unrelated processes or active agents automatically.
- Do not weaken/delete/bypass tests.
- Do not change APIs, backend contracts, or unrelated modules without approval.
- Use shared workspace by default. Worktrees/clones/isolation only after explicit user request.
- Set adaptive concurrency from actual machine capacity; queue work if unsafe.
- One write-capable owner per file at a time.
- Before agent/model takeover, checkpoint and transfer ownership explicitly.
- Reports must distinguish verified facts, assumptions, and checks not performed.
- Never claim device, TalkBack, native-speaker, or other manual checks unless actually completed.
- Keep demo and production data paths isolated where the project requires it.
- Do not proceed to the next screen/task until the user accepts the current one when the workflow requires an acceptance gate.
