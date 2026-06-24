# Sales Dashboard Methodology

**Status:** COMPLETE — documented by Jan's Claude, 2026-06-24
**Owner (going forward):** Zig (CRO Agent, Bookmarked Hermes Fleet)
**Source of truth:** HubSpot portal **39855019** (Bookmarked, USD, America/Chicago)
**Confluence mirror:** https://bookmarked-team.atlassian.net/wiki/spaces/SH/pages/155222017

> This document records exactly how Jan's Claude currently builds the three sales dashboards — Forecast, Deal Working (net-new), and Renewals — so Zig can replicate and automate them. Numbers cited as "as of 2026-06-22/24" are the live values that the published dashboards produced; they are examples of correct output, not hardcoded constants. The **rules, IDs, weights, and formulas** are what Zig must reproduce.

---

## 0. TL;DR for Zig

1. Pull live deals from HubSpot for the **3 included pipelines** (§1).
2. Exclude per the **exclusion rules** (§4) — Testing, junk, Parked/Revisit, Closed Lost, consortium pipeline, FY27-out-of-window for the goal math.
3. Map each open deal's stage to a **weight** (§3) and run the **5 formulas** (§5).
4. Honor the **edge cases** (§8) — Conroe multi-year, $0 consortium, Krum $0, no-close-date deals.
5. There are **no external data sources** feeding the numbers — HubSpot only (§6).
6. **Both prior open items are now resolved (§3):** dashboard weights are canonical (align HubSpot to them), and "Quote Requested" = 60%. Remaining mechanical step for Zig: capture the "Quote Requested" per-pipeline stage IDs from REST.

---

## 1. HubSpot Pipelines

Bookmarked restructured into **7 pipelines by motion + segment** on 2026-06-22 (built by Zig, verified via REST `/crm/v3/pipelines/deals`). The 6 revenue pipelines share one identical 9-stage spine; ESC Partnerships is separate.

**Pipelines the dashboard PULLS FROM (counts toward $1M-by-Sept-1):**

| Pipeline | Pipeline ID | In $1M goal? | Why |
|---|---|---|---|
| New Business — Texas | `default` | **Yes** | Primary TX net-new. Drives the Deal Working dashboard. |
| 26/27 Renewals | `798674460` | **Yes** | Paid 26/27 renewals. Drives the Renewals dashboard. |
| New Business — Out of State | `67437740` | **Partial** | Added 2026-06-22. Only in-window deals (close < 2026-09-01) count. As of 6/24 that's Fort Vermilion AB ($6,148) only. Utah ($1.31M, Jan '27) + Levy FL ($15K, Mar '27) are FY27 — real book, excluded from the Sept-1 goal. |

**Pipelines DELIBERATELY EXCLUDED from the dashboard:**

| Pipeline | Pipeline ID | Why excluded |
|---|---|---|
| Renewals — Consortium | `2365820611` | ~25+ ESC 9/15/16 child districts at $0. Separate structure decision. |
| Renewals — Testing & Pilot | `2366316271` | Test/pilot records. |
| New Business — Testing | `41834638` | Old sandbox pipeline polluted with 2023 `Testing:` junk (~$366K Closed Lost / ~$81K Closed Won incl. "Testing: Abilene" $61K). |
| ESC Partnerships | `61762106` | Partnership (ILA/PD) motion, not quote→PO→close. Not part of revenue forecast. |
| 27/28 Renewals | `2231817917` | FY28 + multi-year Year-N PO-tracking deals (e.g. Conroe Year 2). Not migrated to new spine. |
| 25/26 Renewals (historical) | `688408429` | Historical/closed. Use only to find the $0 consortium customer base. |
| 24/25 Renewals (historical) | `98147764` | Historical/closed. |

> **Banked is pulled across ALL real revenue pipelines** (not just the 3 above) for any Closed-Won since 2025-09-01 — but Testing pipelines and `Testing:` deals are always excluded.

---

## 2. HubSpot Deal Properties Queried

| Property key | Used for |
|---|---|
| `dealname` | Display. Renewal naming: `YY/YY RENEWAL | District Name [suffix]`; multi-year appends `(Year N of Nyr MY)`. |
| `amount` | Dollar value. Renewal amounts are intentionally conservative (NOT $3/student) — pricing flex preserved. |
| `closedate` | Drives banked (≥ 2025-09-01 + Closed Won) and best-case (open deal close < 2026-09-01). |
| `dealstage` | Stage ID → weight (§3). Stage IDs differ per pipeline (§3 table). |
| `pipeline` | Which pipeline the deal is in. |
| `hubspot_owner_id` | Owner. Map from verified IDs (below) — never infer from context. |
| `dealtype` | CFO (Michelle) owns it. Enum: New Business, Existing Business, Pilot, Consortium, Testing, Region Center Partnership. Used for the "Testing" exclusion. Do NOT change its options. |
| `hs_deal_stage_probability` | HubSpot's own stored win-prob. **Jan's dashboards do NOT use this** — they apply the fixed table in §3. The two diverge. |

**Verified owner IDs (account 39855019):** Steve `479487253`, Teela `436425345`, Jan `83343072`, Natasha `83027884`, Patrick `81631149`, Michelle `681243224`.

No custom properties beyond the standard set above are required to reproduce the dashboards. Renewal date bookkeeping uses `contract_start_date` / `contract_end_date` (see §8).

---

## 3. Stage Weighting Table

The 6 revenue pipelines share the same 9-stage spine (labels + order). **Jan's Claude applies these fixed weights** for the Weighted Forecast:

| Stage label | Weight | Notes |
|---|---|---|
| Qualifying | **10%** | Earliest customer-action stage. |
| V2 Review/Demo | **35%** | Demo + V2 review (same moment). |
| Decision-Maker Buy-In | **50%** | DM sold on value. |
| Quote Requested | **60%** | Customer asked for a quote; not yet sent. Added & weighted 2026-06-24. |
| Quote Sent | **65%** | Quote delivered to customer. |
| Committed | **80%** | Customer verbal/written "yes, moving forward" / district confirmed renewing. |
| PO Received | **90%** | PO in hand; contract awaiting counter-signature (the 60–90 day cycle lives here). |
| Closed Won | **100%** | Banked. |
| Closed Lost | **0%** | Excluded. |
| Parked/Revisit | **0%** | Deliberately deferred (REVISIT AFTER SEPT 2026). Shown separately, not in forecast. |
| *(unmapped) "Early — verify stage"* | **20% (provisional)** | Data-quality placeholder for deals stuck in an unmapped stage (NB-Texas `3879394040`). Not a real stage. |

### Stage IDs per pipeline (use labels in reports; never display raw IDs)

**26/27 Renewals (`798674460`):** Qualifying `1172161286` · V2 Review/Demo `3577701096` · Decision-Maker Buy-In `1172161287` · Quote Sent `1172161288` · Committed `3755871958` · PO Received `1172161289` · Closed Won `1172161290` · Closed Lost `1172161292` · Parked/Revisit `3583616757`

**New Business — Texas (`default`):** Qualifying `presentationscheduled` · V2 Review/Demo `979770642` · Decision-Maker Buy-In `decisionmakerboughtin` · Quote Sent `contractsent` · Committed `232657518` · PO Received `135960983` · Closed Won `closedwon` · Closed Lost `closedlost` · Parked/Revisit `1214475888` · unmapped "Early-verify" `3879394040`

**Consortium `2365820611`, Renewals-Testing `2366316271`, OOS `67437740`, NB-Testing `41834638`:** same labels, pipeline-specific IDs — re-pull from REST. **OOS stage IDs are unreliable via the API**, so all 3 real OOS deals are shown at the earliest stage ("Qualifying (OOS — verify)").

### ✅ RESOLVED — Weight divergence (Jan ratified dashboard weights, 2026-06-24)

Jan's dashboards and HubSpot's stored win-probs were **not the same**:

| | Qual | V2 | DM | Quote | Committed | PO | Won | Lost | Parked |
|---|---|---|---|---|---|---|---|---|---|
| **Jan's dashboard — CANONICAL** | 10% | 35% | 50% | 65% | 80% | 90% | 100% | 0% | 0% |
| HubSpot stored probs (6/22) — to be overwritten | 10% | 25% | 40% | 60% | 85% | 95% | 100% | 0% | 10% |

**Decision (Jan, 2026-06-24):** keep the **dashboard weights** as canonical — they produced the published **$738,991 weighted forecast**. **Action for Zig:** set HubSpot's `hs_deal_stage_probability` on every stage to **match the dashboard weights** across all 6 revenue pipelines. Do **not** adopt the lower stored HubSpot probs and do **not** re-baseline the forecast to them. The dashboard table is the source of truth; HubSpot gets aligned to it.

### ✅ RESOLVED — "Quote Requested" stage (Jan set 60%, 2026-06-24)

The "Quote Requested" stage added 2026-06-24 is weighted **60%** — it sits between Decision-Maker Buy-In (50%) and Quote Sent (65%) (customer asked for a quote, not yet sent). **Action for Zig:** apply 0.60 to this stage; capture its per-pipeline stage IDs from REST and add them to the stage-ID maps before automating.

---

## 4. Exclusion Rules

| Rule | Condition | Why |
|---|---|---|
| Testing deal type | `dealtype = Testing` OR name starts with `Testing:` | Sandbox/test (Canyon $17K, Splendora $7.8K, Testing: Abilene $61K). Excluded everywhere incl. banked. |
| Testing pipelines | pipeline ∈ {NB-Testing `41834638`, Renewals-Testing `2366316271`} | Non-real. |
| Parked/Revisit stage | `dealstage = Parked/Revisit` | Deliberately deferred, contingent on V1→V2 parity. Not at-risk, not lost. Killeen, Belton, Argyle, Dalhart, Temple (~$78,734). Excluded from open + weighted; shown separately at 0%. |
| Closed Lost / churned | `dealstage = Closed Lost` | Lost this cycle: Dripping Springs $15,694, New Braunfels $8,899, Smithville $2,751. |
| FY27 out-of-window | `closedate ≥ 2026-09-01` | Real book but outside the goal window (Utah $1.31M, Levy $15K). Excluded from goal/best-case; still in total open book. |
| ESC consortium pipeline | pipeline = Consortium `2365820611` | $0 consortium districts; separate structure. |
| Stale/junk OOS | known abandoned/$2 test records | Jefferson County FL ($1,500 abandoned); Jefferson Monticello / Wyoming City / McAlester ($2 tests). |
| Duplicates | multiple records, same district | Keep canonical, drop dupe (e.g. Jefferson County FL $2 vs $1,500). `[DUPE]` deals are auto-created without dedup by the `Hubspot_Code_MCP` app + the "RENEWAL Deal Creation" workflow. |
| $0 amount | `amount = 0` | **Case-by-case.** Some $0 are VALID (Krum $0 intentional; consortium $0 districts are real customers). $0 contributes $0 to all math, so it never distorts totals. |

---

## 5. Calculation Formulas

Let `Banked`, and for open deals let `weight` = stage weight from §3.

### Banked Revenue
```
Banked = SUM(amount)
         WHERE dealstage = "Closed Won"
           AND closedate >= 2025-09-01
         across all real revenue pipelines
         (exclude Testing pipelines + any "Testing:" deals)
```
As of 2026-06-22: **$379,297** (incl. Conroe 3-Yr Year-1 $206,785 ≈ 55% of banked). The headline goal metric. No weighting — won = 100%.

### Weighted Forecast (the working number)
```
Weighted Forecast = Banked + SUM(open_deal.amount × stage_weight)
                    for all open deals in included pipelines
```
As of 2026-06-22: **$738,991** (Banked $379,297 + Weighted Open $359,694). Every open deal counted at its stage's win-probability — no manual calls.

### High-Confidence Floor
```
Floor = Banked + SUM(amount × weight) for open deals in Committed + PO Received only
```
As of 2026-06-22: **$464,529**.

### Best Case (pre-Sept-1)
```
Best Case = Banked + SUM(open_deal.amount)        ← at 100%, NOT weighted
            for every open deal WHERE closedate < 2026-09-01
```
As of 2026-06-22: **$1,287,674** ($908,377 of open pipeline was in-window). Deals with **no close date** cannot enter this calc.

### Ceiling
```
Ceiling = Banked + SUM(ALL open pipeline)         ← 100%, no date filter
```
As of 2026-06-22: **$1,441,843**.

### Total Open Pipeline
```
Total Open = SUM(open_deal.amount), excluding Parked/Revisit
```
As of 2026-06-22: **$1,062,546** (headline, excludes the $1,327,622 FY27 OOS book). Total open book incl. all OOS = **$2,396,316**.

### Gap to $1M
```
Gap = 1,000,000 − Banked
```
As of 2026-06-22: **$620,703**.

---

## 6. Data Sources Outside HubSpot

**None feed any calculation.** Every number in all three dashboards is computed from live HubSpot data only. The legacy ESC 16 renewal tracker (Google Sheet) was **deprecated 2026-06-11** — HubSpot is now the single renewal source of truth.

Some narrative annotations reference outside context (e.g. "ILTexas ~$1.27M unspent IMTA") but these are **annotation-only and affect zero calculations.** They are documented for completeness in `methodology/external-sources/` (see `none.md` there). Zig should look **nowhere beyond HubSpot** for the numbers.

---

## 7. Fiscal Year

**FY26 = Sept 1, 2025 → Aug 31, 2026.** Confirmed (printed on the Forecast dashboard). The $1M goal is banked-by-Sept-1-2026.

---

## 8. Edge Cases & Judgment Calls

- **Conroe multi-year:** 3-year deal ($206,784.60). Year-1 is Closed-Won, counts in banked (~55% of it) — flag that banked is concentrated in this one deal so it's not read as broad momentum. Year-2/Year-3 are PO-tracking deals in the 27/28 pipeline (Year 2 id `323074610930`), **not** real renewals; don't treat as at-risk. Stays `dealtype = Renewal` (no separate Multi-Year type).
- **$0 consortium deals:** Valid customers, not gaps. ~59 active $0 ESC 9/15/16 districts renewed 25/26 but have no 26/27 deal — a structure decision (Nathan/ESC 16), not an error. Never read "no 26/27 deal" as "not a customer."
- **Krum $0:** Intentional (former founder). Never flag as a data gap.
- **Banked vs in-flight:** Banked = Closed Won since 2025-09-01 (100%, in the bank). In-flight = open, counted at stage weight (weighted forecast) or 100% (best-case, if in-window).
- **No close date:** Open deals without a `closedate` (e.g. Copperas Cove $11,642, Erath Excels $170) are in the weighted forecast but **cannot** enter best-case. Set close dates in HubSpot to include them.
- **Renewal date conventions:** `contract_start` = prior closedwon's `contract_end` + 1 day; `contract_end` = `contract_start` + 1 year (− 1 day); `closedate` = day before `contract_start`.
- **Renewal pricing:** Amounts intentionally conservative (not $3/student) until pricing is set ($1.50/$2/$3 undecided). Don't propose a bulk amount bump.

---

## 9. Known Data-Quality Issues (as of 2026-06-24)

| Issue | Deals | Handling |
|---|---|---|
| Unmapped stage `3879394040` (NB-Texas) | Tomball, San Benito, Lovejoy (~$22K) | Provisional 20%; fix stage in HubSpot. |
| Stage-ID reuse relabeled active deals into Parked/Revisit (`1214475888`, old "Ready for Quote") | Northside $25K, Carrollton-Farmers Branch $22K, Harlingen $15K, Waco $12K, Granbury $7K | Should be Decision-Maker Buy-In; move them. |
| "Testing" dealtype renewals | Canyon $17K, Splendora $7.8K | Exclude. |
| No close date | Copperas Cove, Erath Excels | Can't enter best-case. |
| Krum renewal $0 | 1 | Valid — do not flag. |
| OOS stage IDs unreliable via API | 3 OOS deals | Forced to earliest stage label. |
| "Quote Requested" stage added 6/24 | — | Weighted 60% (resolved 6/24). Capture per-pipeline stage IDs from REST. |

---

## 10. Refresh Process

**Current (Jan's Claude):** Manual, on-demand. Jan asks her Claude Desktop to pull live HubSpot via the HubSpot MCP and rebuild the three HTML dashboards, typically before pipeline/renewal meetings. No fixed schedule. A weekly **Thursday** renewals report goes to Steve + Michelle (cashflow focus — lead with Committed + banked).

**Target (Zig):** Daily cron **06:00 CT** → pull HubSpot → apply `config.json` → write `data/sales-data.json` → commit (GitHub Pages publishes `docs/index.html`) → write `changelog/CHANGELOG.md` → update the Confluence page.

---

## 11. Output Locations

**Current HTML dashboards (bookmarked-os repo, `claude-output/reports/`):**
- `forecast-dashboard-2026-06-22.html` (+ `.pdf`, `.print.html`)
- `jan-deal-working-dashboard-2026-06-22.html` (+ `.pdf`, `.print.html`)
- `renewals-meeting-dashboard-2026-06-22.html` (+ `.pdf`, `.print.html`); latest `renewals-meeting-dashboard-2026-06-24.html` (+ `.pdf`)

**Confluence parent:** https://bookmarked-team.atlassian.net/wiki/spaces/SH/pages/155222017 (three dashboards are child pages).

**Future (this repo):** `data/sales-data.json`, `docs/index.html` (GitHub Pages).

**Design tokens (do not improvise):** primary `#12A38C`, bg `#f9f7f2`, text `#101828`; DM Sans / DM Serif Display / DM Mono. Source of truth: bookmarked-os `claude-output/design/bookmarked-brand.css` (CEO-locked).

---

## Changelog

| Date | Changed By | What Changed |
|---|---|---|
| 2026-06-24 | Steve + Hermes | Repo created, methodology template scaffolded. |
| 2026-06-24 | Jan's Claude | **Full methodology documented** — pipelines, properties, stage weights + IDs, exclusions, formulas, FY, edge cases, refresh, output paths. Flagged 2 open items: weight divergence (dashboard vs HubSpot win-probs) and "Quote Requested" weighting. Confirmed no external data sources feed calculations. |
