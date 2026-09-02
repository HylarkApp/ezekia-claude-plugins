---
name: longlist-shortlist-report
description: "Generate a client-ready longlist or shortlist report from an Ezekia project's candidate pipeline. Use when the user asks for a 'shortlist report', 'longlist report', 'client presentation', 'candidate report', or wants to share where a search stands with a client or board. Pulls live candidate and pipeline data from Ezekia rather than a spreadsheet handoff, and produces a polished document — push to use this whenever the user mentions presenting or sharing candidates with a client, even if they don't say 'report' explicitly."
---

# Longlist / Shortlist Report

Turns an Ezekia project's live candidate pipeline into a client-ready
document — the deliverable that actually gets sent, not an internal
working table.

## Required inputs

The Ezekia project (assignment/opportunity). If not named, search for it
(`Ezekia:search_projects`) using client/role context already in the
conversation; ask if it can't be resolved to one project. Ask whether this
is a **longlist** (broad, all candidates under consideration) or
**shortlist** (narrowed, "Recommended"/"Not recommended" framing) if not
stated — the two have different structure (see below).

## Workflow

**A. Pull the pipeline.** `Ezekia:get_project_details` for the mandate
context (role, client, timeline), then `Ezekia:list_project_candidates` for
everyone on it. For each candidate, pull enough `get_person_details` to
write a real profile: career summary, current role, relevant achievements,
and — for a shortlist — any interview feedback captured in notes.

**B. Load this workspace's pipeline-stage tags.** Check for a cached
config (`Ezekia:search_projects(title="Search Suite Config",
project_type="list")` then `get_notes(entity_type="list", entity_id=<id>,
q="SEARCH SUITE CONFIG")`) before calling `get_tags(type='candidate')`
directly — use the real stage labels this workspace has configured to
sort/group candidates; don't assume generic stage names.

**C. Draft the report.**

*Longlist* — one section per candidate: name, current role/company,
2-3 sentence background summary, why they're a fit for the brief, current
pipeline stage. Group by tier if tier data is available from an earlier
mapping step.

*Shortlist* — split into Recommended / Not Recommended (or whatever this
workspace's real terminal-stage tags indicate), each candidate getting a
fuller write-up: background, fit assessment against the position-spec
competencies if one exists in the conversation, interview feedback summary
from notes, and a clear recommendation line.

Keep the client's voice in mind — this is external-facing. No internal CRM
admin (owner names, created-at dates, hygiene tags) leaks into it.

**D. Export.** This is a document meant to leave the building — use the
docx skill (read its SKILL.md first) to produce a proper Word document
rather than a chat artifact, unless the user asks for something else
(e.g. a slide-based version, in which case use the pptx skill instead).
Confirm the title/client name in the header before finalizing.

## Output

A formatted Word document (or deck, if requested), saved to
`/mnt/user-data/outputs` and presented via `present_files`. Give a one-line
summary in chat (e.g. "Shortlist report for 6 candidates: 4 recommended, 2
not") rather than repeating the whole report inline.

## Not this skill's job

Deciding who belongs on the pipeline (that happens upstream, in
executive-mapping / manual client review) or updating Ezekia — this is
read-only against the CRM.
