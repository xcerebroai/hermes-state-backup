---
project: ocean-ghl-push
status: active
client: internal build for unnamed client
priority: P2
updated: 2026-06-24
---

# Ocean → GHL Auto-Text Push

## Current state
Pushes new Ocean leads into the client's GHL so their workflow texts owners. Direct API, no n8n. Build committed in repo `ocean-nj-intel`: `scrapers/ghl_diff_new_leads.py` (diffs actionable leads vs `data/exports/pushed_keys.json`, key = parcel/block-lot | docket; emits only never-pushed leads WITH phone/email); `scrapers/ghl_push.py` + `scrapers/ghl_bulk_push_ocean_county.py` (upsert to `POST https://services.leadconnectorhq.com/contacts/upsert`, Bearer `GHL_TOKEN`, `Version: 2021-07-28`, real User-Agent header — default Python-urllib got Cloudflare-1010 blocked; writes ledger ONLY on 2xx so failures retry; runs inside daily cron → auto-pushes net-new daily). **257 source-of-record contacts live in GHL**, tagged `ocean county` + `ocean-new-lead`. Decision: primary phone only.

## Next action
**Push the (newly enriched) 3,959 list leads' contactable records to GHL tagged `ocean county` ONLY — NEVER `ocean-new-lead`** (that tag fires the client's SMS workflow; tagging ~4,000 leads would blast ~4,000 texts and blow the A2P cap). These go in as silent contacts; texting them is a separate paced decision. Gated on the Ocean enrichment run completing (see ocean-nj-intel.md). Run: `gh workflow run ocean-county-bulk-push -f push_live=true` (only after enrichment done + API confirmed up).

## Open loops
- [ ] Optional: split `ocean-new-lead` into `ocean-tax-default` / `ocean-sheriff` if client wants branched SMS copy
- [ ] Decide paced rollout for texting the silent (list-lead) contacts later

## Waiting on
- Ocean enrichment run (the 3,959 list leads have 0 contact info on disk — nothing to push until skip-trace completes). See ocean-nj-intel.md.

## Log
- 2026-06-24 — Note split out from combined county-intel briefing. GHL-push scope.
- 06-21 — INCIDENT: a bulk upsert stripped `ocean-new-lead` off the 257 SoR contacts then re-added it → re-fired the SMS workflow → ~231 duplicate texts. Cause understood; fix shipped (additive tagging via `POST /contacts/{id}/tags`, commit bc9bc27). GHL number owner should know (opt-out/complaint risk).

---
**Tagging — CRITICAL:** `ocean-new-lead` = the tag the client's SMS workflow FIRES on (adding it sends a text). `ocean county` (GHL force-lowercases) = silent tag, does NOT trigger SMS. Upsert REPLACES the tags array → always add tags via the additive `POST /contacts/{id}/tags` endpoint, NEVER in the upsert body. Secrets (GitHub Actions): `GHL_TOKEN`, `GHL_LOCATION_ID` (=7qy3konkBt3Z17STRAet), `DEALMACHINE_API_KEY`.
