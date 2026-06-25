---
name: obsidian
description: Read, search, create, and edit notes in the Obsidian vault.
platforms: [linux, macos, windows]
---

# Obsidian Vault

Use this skill for filesystem-first Obsidian vault work: reading notes, listing notes, searching note files, creating notes, appending content, and adding wikilinks.

## Vault path

Use a known or resolved vault path before calling file tools.

The documented vault-path convention is the `OBSIDIAN_VAULT_PATH` environment variable, for example from `${HERMES_HOME:-~/.hermes}/.env`. If it is unset, use `~/Documents/Obsidian Vault`.

File tools do not expand shell variables. Do not pass paths containing `$OBSIDIAN_VAULT_PATH` to `read_file`, `write_file`, `patch`, or `search_files`; resolve the vault path first and pass a concrete absolute path. Vault paths may contain spaces, which is another reason to prefer file tools over shell commands.

If the vault path is unknown, `terminal` is acceptable for resolving `OBSIDIAN_VAULT_PATH` or checking whether the fallback path exists. Once the path is known, switch back to file tools.

## Read a note

Use `read_file` with the resolved absolute path to the note. Prefer this over `cat` because it provides line numbers and pagination.

## List notes

Use `search_files` with `target: "files"` and the resolved vault path. Prefer this over `find` or `ls`.

- To list all markdown notes, use `pattern: "*.md"` under the vault path.
- To list a subfolder, search under that subfolder's absolute path.

## Search

Use `search_files` for both filename and content searches. Prefer this over `grep`, `find`, or `ls`.

- For filenames, use `search_files` with `target: "files"` and a filename `pattern`.
- For note contents, use `search_files` with `target: "content"`, the content regex as `pattern`, and `file_glob: "*.md"` when you want to restrict matches to markdown notes.

## Create a note

Use `write_file` with the resolved absolute path and the full markdown content. Prefer this over shell heredocs or `echo` because it avoids shell quoting issues and returns structured results.

## Append to a note

Prefer a native file-tool workflow when it is not awkward:

- Read the target note with `read_file`.
- Use `patch` for an anchored append when there is stable context, such as adding a section after an existing heading or appending before a known trailing block.
- Use `write_file` when rewriting the whole note is clearer than constructing a fragile patch.

For an anchored append with `patch`, replace the anchor with the anchor plus the new content.

For a simple append with no stable context, `terminal` is acceptable if it is the clearest safe option.

## Targeted edits

Use `patch` for focused note changes when the current content gives you stable context. Prefer this over shell text rewriting.

## Wikilinks

Obsidian links notes with `[[Note Name]]` syntax. When creating notes, use these to link related content.

## Project notes (status-tracking pattern)

When the vault tracks active projects (e.g. a `Projects/` folder whose notes feed a morning brief), use a consistent template per note:

```markdown
---
project: <slug>
status: active        # active | blocked | shipped | paused
client: <name or internal>
priority: P1          # P0 | P1 | P2
updated: <YYYY-MM-DD>
---

# <Project Name>

## Current state
One line: where this stands right now.

## Next action
The single next thing to do. ← what a morning brief surfaces.

## Open loops
- [ ] pending item

## Waiting on
External blockers — who/what you're waiting for.

## Log
- <YYYY-MM-DD> — what changed.
```

Rules that make these notes useful:

- **One trackable thread per note. Split, don't bundle.** If a "project" actually contains independent workstreams that each have their own next action (e.g. a framework + a specific build + a downstream push), write a separate note per thread and cross-link them. A single note with three tangled next-actions defeats the morning brief.
- **Cross-link dependencies instead of duplicating.** When note B is gated on note A, put the dependency in B's `## Waiting on` pointing at A by filename, and reference A for shared context — don't copy A's detail into B.
- **NEVER guess `## Current state` or `## Next action`.** These are the two fields a brief acts on. If you don't actually know them, write `NEEDS INPUT — not recorded` and ask, rather than inventing a plausible-sounding status. A fabricated next-action is worse than a blank one. (Other fields like priority/client may be inferred, but flag the inference in your reply.)
- **Keep client business-context out of build/execution artifacts** but it's fine in an architect-level vault note — flag the name as "confirm" if you inferred it.
- **A non-project doc is not a project note.** An operating brief / how-I-work doc has no current-state or next-action; don't force it into the template or drop it in `Projects/`. Save it to the vault root as a standalone reference and link it.

## Pitfalls

- `patch` warns if you edit a file you only read with offset/limit (partial view). Re-read the whole file before a `write_file` overwrite; for small anchored edits the `patch` still applies, but heed the warning before full rewrites.
- File tools don't expand `~` inconsistently across platforms here — pass concrete absolute paths (e.g. `C:\Users\<user>\Documents\<Vault>\...` on Windows).
