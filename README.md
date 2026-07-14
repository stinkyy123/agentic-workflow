Agent Team

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
- `cold-email` — voice, structure, and pass/fail rubric for cold outreach emails
  (used by outreach; reusable by discovery/close later).
- `discovery-close` — product-agnostic discovery→close method (LRP, ROI from their
  numbers, "don't price before scoped", build-strategy doc structure).
- `offer-receptionist` — the AI-receptionist offer pack (discovery questions, stack,
  pricing, ROI vars, objections). Swap in a sibling `offer-<product>` pack for new products.
- `receptionist-build` — method + landmines for building/deploying a client receptionist from
  the receptionist-template (deploy dance, publish/re-pin, Smarty policy, the 10-scenario QA loop).

Process source of truth: [`docs/client-lifecycle-playbook.md`](docs/client-lifecycle-playbook.md).

## Build order (do NOT build all at once)
Build one agent end-to-end, prove it on real work, then add the next.

1. ✅ Prospecting Agent — built (lowest risk, read-heavy, no send-gate)
2. ✅ Outreach Agent — drafts cold email (email-only v1; dial stats deferred). SEND GATE: drafts only.
3. ✅ Discovery/Close Agent — Mode 1 pre-call prep + Mode 2 build-strategy doc (ROI from real
   discovery answers). Product-agnostic method + swappable offer pack.
4. ✅ Build Agent — gated receptionist build (config → render → deploy → QA), stops at every human
   gate. Drives the separate [receptionist-template](https://github.com/stinkyy123/receptionist-template) repo.
5. ✅ Handover Agent — stub (Phase 3: Loom, one-pager, backups, acceptance signoff).
6. ✅ Retainer/Ops Agent — stub (Phase 4: monitoring, monthly review, scope-guard).

## Agents
| Agent | Type | Reads | Writes / acts |
|---|---|---|---|
| Prospecting | sub-agent | lead list, web | lead list (append) |
| Outreach | sub-agent | lead list, stats | Gmail DRAFTS only (you send) |
| Discovery/Close | sub-agent | web, lead list, calendar | build-strategy docs |
| Build | sub-agent (gated) | client scope, receptionist-template | rendered build, gated deploy + QA |
| Handover | sub-agent | build files | Drive (docs + backups) |
| Retainer/Ops | sub-agent | alerts, call data, Stripe | monthly stats |

## Hard rules baked into agents
- No agent sends email, places calls, provisions accounts, deploys, or moves money.
  Those are human-gated — the agent prepares.
- Every agent is a sequential, gated **sub-agent**. The build is a single gated
  `build-agent`, not a parallel team.

## Agent teams (not required by this design)
`.claude/settings.local.json` still carries the experimental flag
`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` (verified against the official docs), but the
build ended up as a single gated `build-agent`, so nothing in this repo depends on
agent teams. The flag is harmless to leave enabled.
