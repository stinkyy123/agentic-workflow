---
name: loop-design
description: >
  The standard for building agent verification loops in the MakeMyWorkflow
  system. Use when designing any task where an agent should iterate and check
  its own work instead of one-shotting — e.g. building + QA, scoring cold email
  drafts, anything with a real done-check. Defines the stop condition, the hard
  cap, when to use a separate scorer, and what to log. Pull this in before
  writing any "iterate until X" task.
---

# Loop Design Standard

A loop is three things: a **trigger**, an **action**, and a **stop condition**.
The whole game is the stop condition. A loop is only as good as its done-check —
good verification on a bad goal just reaches a bad result faster.

## Before building any loop, answer two questions
1. **What does "done" mean?** Get as objective as possible. Best: "iterate until
   metric X ≥ Y." Acceptable fallback: a rubric score above a threshold. Avoid:
   "until satisfied" — that stops arbitrarily or runs forever.
2. **How will it check?** The agent needs the right tool for the check:
   - Code/script → run it, check it passes / output is correct
   - Functional behavior → run the test scenarios, check pass/fail
   - Visual → screenshot and compare against a reference
   - Writing/tone → score against a rubric (see "separate scorer" below)

## Mandatory parts of every loop
- **Checkable goal** — a stop condition a machine can actually evaluate.
- **Hard cap** — a max number of passes (e.g. "stop after 8 attempts even if not
  met"). Non-negotiable. Without it, a hard goal + hard done-check = a loop that
  runs for hours/days and burns tokens for nothing.
- **Logging** — log each iteration: what it tried, what the check returned, what
  it changed. This is how Albert sees what happened and how you debug a bad loop.
- **Plan first** — reason/plan before the first action, not blind iteration.

## Objective vs. subjective stop conditions
**Objective** (loop directly): test scenarios pass, code runs, output matches a
known value, count/threshold met. Example: the Build/QA loop — "all 10 QA
scenarios pass" is a real stop condition.

**Subjective** (do NOT loop on "until satisfied"): "is this email good?", "is
this copy compelling?" For these, build a **separate scorer**:
- A dedicated scoring agent or scoring skill with an explicit rubric.
- Each rubric item must be checkable (e.g. cold email: single clear CTA present?
  under N words? personalization present? no spam-trigger phrases? subject under
  M chars?).
- The loop iterates until the rubric score ≥ threshold OR the hard cap hits.
- Keep maker and checker separate so the maker can't grade its own homework.

## When NOT to build a loop
Most tasks don't need one. Skip the loop when:
- It's a one-pass task you'll review yourself anyway (most research, most docs).
- The done-check is just "Albert reads it and decides" — that's a human gate,
  not a loop.
- Forcing iteration would cost more tokens than it saves judgment.
Add loops only where a machine can check its own work better than Albert can be
bothered to. For this system that's mainly: Build/QA (objective) and scored
outputs like Outreach drafts (subjective, with a scorer).

## Cost discipline
Loops can run long. Match the loop to the payoff. A 30-min build/QA loop is
worth it; a 12-hour loop chasing an impossible done-check is not. Albert is a
solo founder doing knowledge work + outreach — event/cadence-triggered loops,
not 24/7 perpetual swarms. Don't build toward the fleet-of-agents demo.

## Human gates still apply inside loops
A loop that produces a send-able artifact (cold email, invoice) STOPS at the
gate and hands the finished artifact to Albert. The loop verifies quality; it
does not grant permission to send, call, or move money.
