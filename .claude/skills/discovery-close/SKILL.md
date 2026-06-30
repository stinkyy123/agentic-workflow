---
name: discovery-close
description: >
  The product-agnostic method for running discovery calls and closing in MakeMyWorkflow's
  pipeline. Use when prepping for a discovery call, or turning captured discovery answers into a
  build-strategy doc + ROI — for ANY offer (receptionist, SMS reactivation, lead follow-up). Pairs
  with an offer pack (e.g. offer-receptionist) that supplies the product-specific inputs.
---

# Discovery → Close (Method)

Product-agnostic. The process is identical across offers; the product specifics come from the
matching **offer pack** (e.g. `offer-receptionist`). Source of truth: `docs/client-lifecycle-playbook.md`.

## Core rules (every offer)
- **LRP every exchange** — Listen → Repeat back → Poke deeper. Them 70%, you 30%.
- **Quantify every pain → dollars.** Every painful number becomes a dollar figure.
- **ROI is anchored on THEIR numbers**, never assumptions (e.g. missed/wk × avg ticket × conversion).
- **Do not price before you know** the offer pack's required scoping inputs. Missing input → flag it, don't guess.

## Mode 1 — Pre-call prep
Input: a prospect (name / site). Produce a call-prep sheet:
1. What's knowable from their website + Google Business Profile — **no assumptions about internal numbers**.
2. The offer pack's discovery questions, tuned to this prospect.
3. Relevant objections + responses from the offer pack.

No build spec, no ROI — you don't have their numbers yet.

## Mode 2 — Build-strategy doc (after the discovery call)
Input: the **real discovery answers** Albert captured on the call.
1. Verify the offer pack's **required scoping inputs** are present. Any missing → list them and stop short of pricing.
2. Build the doc (structure below) using the offer pack's stack + pricing model.
3. ROI math from THEIR numbers. State the price, then the ROI.

### Build-strategy doc structure
- Prospect snapshot
- Tech stack (offer pack + their CRM/calendar)
- Phase 1 scope + definition of done
- Timeline
- In scope / out of scope
- ROI math (their numbers)
- Suggested price (offer pack model) — internal until walked line-by-line on the call

## Output
- Mode 1 → call-prep sheet.
- Mode 2 → build-strategy doc (written to Drive) + a one-line ROI summary, plus any missing scoping inputs.
