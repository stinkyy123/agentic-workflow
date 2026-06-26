---
name: icp-scoring
description: >
  The authoritative rubric for scoring trades-business leads 1-5 for MakeMyWorkflow's
  AI-receptionist offer. Use whenever you need to judge how good a fit a business is — in
  prospecting (scoring sourced leads), discovery (re-checking a prospect before a call), or
  the orchestrator (prioritising a list). Defines the 1-5 scale, the "Goldilocks zone"
  sizing, and the disqualify-on-sight auto-1 rules.
---

# ICP Scoring — Trades Businesses (AI Receptionist Fit)

Single source of truth for how good a fit a trades business is for an AI receptionist.
Score every lead 1-5 and record a one-line reason. Any agent (prospecting, discovery, the
orchestrator) that needs to judge fit applies THIS skill rather than inventing its own scale.

## The Goldilocks principle
The ideal client is big enough that missed calls cost real money (so a receptionist pays for
itself), but small enough that you can still reach the owner / decision-maker. The score
penalises both extremes: too small/new, and too big/corporate.

## Signals to gather
- **Trade / category** — HVAC, plumbing, roofing, electrical = high call volume (good).
- **Number of locations** — 1 = ideal; franchise / chain = disqualify.
- **Owner-operated?** — is the owner's name and a direct phone findable?
- **Review count** — proxy for established + size; sweet spot ~20-150.
- **Business age / review velocity** — too new = not enough call volume yet.
- **Miss-call signals** — no online booking, no receptionist, reviews mentioning
  "couldn't reach them" / "never called back".
- **Public phone number present.**

## The 1-5 scale
| Score | Profile |
|---|---|
| **5** | Single-location, owner-operated, high-call trade; ~20-150 reviews (established, not huge); owner name + direct phone findable; clear miss-call signals. |
| **4** | Solid fit, one or two signals soft (e.g. ~150-250 reviews, owner harder to find). |
| **3** | Workable but borderline on size — a bit small/new or a bit large. |
| **2** | Likely too small/new (<~15 reviews, <1-2 yrs, no real web presence) **or** edging too big (2-3 locations). |
| **1** | Disqualified — see auto-1 below. |

## Disqualify-on-sight (auto-1)
Score **1** immediately, with no further analysis, if ANY of these are true:
- Franchise or chain
- 4+ locations
- Corporate "our team" presence with no reachable owner
- No public phone number
- Dead / closed business

## Output
For each lead, produce:
- `icp_score` — integer 1-5
- `icp_reason` — one line naming the deciding factors
  (e.g. "single-loc HVAC, ~60 reviews, owner + cell listed, no online booking → 5")
