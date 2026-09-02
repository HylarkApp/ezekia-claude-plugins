---
name: executive-mapping
description: "Find candidates who currently or formerly held the target role at a list of target companies, combining web research with a live Ezekia check — off-limits status, past pipeline history, and internal notes — for every person found. Use this after target-company-mapping (and ideally database-mining) have run, or whenever the user asks to 'map candidates', 'find executives at these companies', 'build the longlist', or 'check who's off limits' for a search already scoped by company. This is the single step that both discovers people and enriches them against the firm's own CRM — there is no separate 'off-limits check' skill; it's built in here."
---

# Executive Mapping

Builds the candidate longlist for a search: for each target company, find
people who currently or formerly held the target role, then enrich every
one of them against Ezekia — off-limits status, past pipeline history,
useful notes — using whatever conventions *this* workspace actually uses
(never hardcode another firm's setup).

## Required inputs

Target company list (from target-company-mapping, or given directly) and
the target role/title. If a database-mining pass already ran in this
conversation, treat its "Current match" / "Dormant" / "Former holder" rows
as already-found — don't re-discover them, just fold them into the output
table.

## Step 0: load this workspace's conventions

Before matching or enriching anyone, check for a cached config:
`Ezekia:search_projects(title="Search Suite Config", project_type="list")`,
then `Ezekia:get_notes(entity_type="list", entity_id=<id>,
q="SEARCH SUITE CONFIG")` if found (`search_notes` has no free-text
query — only `get_notes` on a known record does). If found and not stale
(~60 days), use its tags/fields/off-limits convention directly. If
missing, run `search-suite-onboarding` first rather than rediscovering
inline each time — only fall back to ad-hoc discovery
(`get_tags(type='person'/'candidate')`, `meta.offLimitAgreements` + person
tags, `get_custom_fields(entity_type='people')`) if onboarding can't run.

State briefly what convention you're using (e.g. "this workspace flags
off-limits via [X]") before proceeding, so the enrichment step is legible.

## Workflow

**A. Discover people, per company.** Web search plus
`Ezekia:search_people(company_name=<company>)` (matches current and past
positions) filtered to the target job title / close synonyms. Keep current
and former holders in separate buckets — a former holder is still a valid
candidate, just note it.

**B. Enrich each person against Ezekia**, using Step 0's discovered
conventions:
- **Do-not-approach status** — apply whatever this workspace's real signal
  turned out to be; note the reason. Flag, never silently drop, the person.
- **Past pipeline** (last ~2 years) — from
  `relationships.assignmentsAsCandidate`: closed client-facing outcomes
  (submitted, interviewed, offer, rejected, placed). Ignore early-funnel-only
  activity and anything older than ~2 years.
- **Recent notes / conversations** — `mostRecentResearcherNote`,
  `mostRecentContextNote`, recent meetings. One tight line, not a dump.
  Skip profile-owner/created-at/hygiene-tag noise.
- **Contactability** — email on file / matching Outlook correspondence.

**C. Batch for scale.** Under ~25 people, one pass. Above that, chunks of
~15-20 (drop to ~10 if people carry long note histories); write results
after each chunk, state progress between chunks
(e.g. "companies 1-6 done, 7-14 to go").

## Output

One visualized table: Name, role, company, tier (from target-company-mapping
if available), current/former, location, Ezekia status (**On Ezekia** /
**Not on Ezekia** / **Ambiguous**), do-not-approach flag if applicable, and
a one-line context summary. Flag rows never get dropped from the table —
the search team decides what to do with them.

Close with counts: total found, on Ezekia, flagged do-not-approach, former
holders, not on Ezekia. Flag any ambiguous Ezekia matches for the user to
resolve by hand.

## Not this skill's job

Building the target company list (target-company-mapping) or the
first-pass internal database check before external discovery
(database-mining — run that first for a new mandate). Never writes back to
Ezekia.
