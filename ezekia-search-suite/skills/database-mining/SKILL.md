---
name: database-mining
description: "Mine the firm's existing Ezekia database for candidates before doing any external market mapping. Use this whenever a new search/mandate starts, or when the user asks to 'check who we already know', 'mine our database', 'search our own candidates first', 'find dormant candidates for X role', or 'who has Ezekia got for this'. This should run BEFORE target-company-mapping / executive-mapping for any new mandate — re-surfacing known and dormant candidates is higher-return than fresh external sourcing and should never be skipped. Also finds former holders of the target role at named companies (people who used to do this job, not just people doing it now)."
---

# Database Mining

The highest-return move at the start of a search is checking who the firm
already knows before spending hours on fresh external research. This skill
surfaces two groups from Ezekia: **currently reachable people who match the
brief**, and **dormant contacts worth re-engaging** — plus, if target
companies are known, **former holders of the target role** that a
forward-looking company search would miss.

## Required inputs

Role title (and reasonable synonyms — titles vary by company) and enough of
the brief to judge fit: sector/industry, seniority, location. Pull these
from a position-spec brief already in the conversation if one exists rather
than re-asking. Target company list is optional (only needed for the
former-holder pass).

## Connector

Ezekia only. Check it's connected; stop and say so if it won't connect.

## Step 0: load this workspace's conventions

Never assume tag or field names — different firms configure Ezekia
differently. Check for a cached config first: `Ezekia:search_projects(title="Search
Suite Config", project_type="list")`, then `Ezekia:get_notes(
entity_type="list", entity_id=<id>, q="SEARCH SUITE CONFIG")` if the
project exists (`search_notes` has no free-text query — only `get_notes`
on a known record does). If found and not stale (~60 days), use its
tag/field/off-limits conventions directly — don't re-discover. If missing,
run `search-suite-onboarding` first (it's quick and every other skill
benefits from the cache existing); only fall back to inline discovery (`get_tags(type='person')`/`type='candidate')`,
`meta.offLimitAgreements` + person tags, `get_custom_fields(entity_type=
'people')`) if onboarding genuinely can't run.

## Workflow

**A. Current matches.** `search_people` on job_title (try the role title and
close synonyms), industry, and location. For each hit, pull enough of
`get_person_details` to judge: current employer, tenure, last contact
(`manager.mostRecentResearcherNote` / `mostRecentContextNote` dates), any
off-limits signal found in Step 0, and past pipeline outcomes
(`relationships.assignmentsAsCandidate`).

**B. Dormant candidates.** From the same pool (or a broader `search_people`
on the same criteria without a recency filter), flag people with no recent
activity (no note, meeting, or pipeline movement in roughly the last 12
months) but a clean fit and no off-limits flag — these are re-engagement
candidates, the group most firms under-use. Note *why* each is worth
re-engaging (past fit note, dormant but strong background) rather than just
listing them.

**C. Former role-holders (if target companies given).** Per company,
`search_people(company_name=<company>, job_title=<role>)` — `company_name`
matches historical as well as current positions, so filter to people whose
tenure in that role has ended. These are people a forward company search
alone would never surface.

**D. Batch for scale.** Under ~25 people, one pass is fine. Above that,
work in chunks of ~15-20, write findings after each chunk rather than
holding everything in memory, and state progress between chunks (e.g.
"batch 1/4 done: 6 current, 3 dormant, 1 off-limits").

## Output

One visualized table: Name, current role/company, status
(**Current match** / **Dormant — re-engage** / **Former holder** /
**Off-limits**, using whatever this workspace's actual off-limits signal
turned out to be), last activity, one-line fit note. Off-limits rows are
flagged but not dropped from the table.

Close with a one-line summary (e.g. "14 people in Ezekia: 6 current
matches, 5 dormant worth re-engaging, 2 former holders, 1 off-limits") and
a note that this is the seed list — target-company-mapping /
executive-mapping should dedupe against these names rather than re-finding
them externally.

## Not this skill's job

External market mapping for people not already in Ezekia (executive-mapping
does that) or building the target company list (target-company-mapping).
Never writes back to Ezekia — read-only.
