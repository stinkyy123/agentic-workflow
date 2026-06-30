---
name: offer-receptionist
description: >
  The AI-receptionist offer pack for MakeMyWorkflow discovery/close: product-specific discovery
  questions, tech stack, pricing model, ROI variables, and objections. Used with the
  discovery-close method skill. To support a new product later, add a sibling offer-<product>
  pack with the same sections — the discovery-close method and discovery-agent stay unchanged.
---

# Offer Pack — AI Receptionist

Product-specific inputs for the `discovery-close` method. Source: `docs/client-lifecycle-playbook.md`.

## One-liner
An AI agent on their existing number that answers 24/7, qualifies the call, books jobs to the
calendar, and texts the customer confirmation in ~90 seconds.

## Discovery questions (ask on the call)
1. Inbound calls per day on average?
2. Who answers now? After hours? Weekends?
3. Estimated % to voicemail / missed?
4. Average ticket: emergency vs scheduled?
5. Dispatch/CRM software? (ServiceTitan, Housecall Pro, Jobber, none)
6. Service area + emergency pricing structure?
7. Tried an answering service? Liked / hated?
8. ROI anchor (the close): "If you booked 5 extra jobs/mo from missed calls, what's that worth?"

## Required scoping inputs before pricing
Call volume (peak / after-hours / weekend), CRM integration complexity, calendar/dispatch setup,
A2P 10DLC status, service-area logic, pricing tiers + emergency surcharge.

## Tech stack
Retell (voice) + Twilio (number; A2P 10DLC, client owns) + n8n (your hosted workflow) + their CRM/calendar.

## Pricing model (never hourly)
- Setup (one-time): $1,500–$3,000
- Monthly retainer: $300–$800/mo
- Usage: pass-through (client pays Retell + Twilio directly)

## ROI formula
`missed_calls/wk × avg_emergency_ticket × conversion_rate` (default 25%) = weekly recovered; ×4.3 for monthly.
Frame: setup pays back ~month one; monthly ≈ 15–20% of recovered revenue.

## Objections → responses
- "We already have an answering service" → they take messages; we book the job + text confirmation in 90s.
- "My wife / office handles it" → after-hours and weekends too?
- "AI sounds like a robot" → play a sample call from a real plumbing shop.
- "Just email me" → emails get buried; lock 15 minutes for the screen-share.
- "How much?" → depends on volume + CRM; covered in the screen-share. Never quote blind.

## <FILL IN> (Albert tunes later)
Real avg tickets per trade, your conversion assumptions, exact stack preferences, signature/branding,
typical timeline commitments.
