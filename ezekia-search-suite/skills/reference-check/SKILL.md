---
name: reference-check
description: "Prepare a structured reference-check question set for a candidate, or summarize completed reference call notes into Ezekia. Use when the user asks to 'prep reference questions', 'do a reference check', 'reference call for [candidate]', or has reference call notes/recording to summarize. Covers both directions: before the call (question prep) and after (summarizing what was learned)."
---

# Reference Check

Structures the part of the search process that stays almost entirely
human — the skill's job is prep and synthesis, never judgment on the
candidate.

## Required inputs

The candidate (and their Ezekia record if one exists) and which direction
is needed: **prep questions** before a reference call, or **summarize
notes** after one. If unclear, ask.

## A. Preparing questions

Pull context: candidate's role history (`Ezekia:get_person_details`) and,
if available in the conversation, the position-spec brief's must-have
competencies — reference questions should probe those specifically, not be
generic.

Draft a question set covering:
- **Working relationship** — how they know the candidate, in what capacity,
  how long
- **Core competencies from the brief** — 3-5 targeted questions tied to the
  must-haves, not generic "tell me about their strengths"
- **Leadership/management style** — concrete examples, not ratings
- **Areas for development** — framed to get a real answer, not a
  throwaway
- **Would you work with them again / rehire them** — direct, closing
  question

Keep it to a workable length for a 20-30 minute call — 8-10 questions, not
25.

## B. Summarizing a completed call

From notes, a transcript, or what the user describes: write a tight
summary — not a verbatim transcript — covering: reference's relationship to
candidate, what was confirmed about the competencies probed, any concerns
raised (verbatim-adjacent on anything material — don't soften a real flag),
and an overall read.

Offer to attach it as an Ezekia note on the candidate
(`Ezekia:add_note_to_person`, tagged appropriately per this workspace's
real note tags) — confirm before writing.

## Output

Prep: a clean question list in chat. Summary: the write-up in chat, plus
confirmation before it's saved to Ezekia.

## Not this skill's job

Deciding whether the reference outcome disqualifies the candidate — that's
a human call, always.
