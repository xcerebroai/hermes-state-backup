---
project: xcerebro-county-intel
status: active
client: internal (framework IP)
priority: P1
updated: 2026-06-24
---

# Xcerebro County Intelligence — Framework (core reusable IP)

## Current state
Universal County Intelligence Framework at **v5.5.0**. Canonical repo `xcerebroai/xcerebro-county-intel` (private). Line: v3 → v4 → v5 → v5.5.0. v5.4.0 = rewrite from monolith to staged pipeline (normalize → classify → match → aggregate → score → review → dashboard). v5.5.0 = hardening patch + §1.5 official-venue classifier (real auction venues vs aggregator re-listings). County-agnostic executable engine each county inherits; the only per-county file that changes is `config/counties/<slug>.json`.

## Next action
**Verify v5.5.0 is merged to `main`** (may still be on branch `feat/v5.5.0-framework-hardening`). Merging gates the next county build (#5) AND the older counties' resilience retrofit.

## Open loops
- [ ] Merge v5.5.0 to `main` + tag
- [ ] Greene + Smith resilience retrofit (continue-on-error + preserve-last-good)
- [ ] Verify Duval / Greene / Smith daily refreshes aren't silently stale (Ocean's was red 6 days undetected — same setup, same hidden failure mode)
- [ ] Rotate Smith `mft.smi.tax` SFTP password (compromised in git history)
- [ ] Past-dated-event cleanup on the 3 older boards
- [ ] Reconcile framework version — FRAMEWORK_VERSION.json says v5.4.0, README says v5.3.1, vault note says v5.5.0. Determine source of truth and align all three.
- [ ] **Confirm lis pendens classifier revert was intentional** — local clone HEAD is a `Revert "Add per-state lis pendens classifier..."` (judicial states unchanged, non-judicial LP → review, MD held in review_required pending regime confirmation). Verify the rollback was deliberate before any further work on this repo.

## Waiting on
External blockers — none recorded.

## Log
- 2026-06-24 — Note split out from combined county-intel briefing. Framework-only scope.
- 2026-06-24 — Added version-reconciliation loop: FRAMEWORK_VERSION.json (v5.4.0) / README (v5.3.1) / this note (v5.5.0) disagree (surfaced via a Claude Code repo read).
- 2026-06-24 — Cloned repo to `~/projects/xcerebro-county-intel`; local HEAD is a revert of the per-state lis pendens classifier. Flagged open loop to confirm the rollback was intentional. Repo otherwise left untouched pending that confirmation.

---
**Canonized principles:** source-of-record over aggregators (reseller data = enrichment only, never primary); 8-role source classification; scheduled-event classification (UPCOMING_SALE shown / PAST_SALE excluded); daily refresh must reproduce ENRICHMENT not just lead count (a numerically-correct all-Unknown board must FAIL the publish gate); preserve-last-good + continue-on-error resilience; no client-facing build-status banners; Daniel's-Law redaction is per-source, not uniform; deterministic classification before any semantic step; raw data immutable.

**Build method (repeatable):** 1) Recon first — browse the real county sources before any scraper. 2) Classify every source by role + reliability. 3) Scrape source-of-record into raw immutable data. 4) Enrich owner/contact via DealMachine where source redacts/omits. 5) Score motivation + stack + dedupe. 6) Render PropStream-style dashboard. 7) Automate daily refresh (GitHub Actions cron) with resilience. 8) Operator click-test before ship (headless STATIC_OK misses visual/UX bugs).

**Motivation scoring weights (from Bexar; all counties extend):** taxdel:30, probate:28, fc:22, lispendens:15, over65:14, disabled:12, veteran:10, bk:10, divorce:8, judgment:8, multilien:6, absentee:5, vacant:4, homestead:3, llc:3.

**Live counties** (PRIVATE repos under github.com/xcerebroai, served via GitHub Pages, local `~/Dev/xcerebro/counties/<county>`): Bexar TX (`bexar-intel`, ~287, origin of scoring model) · El Paso TX (`el-paso-intel`, first full client-experience test) · Duval FL (`duval-fl-intel`, ~26,754) · Greene NY (`greene-ny-intel`, 1,210) · Smith TX (`smith-tx-intel`, ~39,453) · Ocean NJ (`ocean-nj-intel`, active build #4 — see ocean-nj-intel.md). Reopen: `cd ~/Dev/xcerebro/counties/<county>` then `claude --dangerously-skip-permissions`.
