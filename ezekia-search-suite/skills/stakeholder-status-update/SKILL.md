---
name: stakeholder-status-update
description: "Draft a search-progress / pipeline-health status update for a client or internal stakeholder, pulled live from an Ezekia project. Use when the user asks for a 'status update', 'progress report', 'where do things stand on [search]', 'client update', or 'board update' on an active mandate. Produces a draft the user reviews before sending — never sends on its own."
---

# Stakeholder Status Update

Replaces the "quick status meeting" with a pulled-together digest: what's
moved, what's stuck, what's next — grounded in the actual Ezekia pipeline,
not memory.

## Required inputs

The Ezekia project. If not named, resolve from context
(`Ezekia:search_projects`); ask if ambiguous. Audience (client-facing vs
internal/board) if not stated — changes tone and what's appropriate to
disclose (internal blockers, fee status, etc. stay internal-only).

## Workflow

**A. Pull current state.** `Ezekia:get_project_details` for mandate
context and timeline, `list_project_candidates` for the pipeline. Discover
real stage tags (`get_tags(type='candidate')`) to group people accurately.

**B. Pull what's changed.** `Ezekia:get_notes` / `search_notes` on the
project and its candidates since the last update (ask when that was, or
default to the last ~2 weeks) — new candidates added, stage moves,
interview outcomes, client feedback logged.

**C. Draft the update.** Structure:
1. **Headline** — one line on overall momentum (on track / slipping / at a
   decision point)
2. **Pipeline snapshot** — counts by stage, in the workspace's real stage
   language
3. **Since last update** — what moved and why it matters (not a raw
   activity log)
4. **Next steps / what's needed from the client or team** — be specific;
   vague "continuing to progress" updates are the thing this replaces
5. *(Internal-only)* anything about fee status, internal blockers, or
   staffing — never include in a client-facing draft

**D. Match the channel.** Use `message_compose_v1` to draft as email or
Slack depending on audience/context — don't send it yourself; this always
stops at a draft for the user to review and send.

## Output

A draft message (email or Slack), plus a one-line summary of what it says.
If the user wants it as a standalone doc instead (e.g. for a board pack),
say so and use the docx skill instead of a message draft.

## Not this skill's job

Deciding what to tell the client about sensitive internal matters — surface
the facts, let the user make that call. Never sends messages itself.
