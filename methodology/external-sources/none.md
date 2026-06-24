# External Data Sources — NONE feed the calculations

**Verified by Jan's Claude, 2026-06-24.**

## Statement (unambiguous, for Zig)

**No external data source feeds any dashboard calculation.** Every number in all three dashboards — Forecast, Deal Working (net-new), and Renewals — is computed from **live HubSpot data only** (portal `39855019`).

There is **no** Excel spreadsheet, Google Sheet, Google Shared Drive file, SharePoint file, CSV, or manual override that is read, cross-referenced, or reconciled to produce any figure (banked, weighted forecast, best case, ceiling, total pipeline, gap to $1M). **Zig should look nowhere beyond HubSpot for the numbers.**

The legacy ESC 16 renewal tracker (Google Sheet `1cvSeIp88SGTKcOVLXgws54aqOBq4qbYnYpaBOiK5wqc`, Confluence page `95518739`) **was deprecated on 2026-06-11.** HubSpot became the single renewal source of truth on that date. Do **not** resurrect that tracker as a data input.

---

## Annotation-only context (does NOT affect any calculation)

For completeness and honesty: a few **narrative annotations** in the dashboards reference outside context. These are commentary that helps a human read the numbers — they are **not inputs to any formula** and Zig must not wire them into the math.

| Annotation | Where it appears | Outside reference | Affects calc? |
|---|---|---|---|
| "ILTexas ~$1.27M unspent IMTA" (at-risk flag) | Forecast / Renewals narrative | TEA IMTA (Instructional Materials & Technology Allotment) balances — local PDF, SY 2026-27 (in `~/Downloads`, summarized in bookmarked-os memory `reference_tima_funding_data.md`) | No — context only |
| Student-enrollment-based pricing context ($3/student) | Pricing discussion only | District enrollment figures | No — renewal amounts come straight from HubSpot `amount`; pricing is intentionally NOT recomputed |
| Migration / adoption context (e.g. "migrating to V2 for SB 13") | Renewals narrative | bookmarked-os V1→V2 migration notes | No — narrative only |

If any of these ever needs to become a real calculation input, that is a **methodology change** — it must be added to `config.json`, documented here, logged in `changelog/CHANGELOG.md`, and ratified by Jan. As of 2026-06-24, none are.
