---
name: project-note-maintenance
description: Use whenever a project's state changes in conversation — work completed, a decision made, a blocker appears or clears, the next action shifts, or Quentin reports progress on any active project. Keeps the Obsidian project notes in C:\Users\Owner\Documents\Xcerebro-Vault\Projects\ current — stamp the updated date, revise current state, check off completed loops, update next action, append a log line. Fires after substantive project work or any status change, across every project.
version: 1.0.0
author: Quentin Flores
metadata:
  hermes:
    tags: [vault, project-tracking, maintenance, obsidian]
---

# Project Note Maintenance

Keep the Obsidian project vault current automatically. Vault root:
C:\Users\Owner\Documents\Xcerebro-Vault\. Project notes live in Projects\, one
per project; operating-rules.md sits in the root. Quentin should never
hand-update these. When a project's state moves in conversation, update its note
— correctly — so the vault always reflects reality and the morning brief stays
accurate.

## When to use this
Trigger after any of these:
- Work completed on a project (build step done, bug fixed, deliverable shipped).
- A decision that changes direction or next step.
- A blocker appears, clears, or changes.
- The next action shifts.
- Quentin reports progress or status on an active project.

Do NOT trigger for: passing mentions with no state change, hypotheticals, or
questions that don't change status.

## Procedure
1. Identify which EXISTING note the change belongs to. Match to a note already in
   Projects\ — search by project AND product name (a project may have a note under
   a different filename than the term Quentin just used). Do NOT create a duplicate.
   Only create a new note for a genuinely
   new project, using the standard template at
   templates/project-note.md (load it via skill_view file_path; copy its exact
   frontmatter + section structure, set updated: and the Log date to today).
2. Read the current note before editing. Patch it; never overwrite the whole file.
3. Apply only the fields that actually changed:
   - updated: → today's date, every time.
   - ## Current state → revise if changed.
   - ## Next action → update if the next step shifted.
   - ## Open loops → check off [x] completed; add newly emerged ones.
   - ## Waiting on → update blockers.
   - ## Log → append one dated line describing what changed.
4. Then briefly tell Quentin which note you updated and what changed.

## Pitfalls
- NEVER fabricate or guess current state or next action. If you can't determine
  the new state, ask — don't invent it.
- BRACKETED-PLACEHOLDER TRAP: if Quentin pastes a value that's still a template
  placeholder (e.g. `[where it actually stands — one line]`, `<today>`,
  `[the single next thing to do]`), that is NOT a real value. Do not write literal
  brackets into the note — leave the field as NEEDS INPUT and ask him for the real
  line. He has done this; it's a real failure mode.
- DON'T AUTO-CORRECT A FIELD YOU CAN'T VERIFY. If a field (e.g. a version number)
  conflicts with another source but you don't know which is authoritative, do NOT
  silently rewrite it to match. Leave it and add an Open loop to reconcile. Guessing
  the source of truth is just a different fabrication.
- NEVER elevate a single county/project as default priority. Counties are
  interchangeable framework instances; no single county is the default.
- NEVER create a duplicate note. One note per project. RENAME ≠ NEW: if asked to
  create a note for a project that already has one under a different name, flag the
  existing note and confirm rename/delete BEFORE creating a second file. Quentin
  renames projects mid-stream (e.g. surplus-funds → surplusiq); match on the
  project, not the literal filename.
- NEVER fold unrelated changes into a note. One project's changes, one note.
- Keep client confidentiality; don't add business context that doesn't belong.

## Verification
After editing, re-read the note: updated date is today, changed fields match what
was actually said, log line appended, nothing else disturbed.
