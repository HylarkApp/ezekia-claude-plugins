---
name: position-spec
description: "Draft a position specification / intake brief for a new executive search mandate. Use this whenever the user is starting a new search, mentions an 'intake call', 'position spec', 'role brief', 'search brief', kicking off a new mandate, or asks to scope a new leadership role before mapping candidates. This is the first step of the per-mandate workflow (after search-suite-onboarding has run once for the workspace), before target-company-mapping or executive-mapping run. Produces a structured brief covering why the role is open, 12-month success criteria, must-have vs nice-to-have competencies, team/culture context, and a market compensation benchmark. Push to use this proactively when a client/company name and a role title are mentioned together for the first time in a conversation about a new search."
---

# Position Spec / Intake Brief

Turns a rough ask ("new CFO search for Acme") into the structured brief that
anchors the rest of the search — market mapping, candidate assessment, and
client reporting all trace back to this document. The most common failure
mode in real search work is skipping this step and jumping straight to
sourcing; resist that.

## Required inputs

Client name, role title, and location. If any are missing, ask — don't
guess. Nice-to-haves (ask only if the user has context to give quickly, don't
block on them): reporting line, team size, why the role is open (backfill /
newly created / restructuring), target start date.

## Workflow

**A. Check Ezekia for existing context.** Search for the client company
(`Ezekia:search_companies` or `Ezekia:get_company_details`) and, if a project
already exists for this mandate, pull it (`Ezekia:search_projects` /
`Ezekia:get_project_details`). Reuse anything already on file (past notes,
prior similar searches for this client) instead of asking the user to
repeat it.

**B. Research the role.** Web search for:
- Comparable role scopes at similar-stage/sector companies (what the title
  actually covers at this level, since titles vary a lot company to company)
- Market compensation range for the role, sector, seniority, and location
  (base, bonus/incentive structure, equity norms where relevant) — this
  feeds the brief but should stay a range, not a single number
- Recent context on the client company (funding stage, recent leadership
  changes, public news) that bears on why this role matters now

**C. Draft the brief.** Structure (per the standard executive search intake
format):

1. **Role summary** — title, reporting line, team size/structure, location
   (incl. remote/hybrid expectations)
2. **Why this role is open now** — backfill, growth, restructuring,
   succession; what's driving urgency
3. **12-month success criteria** — 3-5 concrete outcomes, not generic
   responsibilities
4. **Must-have vs nice-to-have competencies** — a clear split, not one long
   list; flag anything that's a hard filter (e.g. regulatory licence,
   language, visa/location constraint)
5. **Team & culture fit notes** — what the hiring manager/team dynamic
   demands
6. **Compensation benchmark** — market range with sources, framed as
   guidance for the client conversation, not a fixed number
7. **Target company / market context** — 2-3 sentences framing where
   candidates are likely to come from (sets up target-company-mapping)

**D. Offer to save.** If a matching Ezekia project exists, offer to write
the brief into its notes or a custom field (check `get_custom_fields` for
`entity_type='projects'` first — don't assume a "Position Spec" field
exists; if none fits, just attach as a note via `Ezekia:add_note_to_project`
after confirming with the user, per the write-back confirmation rule). If no
project exists yet, offer to create one — but confirm the project name/type
with the user first rather than assuming assignment vs opportunity.

## Output

Visualize the brief as a structured document in chat by default (it's a
planning artifact the user will iterate on). Offer a Word doc export
(docx skill) only if the user says this needs to go to the client or hiring
committee as-is.

## Not this skill's job

Building the target company list (target-company-mapping) or finding
candidates (executive-mapping / database-mining) — this skill stops at the
brief.
