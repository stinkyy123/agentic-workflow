---
name: receptionist-build
description: >
  The method + landmines for building and deploying a MakeMyWorkflow AI receptionist from the
  receptionist-template. Use when generating a client config, rendering, deploying, or QA-ing a
  receptionist build. Distills the template's BUILD-NOTES.md: deploy ordering, the publish+re-pin
  dance, prompt/worker sync, the voice-config overwrite warning, Smarty policy, and the QA loop.
---

# Receptionist Build (method)

Source of truth for the code and every incident: `BUILD-NOTES.md` in the `receptionist-template`
repo — read it before touching the worker. **Parameterize, never refactor:** the worker's logic is
battle-tested; a needed logic change stops and asks Albert.

## Client dashboard (every build ships one)
The worker serves a read-only owner dashboard at `/d/<DASHBOARD_SECRET>` (BUILD-NOTES §9). Every
build must:
- set `avgTicket` in the config (from the discovery ROI numbers — drives the revenue figure),
- generate a per-client secret and set it: `wrangler secret put DASHBOARD_SECRET` (e.g.
  `openssl rand -hex 24`) — it's a Worker secret, never in the committed config,
- **hand the `/d/<secret>` link to the owner at handover** (the handover-agent delivers it).

Preview/QA it before go-live: `node render.js <client> && node tools/preview-dashboard.js <client>`.
The dashboard shows outcomes only (never internal states); if you add a stat, keep that invariant.
It's a worker-only change — no Retell push/publish/re-pin just for the dashboard.

## Before you build: sanity-check the config
`node validate-config.js <client>` (exits 1 on a HIGH finding). It catches the two config-logic
traps the code cannot:
- an **`emergencyKeyword` that also names a normal service** — the worker's emergency guardrail
  scans those keywords and returns *before any calendar path*, so **that service can never be
  booked**. (An HVAC client with "no cooling" as both a service and an emergency keyword could
  never book their most common job.)
- a **`highTicketKeyword` matching a cheap job** — the documented "$199 faucet swap" footgun;
  every one gets `high_ticket_review`-flagged for a human.

These are **business decisions — surface them, never silently "fix" them.**

## Schedule + timezone (now enforced server-side)
- **`schedule` is enforced.** `checkAvailability` fails closed on a closed day, an hour outside
  **that day's** window, a same-day request past `sameDayCutoffH`, or a same-day start past
  `latestSameDaySlotH`. Alternatives stay inside the same window. **Emergencies bypass it** (they
  never touch the calendar), so `emergencyOnlyDays` works. No `schedule` block → falls back to
  `businessStartH`/`businessEndH`. `minFee` stays prompt-text (a quoting rule, not scheduling).
- **`timezone` is load-bearing — get it right.** The worker's time math used to be hardcoded to
  Eastern; a Phoenix caller asking for 2 PM was booked **3 hours early**. It now derives from
  `BUSINESS.timezone`. The `eastern*` function names are **legacy** — they are timezone-generic.
- ⚠️ **Redeploying BlueTap applies a real behavior change:** Sunday used to be silently bookable
  and is now correctly refused; the 4 PM cutoff / 5 PM latest slot are now real. Say this out loud
  before any BlueTap redeploy — it is the intended fix, but it is not a no-op for them.

## Trade-agnostic wording
`emergencyScreenQuestion` and `emergencyExamples` are config, not constants — set them for the
client's trade. The defaults are plumbing; an HVAC agent must not screen callers for sewage.

## Golden rule: deploy the worker FIRST, then Retell
A prompt expecting new worker behavior against an un-deployed worker is a silent mismatch.
Worker-only changes need just `wrangler deploy`; prompt/tool changes need the full Retell dance.

## The deploy dance (BUILD-NOTES §2 — skip a step and you ship into the void)
1. `cd clients/<client>/dist/worker && npx wrangler deploy` — needs `CLOUDFLARE_API_TOKEN`, a
   **`cfut_` Workers token** (a `cfat_` token fails with 9109).
2. `RETELL_API_KEY=… TOOL_SECRET=… node deploy_retell.js <client>` — pushes prompt + tools to the LLM.
3. **Publish:** `POST https://api.retellai.com/publish-agent/<agent_id>` with body `{}`.
4. **RE-PIN the phone number** to the just-published version — THE #1 footgun:
   - The number hard-pins an integer `agent_version` in `inbound_agents`; it does NOT follow
     "latest published". Publishing changes nothing for callers until you re-pin.
   - Find the version: `GET /get-agent/<agent_id>` returns the current DRAFT number; the version you
     just published = **draft − 1**. Verify `is_published:true` at `GET /get-agent/<id>?version=N`.
   - `PATCH /update-phone-number/<E164>` with
     `{ "inbound_agents":[{ "agent_id":"…","agent_version":<INT>,"weight":1 }] }`.
     `agent_version` must be an integer; the old `inbound_agent_id/version` fields are deprecated (400).

## Voice config (BUILD-NOTES §3) — the overwrite warning
`deploy_retell.js` OVERWRITES every run: the LLM `general_prompt`+`general_tools`, and agent
`interruption_sensitivity`(0.3), `responsiveness`(1), `enable_backchannel`(false), `ambient_sound`.
It does NOT touch `voice_id/voice_model/voice_speed/voice_temperature/volume/stt_mode/
denoising_mode/backchannel_frequency/language/model` (dashboard-only). **Hand-tuning a
script-controlled field in the dashboard is silently reverted on the next deploy** — codify winners
into the script. `interruption_sensitivity 0.3` is the echo↔talk-over dial; drop to 0.2 if a tester
still hears the greeting stutter.

## Smarty address policy (BUILD-NOTES §4)
At `dpv==="Y"` Smarty is authoritative — trust its standardization, no flag. Deliverable-but-
non-exact → SOFT `address_autocorrected` (confirm SMS). `no_match`/undeliverable → HARD callback.
Ask-address-twice is the capture protocol; the WORKER compares the two captures, not the model.

## QA loop (maps the playbook's 10 pre-go-live scenarios to `loop-design`)
- **Checkable goal:** all 10 scenarios pass. **Hard cap:** stop after N build↔QA rounds; log each.
- **Automated (tool-call) scenarios** → `curl` payloads to `https://<baseUrl>/t/<TOOL_SECRET>`
  simulating each tool call; assert the worker's response + the resulting Bookings row/flag.
  ⚠️ test tool-calls write REAL Bookings rows (BUILD-NOTES §3 caveat) — use throwaway rows / the
  TEST agent clone; delete them after.
- **Voice scenarios** (echo, interrupts, heavy accent, angry caller) → flag for Albert's manual
  test calls; they cannot be automated.
- The 10: emergency (burst pipe 2am) · routine scheduling · pricing shopper · angry customer ·
  spam/wrong number · repeat customer · out-of-service-area · heavy accent/noise · caller interrupts
  mid-sentence · calendar conflict. Never let a call fail silently — the error workflow must alert.
