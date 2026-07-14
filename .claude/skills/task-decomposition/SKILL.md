---
name: task-decomposition
description: >
  The decomposition and routing logic for the pipeline-manager orchestrator.
  Use when breaking a multi-step client task into sub-tasks and assigning each
  to the right specialist agent. Defines which agent owns which kind of work,
  dependency ordering, and when NOT to delegate.
---

# Task Decomposition & Routing

## Routing table — match work to agent
| If the sub-task is about... | Route to | Notes |
|---|---|---|
| finding/scoring/cleaning leads | prospecting-agent | read/write lead list |
| drafting cold email or logging dial stats | outreach-agent | DRAFT only, gate the send |
| pre-call research on a prospect | discovery-agent | their site + GBP |
| building the scope / ROI math / pricing | discovery-agent | never quote before scoped |
| actually building the receptionist | build-agent | gated: config → render → deploy → QA |
| running QA test scenarios | build-agent | the 10-scenario QA loop (inside the build) |
| handover docs / backups / signoff | handover-agent | writes to Drive |
| monitoring / monthly stats | retainer-agent | read + alert |
| "is this in scope?" | retainer-agent (scope-guard) | run FIRST on expansion requests |

## Dependency rules
Common chains (run in this order):
- New lead → close: prospecting → discovery → outreach(draft) → [Albert sends]
- Won deal → live: discovery(scope) → build-agent → handover
- Independent / parallelizable: lead scoring of separate batches, research on
  multiple prospects, QA scenarios that don't depend on each other.

If two sub-tasks are independent, run them in parallel. If B consumes A's
output, serialize.

## When NOT to delegate (keep it in the main session)
- A quick one-off question — just answer it, don't spin up an agent.
- A task that's a single specialist's job — call that agent directly, you don't
  need the orchestrator overhead.
- Anything that needs the full conversation context — sub-agents wake up blind.

## Decomposition checklist (run every time)
1. Restate the task as numbered sub-tasks.
2. Map each to ONE agent. Unmapped sub-task → flag, don't improvise.
3. Mark dependencies (which waits on which).
4. Mark gates (send / call / money / permissions / delete) — these stop and
   wait for Albert.
5. Write each agent a brief: goal + inputs + required output format.
6. After results return, integrate — don't just concatenate.

## Anti-patterns to avoid
- Spawning many agents for a task one could handle (token waste).
- Passing thin context down then blaming the agent for a thin result.
- Treating your coordinator role as permission to send/act. It isn't.
- Letting a dynamic-workflow-style swarm spin up dozens of agents unprompted —
  if a task looks like it needs that many, stop and confirm with Albert first.
