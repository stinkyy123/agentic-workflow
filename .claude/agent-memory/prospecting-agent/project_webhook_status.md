---
name: project-webhook-status
description: n8n webhook mmw-leads is working as of 2026-06-26 — fixed after a Google Sheets schema mismatch; returns HTTP 200 and stamps rows with date_added + status
metadata:
  type: project
---

The webhook at `https://poopoo4564564.app.n8n.cloud/webhook/mmw-leads` is working as of 2026-06-26. Returns HTTP 200 and echoes the row back with `date_added` and `status` fields stamped by the workflow. Confirmed live during the Phoenix HVAC smoke test (3 leads pushed successfully).

**Previous issue (resolved 2026-06-26):** Was returning HTTP 500 `{"message":"Error in workflow"}` on every POST. Root cause was a Google Sheets schema mismatch inside the n8n workflow — not a payload problem. Fixed on the n8n side.

**How to apply:** Webhook is expected healthy. If 500s return, test with a minimal payload first — the prior incident showed the error lives in the workflow node, not the caller's JSON.
