# Prospecting Agent — Design Spec

**Date:** 2026-06-25
**Status:** Approved (pending spec review)

## Context

First specialist agent in the MakeMyWorkflow pipeline. Per the project README's build
order ("Build one agent end-to-end, prove it on real work, then add the next"), the
Prospecting Agent is built first — it's read-heavy and has no human-gated send actions,
so it's the lowest-risk place to start.

MakeMyWorkflow sells **AI receptionists to trades businesses**. This agent finds, enriches,
and ICP-scores trades leads, then pushes them to a Google Sheet via an n8n webhook so Albert
has a scored prospect list to work from.

## Purpose

Given a target brief (e.g. *"find HVAC companies in Phoenix"*), the agent sources real
businesses from the web, enriches each, scores them 1–5 against the ICP rubric, pushes
leads to the lead sheet, and reports a summary back to the orchestrator / Albert.

## Flow

1. **Source** — `WebSearch` for businesses matching the brief (trade + location).
2. **Enrich** — `WebFetch` each candidate's website / listing for: website, phone,
   owner_name, location, category, and ICP signals (review count, # of locations,
   business age, online-booking / receptionist hints).
3. **Score** — apply the **`icp-scoring` skill** (the single source of truth, also used by
   the discovery agent + orchestrator); record a one-line `icp_reason`.
4. **Push** — `Bash` `curl` POST each lead as JSON to the n8n webhook.
5. **Report** — return a summary to the orchestrator/Albert: counts (found / scored /
   pushed), the top scorers with reasons, and any briefs it could not fulfill.

## ICP Rubric (1–5)

> **Canonical location:** this rubric now lives in `.claude/skills/icp-scoring/SKILL.md` as
> the single source of truth (reusable by prospecting, discovery, and the orchestrator). It
> is reproduced below as design rationale; the skill is authoritative.

Built around the "Goldilocks zone": big enough to justify the cost of a receptionist,
small enough that the owner / decision-maker is reachable.

| Score | Profile |
|---|---|
| **5** | Single-location, owner-operated, high-call trade (HVAC / plumbing / roofing / electrical); ~20–150 reviews (established, not huge); owner name + direct phone findable; signs they miss calls (no online booking, reviews mention "couldn't reach them"). |
| **4** | Solid fit, one or two signals soft (e.g. ~150–250 reviews, owner harder to find). |
| **3** | Workable but borderline on size — a bit small/new or a bit large. |
| **2** | Likely too small/new (<~15 reviews, <1–2 yrs, no real web presence) **or** edging too big (2–3 locations). |
| **1** | Disqualified — franchise / multi-location / call-center, national brand, no public phone, or dead business. |

**Disqualify-on-sight (auto-1):** franchise/chain, 4+ locations, corporate "our team" page
with no reachable owner, or no public phone.

## Lead schema (sent to the webhook)

Agent sends these 9 fields as a JSON body:

`business_name, website, phone, owner_name, location, category, icp_score, icp_reason, source_url`

`date_added` (today) and `status` (`new`) are stamped by the n8n workflow, not the agent.

## Lead sink (built + verified 2026-06-25)

- n8n workflow **"MMW — Prospecting Lead Sink (Sheets)"**, id `6OiywfVoOKXxpA7f`, **active**.
- **Webhook:** `https://poopoo4564564.app.n8n.cloud/webhook/mmw-leads`
- POST a JSON body with the 9 fields → appends a row to the **leads** spreadsheet
  (`https://docs.google.com/spreadsheets/d/1fi44BbfsX68CcPGhn99gulCccCuAV3b4ywzWPTREK6E`),
  first tab, auto-stamping `date_added` and defaulting `status` to `new`.
- Verified end-to-end via a smoke-test execution (status: success, row written).
- **Known follow-up:** dedup is OFF for v1 (plain Append, because the sheet started empty).
  Upgrade to Append-or-Update matching on `website` once the sheet has data + headers.

## Agent config

- **name:** `prospecting-agent`
- **tools:** `Read, Write, WebSearch, WebFetch, Bash` (Bash used only for the `curl` push)
- **model:** `sonnet` (research-heavy and high-volume; cheaper/faster than opus, plenty capable)
- **memory:** `project` (matches the pipeline-manager pattern)
- **Scoring:** applies the `icp-scoring` skill — the rubric is NOT embedded in the agent body
- **Push policy:** push **all** scored leads (the sheet has `icp_score` for filtering)
- **Per-run cap:** stop after **~25 businesses researched OR 10 qualified leads pushed**,
  whichever comes first — a hard cap so it can't run forever (per the `loop-design` skill).

## Hard rules (baked into the agent)

- Never emails, calls, or messages a business. No outreach of any kind.
- Its only write side-effect is appending to the lead sheet via the webhook.
- Surfaces results to Albert and stops; performs no human-gated actions.

## Out of scope (v1)

- Sink-side dedup (deferred; see follow-up above).
- *(Reversed 2026-06-26)* `icp-scoring` is now a **separate skill** and the single source of
  truth for scoring — reusable by the discovery agent and orchestrator, not embedded in the
  agent. See `.claude/skills/icp-scoring/SKILL.md`.
- Reading the existing sheet to skip already-researched businesses (no list endpoint yet).

## Verification

1. Build `prospecting-agent.md`; confirm Claude Code registers it as an agent type after restart.
2. Dry-run: give it a small brief (e.g. "find 3 HVAC companies in Phoenix"), confirm it
   researches, enriches, scores, and the `curl` push lands real rows in the leads sheet
   (delete test rows afterward).
3. Confirm scores and `icp_reason` values are sensible against the rubric.
