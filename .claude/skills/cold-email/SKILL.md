---
name: cold-email
description: >
  How to write and score a cold outreach email for MakeMyWorkflow's AI-receptionist offer to
  a trades business. Use when drafting or revising a cold email to a prospect (outreach agent
  now; discovery/close later). Holds the voice, structure, personalization rules, and the
  pass/fail rubric a draft must meet.
---

# Cold Email — Trades Outreach (AI Receptionist)

Single source of truth for cold-email copy + quality. Draft, then score against the rubric;
revise until it passes or you hit the try cap (see the `loop-design` skill).

> ⚠️ **SCAFFOLD** — Albert will fill in the real voice/offer specifics. The structure and
> rubric below are working defaults so the agent functions today; tighten the `<FILL IN>`
> sections later.

## The offer (one line)
`<FILL IN>` — default: an AI receptionist that answers every missed call 24/7 so the business
stops losing jobs to voicemail.

## Voice & constraints
- Plain text; sounds like one human emailing another, not marketing.
- Short — body ≤ ~120 words.
- One clear ask (the CTA); no link dumps, no attachments.
- `<FILL IN: tone, phrases to use/avoid, anything off-limits>`

## Structure (default)
1. **Subject** — ≤ ~50 chars, specific, no clickbait/spam words.
2. **Opener** — one personalized line proving it isn't a blast (owner name, city, the trade,
   or a real detail like "no online booking on your site").
3. **Problem → offer** — a line or two tying their missed-call risk to the receptionist offer.
4. **CTA** — one low-friction ask (e.g. "worth a quick 10-min call this week?").
5. **Sign-off** — `<FILL IN: Albert's name / signature>`.

## Personalization inputs (from the lead)
`owner_name, business_name, category` (trade), `location`, `icp_reason` (the miss-call angle).
If `owner_name` is missing, address the business — never "Hi there".

## Scoring rubric (every item must pass)
- [ ] Subject ≤ ~50 chars, no spam-trigger words (free, guarantee, $$$, ALL CAPS).
- [ ] Body ≤ ~120 words.
- [ ] Personalization present — a real, lead-specific detail, not just the name.
- [ ] Exactly one CTA.
- [ ] No link dumps / no implied attachments.
- [ ] Reads human (no "Dear Sir/Madam", no corporate filler).

Revise until all pass **or** 3 tries (`loop-design`). Keep maker and checker separate — don't
let the draft grade its own homework on the same pass.

## Output
Return: `subject`, `body`, and a one-line note if any rubric item could not be met.
