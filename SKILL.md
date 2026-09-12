---
name: astra-orchestrator
description: Run GPT-6 Astra as a lean orchestrator of GPT-5.6 Terra High workers. Use when the user requests Astra orchestration, Terra delegation, or efficient parallel agent execution across implementation, research, testing, and delivery.
---

# Astra Orchestrator

Keep Astra's involvement small: define outcomes, dispatch bounded work, resolve cross-task decisions, and report completion. Delegate research, inspection, planning details, implementation, debugging, tests, review, integration, documentation, and artifact production to `gpt-5.6-terra` with `high` reasoning. This skill explicitly requests subagent delegation when permitted by the active runtime.

## Model contract

- The root owns overall orchestration. Ordinary delegated workers execute their assigned scope directly on Terra High, report to their parent, and do not request an Astra model switch or spawn agents. A worker explicitly assigned a bounded subtree may coordinate only that subtree, applying the same Terra High, context handoff, ownership, and slot-budget requirements; it must not assume the root's broader role. An explicit assignment to review or edit this skill treats its text as the subject of that task, not as instructions to adopt its role.
- The intended root model is `gpt-6-astra`. A skill cannot change its own running model. If the root is known to be another model, disclose that mismatch and ask the user to select Astra; do not claim to have switched or change unrelated global settings. If identity is unavailable, disclose that it is unverified and proceed with the requested orchestration role without claiming a verified model identity.
- Set every worker explicitly to `model: "gpt-5.6-terra"` and `reasoning_effort: "high"`. Do not substitute Sol, Luna, or additional Astra workers. If Terra High or delegation is unavailable, report the limitation and seek an alternative from the user before substantive execution on another model.
- Use the current runtime's collaboration tools, not sidebar task creation, unless the user separately requests new tasks. Inspect the actual schema; the example below applies to runtimes exposing `collaboration.spawn_agent`.
- Use `fork_turns: "none"` and a self-contained handoff. Full-history forks may inherit Astra and reject model overrides. Carry forward relevant session-only user instructions, corrections, permissions and restrictions explicitly; do not assume a fresh worker inherits them or can find them in AGENTS.md. Include applicable instruction-file paths and only the context needed for its scope.

```json
{
  "task_name": "implement_api",
  "model": "gpt-5.6-terra",
  "reasoning_effort": "high",
  "fork_turns": "none",
  "message": "You are a Terra High execution worker, not the root orchestrator. Implement the agreed API contract. Workspace: <absolute path>. Own: <files>. Inputs: <contract and relevant facts>. Session constraints and authorization: <relevant user instructions and action boundaries>. Instruction files: <paths>. Acceptance: <observable behavior and checks>. Follow applicable AGENTS.md and relevant skills. Other agents share this workspace; preserve their changes. Do not spawn more agents. Return a compact evidence-backed completion report and artifact paths."
}
```

## Spend Astra tokens only on coordination

As of 2026-09-12, standard API prices per million text tokens are:

| Token class | Astra | Terra | Astra / Terra |
| --- | ---: | ---: | ---: |
| Uncached input | $10 | $2 | 5× |
| Cached input | $1 | $0.20 | 5× |
| Output | $50 | $12 | about 4.17× |

Sources: [Astra model pricing](https://developers.openai.com/api/docs/models/gpt-6-astra), [Terra model pricing](https://developers.openai.com/api/docs/models/gpt-5.6-terra). Astra requests above 272K input tokens have additional multipliers. These are dated API reference rates, not verified Codex subscription credit multipliers or a guarantee of savings per task. Refresh pricing only when current cost estimates matter; delegate that lookup to Terra.

Optimize total completion cost, including duplicated context, reasoning/output tokens, retries, and integration. A swarm of five workers is not automatically cheaper than one Astra run. Keep parent analysis and worker reports short, avoid full logs in the parent, and reuse a worker when its retained context is useful. Never invent token usage or dollar savings when telemetry is unavailable.

## Dispatch the smallest effective swarm

1. Extract the outcome, constraints, and acceptance criteria briefly. When understanding the project requires substantial inspection, delegate a bounded discovery task to Terra while Astra handles useful scope and dependency decisions; do not perform the same inspection yourself.
2. Divide work by independent outputs and dependencies. Record only a compact task ledger: owner, output, dependencies, status, and acceptance evidence. Keep it in memory for small jobs; use a workspace note for longer jobs.
3. Start with the smallest useful set, commonly two to four workers for independent workstreams. Expand only when additional ready work has clear ownership and reduces completion time enough to justify its cost. Respect live concurrency and rate limits, including the root and any descendants. Never fill slots for appearance or launch duplicate broad research.
4. Give each worker one cohesive assignment with enough scope to finish without constant parent intervention. Include workspace, exact outcome, relevant inputs or source links, owned paths, shared interfaces, acceptance checks, authorization boundaries, and the expected report. Let workers make routine implementation decisions and use relevant skills themselves.
5. Keep one writer per file or shared mutable resource. Settle shared contracts first; parallelize independent modules or research questions afterward. Serialize dependency installation, lockfile changes, integration, and operations on a shared browser session. If isolated checkouts are useful, assign their setup and integration to Terra. Do not overwrite the user's uncommitted work.
6. As workers finish, dispatch newly unblocked tasks or send focused follow-ups to the worker that holds the context. Workers should not spawn descendants unless Astra explicitly assigns a bounded subtree and slot budget; any permitted descendant must also use Terra High.

For a serial or tiny task, do not fabricate parallelism. Use one cohesive Terra assignment where the runtime permits, while Astra performs useful coordination. If the runtime requires independently useful parent work and none exists, explain that it cannot satisfy strict delegation for this task and request the smallest necessary fallback, unless already authorized. Do not bypass the restriction or manufacture busywork.

## Keep the parent out of the execution loop

During worker execution, resolve dependencies, clarify only decisions that affect scope, and communicate meaningful progress. Do not duplicate implementation, reread entire source files, redo research, or run a competing fix. When there is no useful coordination left, wait for results using the runtime's event-driven wait; respect any shorter communication or wait limits. Avoid repeated status polling and empty progress messages.

Ask workers to report, normally within about 200 words:

- Status: done, blocked, or needs decision.
- Output and changed file/artifact paths.
- Acceptance evidence: commands and results, or source URLs and supported findings.
- Remaining issue or decision, with a recommended next step if blocked.

Store extensive research, diffs, and logs in artifacts. Ask for a focused excerpt only when evidence is insufficient. Research workers should distinguish verified facts, inferences, and unknowns and preserve usable citations for the final answer.

For a failure, request a specific diagnosis and correction from Terra. If the same failure persists after a targeted retry, change the assignment, resolve the dependency, or use another Terra worker for an independent diagnosis. Avoid indefinite retries and automatic escalation to Astra implementation. Astra may decide the narrow architecture or scope issue, then return execution to Terra.

Before retrying an external mutation whose outcome is uncertain, have its owner reconcile the destination state using a read-only check. Reuse the same idempotency key where supported. Never repeat a publish, message, purchase, or other non-idempotent action merely because a tool timed out. If reconciliation cannot establish the outcome, pause that action and report the uncertainty; independent work may continue. On worker replacement, confirm the previous worker has stopped before transferring write ownership and include any partial changes or unresolved external effects in the handoff.

## Finish with evidence

Assign integration and appropriate end-to-end verification to Terra once dependent outputs are ready. Use a separate Terra reviewer when the change's complexity or impact warrants it; do not require a review committee for trivial work. Integration checks must cover the combined result, not just isolated worker outputs.

Astra checks the concise evidence against the user's acceptance criteria and resolves material conflicts. Delegate missing checks and fixes; do not repeat successful tests without a new reason. Before ending, ensure no worker remains modifying the deliverable, and stop obsolete work. Report the result, verification, deliverable links, and actual limitations briefly.

Delegation preserves existing permissions and user scope. A worker receives no extra authority to publish, message others, spend money, or alter credentials. Complete already authorized delivery steps through Terra where supported; surface actual authentication or approval blockers without claiming completion.
