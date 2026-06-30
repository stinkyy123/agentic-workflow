# MakeMyWorkflow — Client Lifecycle Playbook

> Canonical source of truth for the sales→build→retainer process. Skills and agents cite
> this file rather than duplicating it. Anchored on the **AI receptionist** offer but the
> structure applies to SMS reactivation, lead follow-up, and future products — only the
> product-specific inputs (the "offer pack") change.

Cold call → booked meeting → close → build → handover → retainer.

## Funnel Map
- **Phase 1** — Cold call → booked meeting
- **Phase 2** — Discovery → close
- **Phase 3** — Build → handover
- **Phase 4** — Maintenance retainer

---

## Phase 1 — Cold Call → Booked Meeting

**Talk track (6 parts):** voicemail-drop callback opener → ICP qualifier → status alignment + change in the world → unique mechanism → pullback + soft close → tie down. **Qualify AFTER booking, not before.**

1. **Opener (voicemail-drop callback)** — VM drop the night before, call next day from the same caller ID. On pickup: *"Hey — I tried reaching you last night, couldn't get through. Do you guys have someone answering the phone at all hours?"* (pause, let silence sit). Branch on their answer into a permission-based 30-second ask.
2. **ICP qualifier** — *"Before I get ahead of myself — you guys mostly residential plumbing, or commercial too?"* Exit fast if not ICP.
3. **Status alignment + change** — we build AI receptionists for plumbing shops; voice AI crossed the realism threshold in the last 12–18 months; ~30–40% of after-hours plumbing calls go to voicemail and most callers dial the next shop.
4. **Unique mechanism** — AI agent on their existing number, picks up 24/7, qualifies, books emergencies to calendar, texts confirmation in ~90s; knows service area, pricing tiers, emergency protocol.
5. **Pullback + soft close** — *"You might already use an answering service, or your wife handles it fine. Would you be completely opposed to 15 minutes for a screen-share — see the dashboard, hear a sample call, decide if it's worth keeping in the back pocket?"*
6. **Tie down** — *"How's tomorrow afternoon look?"*

**Qualify after booking:** calls/day? who handles after-hours? anyone else to loop in? how will you tell us if you can't make it? (micro-commitment lifts show rate).

**Session stats:** dial→pickup 3% floor / 6–7% great; pickup→right-ICP >60%; conversation→booked 20%+; booked→showed 70%+; showed→closed 30%+. If bad-data + wrong-ICP > 40%, fix targeting before script.

**Objections:** answering service (they take messages; we book + text in 90s) · wife/office handles it (after-hours/weekends?) · AI sounds robotic (play a sample call) · just email me (emails get buried; 15 min) · how much (depends on volume + CRM; covered in screen-share).

---

## Phase 2 — Discovery → Close

**LRP framework — run the whole call:** Listen → Repeat back → Poke deeper. Them 70%, you 30%. The #1 miss: client mentions pain and you move on instead of poking. Every pain → quantify → translate to dollars.

**Discovery questions:**
1. Inbound calls/day on average?
2. Who answers now? After hours? Weekends?
3. Est. % to voicemail / missed?
4. Avg ticket: emergency vs scheduled?
5. Dispatch/CRM software? (ServiceTitan, Housecall Pro, Jobber, none)
6. Service area + emergency pricing structure?
7. Tried an answering service? Liked / hated?
8. **ROI anchor (the close):** *"If you booked 5 extra jobs a month from currently-missed calls, what's that worth?"* Anchor all pricing off their answer.

**Do not price before you know:** call volume (peak/after-hours/weekend), CRM integration complexity, calendar/dispatch setup, A2P 10DLC status, service-area logic, pricing tiers + emergency surcharge.

**Build-strategy doc (bring to call 2/3):** tech stack (Retell, Twilio, n8n, their CRM/calendar), Phase 1 scope + definition of done, timeline (~2–3 wks for receptionist), in/out of scope. Get them nodding on every line, then price.

**Pricing model (never hourly — tie to outcomes):**
| Component | Price | Covers |
|---|---|---|
| Setup (one-time) | $1,500–$3,000 | Build, integration, prompt tuning, go-live |
| Monthly retainer | $300–$800/mo | Hosting, monitoring, prompt updates, support |
| Usage | Pass-through | Client pays Retell + Twilio directly |

**Justify with math:** *"You miss ~8 emergency calls/wk, avg ticket $450, even 25% conversion = $450/wk, $1,800/mo recovered. Setup pays back month one; monthly runs 15–20% of recovered revenue."* State price, state ROI, then shut up.

**Close:** walk the build-strategy doc line by line → state price + ROI → send contract (DocuSign/PandaDoc) while on the call → book kickoff before hanging up → Loom welcome within 24h.

---

## Phase 3 — Build → Handover

**Hosting & ownership (n8n license compliance):**
| Component | Ownership |
|---|---|
| Retell account | Client owns + pays directly |
| Twilio account | Client owns (A2P 10DLC is per-business) |
| Google Calendar / CRM | Client owns + grants access |
| n8n hosting | Your self-hosted instance, running their workflow as service delivery |

You cannot resell n8n as a hosted product without a commercial license; you can sell services built on n8n.

**Security:** credentials in n8n credentials only (never node params) · sign Vapi webhooks where possible · trim execution retention ~30 days · error workflow pings you + client · don't store PII in logs longer than needed · per-client env vars, no cross-client credential leakage.

**Pre-go-live QA (run real scenarios, log inputs/outputs/tools, show client):** emergency (burst pipe 2am) · routine scheduling · pricing shopper · angry customer · spam/wrong number · repeat customer · out-of-service-area · heavy accent/noise · interrupts mid-sentence · calendar conflict. Never let calls fail silently — build an error workflow.

**Handover deliverables:** Loom walkthrough (5–10 min) · one-pager (stack diagram, their logins, recovery) · test vs production workflows (changes go through test first) · weekly n8n JSON backup to Drive/GitHub · acceptance signoff → final invoice.

---

## Phase 4 — Maintenance Retainer

**Covered:** monitoring, prompt tweaks, bug fixes, minor integration adjustments, monthly 10-min review (call volume, booking rate, missed-calls-recovered).

**NOT covered (re-scope as new project):** new workflows, new integrations, major service expansion, multi-location/franchise rollout.

**Service levels:** critical outage < 2h · bug/tweak < 1 business day · new feature → scope separately.

**Exit clause:** export workflows, hand over docs, walk them through, bill X hrs at $Y/hr. They keep Vapi/Twilio/Calendar (always theirs). Clean exit protects reputation + referrals.
