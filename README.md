# MakeMyWorkflow — Agent Team

Claude Code agents mapped onto the Client Lifecycle Playbook.

## How this works
- **Agents** live in `.claude/agents/` as markdown files. Each has YAML front
  matter (name, description, tools, model) and a body (instructions).
- **Skills** live in `.claude/skills/` as markdown files. Any agent can use any
  skill via progressive disclosure — Claude reads the skill's `description`,
  decides if it's relevant, and pulls it in only if so.
- The `description` field is the trigger. If an agent isn't firing when you
  expect, tighten its description. If it fires when you don't want it, narrow it.

## Adding a skill from a YouTube video (your use case)
1. Watch the video, distill the method into steps.
2. Create `.claude/skills/your-skill-name.md`.
3. Write YAML front matter: `name` + a precise `description` saying when to use it.
4. Write the body: the actual method as numbered steps / templates.
5. Save it. The relevant agent picks it up automatically next session.

No agent rebuild needed. You can stack multiple skills per topic
(`cold-email-v1.md`, `cold-email-followup.md`) and refine them over time.

## Current skills
- `icp-scoring` — rubric for scoring trades leads 1-5 (prospecting).
- `task-decomposition` — routing logic for the orchestrator.
- `loop-design` — standard for building verification loops: checkable goal,
  hard cap, separate scorer for subjective outputs, logging. Pull this in
  before writing any "iterate until X" task.

## Build order (do NOT build all at once)
Build one agent end-to-end, prove it on real work, then add the next.

1. ✅ Prospecting Agent — built (lowest risk, read-heavy, no send-gate)
2. ⬜ Outreach Agent — drafts cold email / logs dial stats. SEND GATE: drafts only.
3. ⬜ Discovery/Close Agent — pre-call research, build-strategy doc, ROI math.
4. ⬜ Build Team (parallel) — Build + Integration + QA agents, QA↔Build loop.
5. ⬜ Handover Agent — handover package, backups, acceptance signoff.
6. ⬜ Retainer/Ops Agent — monitoring, monthly stats, scope-guard.

## Agents
| Agent | Type | Reads | Writes / acts |
|---|---|---|---|
| Prospecting | sub-agent | lead list, web | lead list (append) |
| Outreach | sub-agent | lead list, stats | Gmail DRAFTS only (you send) |
| Discovery/Close | sub-agent | web, lead list, calendar | build-strategy docs |
| Build Team | **team** | build files | n8n/Vapi/Twilio config |
| Handover | sub-agent | build files | Drive (docs + backups) |
| Retainer/Ops | sub-agent | alerts, call data, Stripe | monthly stats |

## Hard rules baked into agents
- No agent sends email, places calls, or moves money. Those are human-gated.
- Agent teams are only used for the parallel Build cluster. Everything else is
  sequential and runs better as sub-agents.

## Enabling agent teams
`.claude/settings.local.json` contains the experimental enable flag,
`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` (verified against the official docs).
Only needed for the parallel Build Team — the orchestrator and sub-agents work
without it.
