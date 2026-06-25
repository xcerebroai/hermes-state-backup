---
name: county-build-recon
description: "Use when starting a new county intelligence build, scraping any county data source (recorder, clerk, assessor, appraisal district, tax collector, code enforcement, courts), adding a county to the framework, or building/fixing a PropStream or DealMachine-style lead-gen dashboard from county records. Enforces the operator doctrine: recon before any scraper code, deterministic classification before semantic, raw data immutable, live-URL verification before done."
version: 1.1.0
author: Quentin Flores
metadata:
  hermes:
    tags: [REI, county-intel, scraping, lead-gen]
---

# County Intelligence Build — Operator Procedure

How Quentin builds a county lead-generation system. The moat is daily pulls of
live county source data (recorder, assessor, tax collector, code enforcement,
courts) versus stale monthly reseller extracts. Follow the phases in order. The
gates are non-negotiable.

## When to use this
- Starting a new county build or adding a county to the Universal County
  Intelligence Framework.
- Writing any scraper against a county source.
- Building or fixing a county lead dashboard.
- Any task that turns county records into leads.

## The rule that overrides everything
Recon before code. Never write a line of scraper code until you have manually
opened the live source in a browser and confirmed: what loads, how it paginates,
what blocks it (CAPTCHA, WAF, JS-rendered tables), and exactly which fields
exist. Writing the scraper first and learning the source shape later is the most
expensive mistake. If you're writing a parser before you've seen the live page,
stop.

## Procedure

### Phase 0 — Source recon (manual, no code)
1. Open each target source live: Register/Recorder of Deeds, County Clerk,
   Appraisal District/Assessor, Tax Collector, Code Enforcement, District/County
   Courts.
2. For each, record: URL, how results load (static HTML / JS-rendered / API /
   ArcGIS REST endpoint), pagination, and any block (reCAPTCHA, Cloudflare WAF,
   login wall).
3. Probe for a hidden API first. County GIS/assessor sites are often backed by
   ArcGIS REST endpoints — find the endpoint and you skip HTML scraping entirely.
4. Read the exact field names off the live page. Do not assume them.
5. Classify each source P0/P1/P2 (P0 = primary source of record) and assign an
   A–E reliability grade.

### Phase 1 — Pull (raw, immutable)
1. Pull live from source. Store the raw response exactly as received. Raw data is
   immutable — never edit in place; all transforms write to new tables.
2. Scrapers run daily. Freshness is the product.

### Phase 2 — Normalize, classify, match, score
1. Normalize raw records to the common schema.
2. Classify deterministically first (rules, patterns, known field values). Reach
   for semantic/LLM classification only when deterministic rules genuinely can't
   decide.
3. Match and stack records across sources to the same parcel/owner; boost score
   on stacked signals.
4. Score with the FIXED motivated-seller weights — do not change silently:
   taxdel:30, probate:28, fc:22, lispendens:15, over65:14, disabled:12,
   veteran:10, bk:10, divorce:8, judgment:8, multilien:6, absentee:5, vacant:4,
   homestead:3, llc:3.

### Phase 3 — Review and dashboard
1. PropStream/DealMachine-style UX with operator-language tags (Foreclosure, Tax
   Delinquency, Probate, Demolition Order), not internal pattern codes.
2. Provide Skip Trace and GHL CSV export formats.

## Verification — the closing gate
A build is NOT done until verified against the LIVE deployed URL, not the local
build. Run Playwright (or equivalent) against the live user-facing URL: confirm
the dashboard renders, filters work, and lead counts are non-zero and correct.
"Works locally" is not done. Most silent failures — stale data, broken
rendering, empty filters — only show on the live URL.

### "Done" is not "correct" — five states that diverge
Committed, pushed, tests-green, live, and correct are five DIFFERENT states that
drift apart constantly. Real failures that have bitten this operator: code
committed but never pushed (thought live, wasn't); tests passing 35/35 while the
deliverable was wrong because tests covered classification but not the display
layer; a fix correct at every layer except the final export step the dashboard
actually reads; a dollar figure overstated for weeks because a string-prefix
mismatch silently bypassed the fix. So when you report something done, pre-empt
the operator's own verification: pull the REAL artifact (published data, rendered
output) and show what it actually contains — "the run went green" is not evidence
the deliverable is correct. The daily refresh must reproduce ENRICHMENT, not just
lead count: a numerically-correct all-Unknown board must FAIL the publish gate.

## Pitfalls
- Writing scraper code before manual recon.
- Trusting a local build instead of checking the live URL.
- Changing scoring weights without flagging it.
- Leaving `__pycache__` after replacing a scraper file — delete it; stale
  bytecode causes confusing failures.
- Pasting multi-line Python into the shell — write it to a file and run the file.
- CI scrapers failing on reCAPTCHA with no display — use a virtual display
  (xvfb-run) on Linux runners.
- Using semantic classification where a deterministic rule would be exact.
- CRM trigger-tag trap (e.g. GoHighLevel): an upsert REPLACES the contact's tags
  array. If a tag fires an automation (an SMS workflow), stripping then re-adding
  it RE-FIRES the automation — one bulk upsert re-blasted ~231 duplicate texts to
  already-contacted owners. Add/keep trigger tags via the additive tag endpoint
  (`POST /contacts/{id}/tags`), never in the upsert body. Separate the silent tag
  (no automation) from the trigger tag, push net-new only, and write the
  pushed-keys ledger ONLY on a 2xx so failures retry instead of being lost.
- Default `python-urllib` User-Agent gets Cloudflare-1010 blocked by some lead
  CRMs — set a real browser User-Agent header on those API calls.