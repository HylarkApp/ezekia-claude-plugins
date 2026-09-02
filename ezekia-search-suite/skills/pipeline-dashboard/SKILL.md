---
name: pipeline-dashboard
description: "Generate a live, interactive dashboard of search pipeline health across all active mandates — active mandate count, candidates by stage, what moved recently, and what needs attention today. Use when the user asks for a 'dashboard', 'pipeline health', 'how are our searches doing', 'what needs my attention today', or after search-suite-onboarding completes its setup. This is the visual, at-a-glance view; for a single mandate's narrative update to a client, use stakeholder-status-update instead."
---

# Pipeline Dashboard

The at-a-glance view across the whole book of search work — the thing you
check instead of asking "where do things stand" in five separate
conversations. Interactive and current, not a static snapshot from
whenever it was last built.

## Inputs

None required — defaults to all active mandates the user has visibility
into. Optionally scoped to one client or consultant if asked.

## Workflow

**A. Load cached conventions.** Check for the "SEARCH SUITE CONFIG" note
(see search-suite-onboarding) via `Ezekia:search_projects(title="Search
Suite Config", project_type="list")` then `Ezekia:get_notes(
entity_type="list", entity_id=<id>, q="SEARCH SUITE CONFIG")` —
`search_notes` has no free-text query, only `get_notes` on a known record
does — so stage labels and counts use this workspace's real taxonomy. If
no cache exists, run search-suite-onboarding first rather than guessing at
tag names.

**B. Pull live data.** `Ezekia:search_projects` for active mandates (open
assignments/opportunities). For each, `get_project_details` (counts only —
don't pull full candidate profiles here, this is a summary view) and
`list_project_candidates` grouped by the real pipeline-stage tags. Pull
recent notes/activity firm-wide (`search_notes`, last ~7 days) to power the
"what moved" section.

**C. Compute the headline metrics:**
- Active mandate count, and how long each has been open (from project
  `createdAt`)
- Candidates by stage, aggregated across mandates and per-mandate
- Mandates with no pipeline movement in the last ~2 weeks — flag these as
  **at risk**, the thing status meetings exist to catch
- What moved in the last ~7 days (new candidates added, stage changes,
  notes logged) — the "what happened overnight" view

**D. Render as an interactive artifact.** Load the Visualizer's `chart` and
`diagram` modules (read_me first). Build a dashboard with: a summary strip
(active mandates / total candidates / at-risk count), a stage-distribution
chart, and a per-mandate table (client, role, stage breakdown, days open,
last activity, at-risk flag). This is a live pull each time the skill
runs — not something that auto-refreshes itself in the background; re-run
the skill to refresh.

## Output

The dashboard artifact, plus a one-line spoken summary leading with what
needs attention (e.g. "3 mandates at risk — no movement in 2+ weeks:
[names]") rather than just restating the counts already visible in the
chart.

## Sanity-check the data before trusting it

Not every connected Ezekia workspace runs candidate search mandates —
some (e.g. a vendor's own sales-pipeline instance) use `assignment`/
`opportunity` projects for something else entirely, like tracking
prospective customers through a sales funnel. If project statuses look
like a sales process (e.g. "Discovery Call Completed", "Demo Completed")
rather than a search process, say so plainly instead of presenting the
dashboard as if it were candidate pipeline health — this distinction
isn't discoverable from tags alone and needs a plain-language check
against what the statuses actually describe.

## Not this skill's job

Narrative client-facing updates for a single mandate (stakeholder-status-
update) or candidate-level detail (longlist-shortlist-report). This stays
at the portfolio level.
