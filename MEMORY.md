# Environment
- Windows + PowerShell. Hermes runs as Scheduled Task 'Hermes_Gateway'. Telegram gateway live (bot @Xcerebrobot, allowlisted user 6004053137).
- Primary build tool: Claude Code (CLI: claude); Hermes can run it as a subprocess to kick off builds.

# Focus / active work
- Weight memory here: Surplus Funds/SurplusIQ (FL foreclosure-surplus lead intel), the county-intel Framework engine, and the new Harris build.
- Universal County Intelligence Framework (v5.x): county-agnostic engine, staged pipeline normalize→classify→match→aggregate→score→review→dashboard. No single county is a priority or default — counties are interchangeable framework instances (Bexar = original reference only).
- Harris build: assumed-names (DBA) dashboard, 14k+ records in SQLite, reads live from local DB.

# Conventions
- County builds: pull live from source daily; verify against the live URL, not just local builds, before calling done.
- Motivated-seller scoring weights are fixed (taxdel:30, probate:28, fc:22, lispendens:15, over65:14...). Don't change them silently.
- Don't migrate existing agent stacks into Hermes. Hermes orchestrates; it doesn't replace working systems.
§
# Operating contract (full text: Xcerebro-Vault/operating-rules.md)
- Quentin architects; builder executes. Complete runnable output only (exact commands/full scripts/real paths), never pseudocode. ONE scoped change per verification cycle (hard rule). Report adjacent work separately.
- 'Done' != correct: committed != pushed != tests-green != live != correct. Verify end-to-end against the REAL artifact/live output, not run status. Anti-fabrication absolute: blank/'unknown' over any guess.
- Investigate the real source before building; treat specs as hypotheses. Hard pushback BEFORE mistakes/scope creep. After interruption, check git state first. Keep client names OUT of build-execution prompts.