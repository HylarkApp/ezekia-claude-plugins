---
name: call-notes-crm-hygiene
description: "Turn a call, meeting, or Zoom/Teams transcript into a clean Ezekia note, and optionally update the person/project's pipeline stage or custom fields. Use whenever the user says 'log this call', 'add these notes to Ezekia', 'update the CRM from this meeting', 'summarize this transcript into Ezekia', or references a recent call/meeting they want captured. This is foundational CRM hygiene — clean, current records are what make every other search skill (database-mining especially) actually useful, so treat requests to 'quickly note' something from a call as worth doing properly, not skipping."
---

# Call Notes → CRM Hygiene

Keeps Ezekia records current by turning call/meeting content into a
structured note, attached to the right person/company/project — rather
than the raw transcript or a vague one-liner.

## Required inputs

The call/meeting content (Zoom recording/transcript, Teams meeting, or
text the user pastes/describes) and who/what it concerns. If the person or
project isn't obvious from context, ask rather than guessing — a note on
the wrong record is worse than no note.

## Workflow

**A. Get the source content.** If it's a Zoom recording, pull the
transcript/summary via the Zoom connector. If it's a Teams/Outlook meeting,
check for linked notes via Microsoft 365. If the user pastes text directly,
use that.

**B. Identify the record(s).** `Ezekia:search_people` (and
`search_companies` / `search_projects` if relevant) to find the actual
Ezekia record(s) this concerns — match on name + company or email, confirm
if ambiguous.

**C. Write a tight note**, not a transcript dump:
- What was discussed (2-4 sentences, substance not small talk)
- Anything bearing on fit, motivation, availability, or comp expectations
- Any commitment made (follow-up owed, next step, timeline)
- Skip pleasantries and filler

**D. Attach it.** `Ezekia:add_note_to_person` (or `add_note_to_company` /
project-linked, using `context` to link multiple records if the call
touched more than one — e.g. a candidate discussing a specific assignment).
Check the cached config (`search_projects(title="Search Suite Config",
project_type="list")` then `get_notes(entity_type="list", entity_id=<id>,
q="SEARCH SUITE CONFIG")`) for this workspace's real note tags first,
falling back to `Ezekia:get_tags(type='note')` if no cache exists; apply a
tag only if one clearly fits — don't force it.

**E. Offer pipeline/status updates, don't do them silently.** If the call
content suggests a status change (candidate now interested/declined,
moved stage) or a custom field update, propose the specific change and
confirm before calling `update_candidate_status_in_project` or
`update_person_custom_fields` — these are write actions and CRM status
changes have downstream effects (reporting, reminders) the user should
approve explicitly.

## Output

Confirm what was written and where (e.g. "Added a note to [Name]'s Ezekia
record summarizing the call — mentioned they're not ready to move until
Q2. Want me to also update their pipeline stage?"). Don't repeat the full
note text back if it's already visible in the confirmation.

## Not this skill's job

Deciding search strategy from what's learned in the call — just capturing
it cleanly so database-mining and reporting skills can use it later.
