---
name: search-suite-onboarding
description: "Run this ONCE per Ezekia workspace, before any other skill in the executive-search suite (position-spec, database-mining, target-company-mapping, executive-mapping, longlist-shortlist-report, call-notes-crm-hygiene) needs to discover this workspace's tags, custom fields, or off-limits conventions. Use when the user asks to 'set up the search suite', 'onboard this workspace', 'connect Ezekia', or when another skill's Step 0 discovery check finds no cached config and needs one created. Also stands up the initial pipeline dashboard as its last step. Re-run only when explicitly asked to 'refresh setup' or when the user mentions tags/fields/pipeline stages have changed in Ezekia."
---

# Search Suite Onboarding

Every other skill in this suite needs to know this workspace's real tags,
custom fields, and off-limits convention — but re-discovering that from
scratch on every single run is wasteful and, worse, risks skills in the
same conversation using inconsistent answers. This skill runs the
discovery once, caches it *inside Ezekia itself* (so it persists across
conversations, not just this one), and every other skill checks for that
cache before doing its own discovery.

## Step 1: check for an existing cache

The cache lives as a note on a dedicated anchor **list-type project**, not
on a company or the current user's own profile. Testing found two dead
ends: a system user isn't guaranteed to have a corresponding Person record
(`Ezekia:me` returns a user id that `get_notes(entity_type='person', ...)`
doesn't recognize as a person), and there is no standalone company-creation
tool — companies can only be created as a side effect of `create_project`.
A "list" project is the right fit anyway: this is exactly what firms
already use "list" projects for internally (e.g. ad-hoc tracking lists
alongside long-running talent pools), it's always queryable by title, and
it supports notes directly.

`Ezekia:search_projects(title="Search Suite Config", project_type="list")`.
If it exists, pull its notes with `Ezekia:get_notes(entity_type="list",
entity_id=<id>, q="SEARCH SUITE CONFIG")` — `search_notes` has no
free-text query parameter, only `get_notes` on a known record does, so the
anchor must be found first. If a recent note exists (treat anything older
than ~60 days as stale), report what it contains and ask whether to reuse
it or refresh. If the project or note doesn't exist, proceed to discovery.

## Step 2: discover workspace conventions

- `Ezekia:get_tags()` with no type filter — returns every tag category at
  once (person, candidate, company, project/assignment/opportunity/list,
  note, owner, lead). Record the real labels for each.
- `Ezekia:get_custom_fields` for `people`, `companies`, `projects`,
  `assignments`, `opportunities`, `lists` — record fieldId, label, and type
  for anything that looks relevant to search work (skip obvious
  demo/placeholder fields).
- **Off-limits convention**: sample a handful of real person records
  (`search_people` + `get_person_details`) and check
  `meta.offLimitAgreements` and `manager.tags` for whatever this workspace
  actually uses to flag do-not-approach. Note the exact field/tag name(s)
  found — don't assume a label from another workspace.
- **Project-type usage**: check a few real projects to see how this firm
  actually uses assignment vs. opportunity vs. list, since that varies.
- **Scale check**: `Ezekia:search_projects` for active mandates, roughly
  how many, roughly how many candidates on file — this seeds the dashboard
  in Step 4.

## Step 3: write the cache

If the "Search Suite Config" list project doesn't exist yet, create it
(`Ezekia:create_project(label="list", name="Search Suite Config",
description="Internal anchor record for the executive-search skill
suite...")`) — this is a one-time, one-record piece of shared
infrastructure, not a real client or search, so say so plainly when
confirming. Then write a single structured note via
`Ezekia:add_note_to_project` titled "SEARCH SUITE CONFIG (generated
<date>)". Body: the discovered tag taxonomy, custom fields worth knowing,
the off-limits convention, and project-type usage, in a format other
skills can parse (a short markdown table per category works well).
**Confirm with the user before writing either the project or the note** —
these are CRM writes, even though low-risk/infrastructure-only.

## Step 4: stand up the dashboard

Immediately hand off to `pipeline-dashboard` using the scale data gathered
in Step 2, so onboarding ends with something the user can actually look
at — not just a confirmation message.

## Output

A short confirmation of what was discovered and cached, followed by the
dashboard (from Step 4). Tell the user other skills will now use this
cache automatically, and that they can say "refresh setup" any time the
workspace's tags or fields change.

## Not this skill's job

Any actual search work (mapping, reporting, etc.) — this only sets up the
shared foundation those skills read from.
