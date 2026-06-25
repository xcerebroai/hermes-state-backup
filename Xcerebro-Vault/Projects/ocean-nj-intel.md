---
project: ocean-nj-intel
status: active
client: internal build for unnamed client
priority: P2
updated: 2026-06-24
---

# Ocean County NJ — Intelligence Build (county #4)

## Current state
Repo `xcerebroai/ocean-nj-intel`. Dashboard LIVE: **https://xcerebroai.github.io/ocean-nj-intel/dashboard/**. PropStream-style board (lead-type tabs, motivation pills, combinable filters, structured cards, filter-aware CSV export); rebuilt at commit c88f5cd after operator found filters/cards broken. Daily refresh cron `0 6 * * *` UTC is GREEN and self-sustaining. Three distinct lead populations on disk (keep straight): **538 source-of-record** (504 HLS Brick tax-default + 32 sheriff foreclosure + 2 NJPA; 257 of these are contact-enriched and pushed to GHL) · **3,959 DealMachine commercial-list** (vacant 1,173 / expired 1,681 / HOA 1,097 / zombie 8 — softer inferred signals, Ocean-only, NOT framework canon; have addresses but NO contact info on disk) · **1,392 probate research-targets** (no address, default-hidden toggle).

## Next action
**Enrich the 3,959 DealMachine list leads** (skip-trace for name/phone/email). Operator approved spending the credits. FIRST confirm DealMachine's enrichment API has recovered from its outage with ONE lightweight test call — do not loop-retry into the outage. (The actual GHL push of the enriched leads is tracked in ocean-ghl-push.md.)

## Open loops
- [ ] Decide: enrich all four lists or drop `expired_listing` (1,681 — softest signal, ~40% of credit cost)
- [ ] Past-dated-event cleanup on the board

## Waiting on
- **DealMachine enrichment API** — was down 3 days (06-20/21/22: 504s + Prisma timeouts). Confirm recovered before the enrichment run. DM account "Infinity Gauntlet LLC", org 5702, county 34029, ~91k credits at last check. DM has ZERO tax-delinquent data for Ocean (confirms Brick source-of-record is irreplaceable); DM `enrich` ignores field selection (flags only via `properties search`); senior/tired-landlord are owner-attribute flags, not lead lists.

## Log
- 2026-06-24 — Note split out from combined county-intel briefing. Ocean build scope.
- Earlier — Daily refresh silently failed 6 days on a PYTHONPATH bug ("No module named scrapers"), fixed (commit 9a2eab2). Daily CI push keeps the repo active so GitHub won't auto-disable the schedule. Uses legacy branch-based Pages (git push deploys; redundant actions/deploy-pages step removed).
