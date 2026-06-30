---
name: project-first-run
description: First end-to-end smoke test of the outreach pipeline — confirmed all steps work (webhook fetch, email find, draft, Gmail, status update)
metadata:
  type: project
---

Smoke test run on 2026-06-29 processed one lead (All Pro AC, https://allproacaz.com).

Every pipeline step confirmed working:
- get-leads webhook returns `{count, leads:[...]}` with full lead objects
- Email found via WebFetch on /contact page (allproac@cox.net)
- cold-email skill + rubric scoring worked; draft passed on iteration 2 (subject char count fix)
- Gmail draft created successfully via mcp__claude_ai_Gmail__create_draft; returns `{id: "r-..."}` format
- update-status webhook accepted POST and returned `{website, status}` confirmation

**Why:** First pipeline smoke test to confirm plumbing before scaling.
**How to apply:** All steps are confirmed functional. Subject line char count is easy to miss — count carefully on first pass.
