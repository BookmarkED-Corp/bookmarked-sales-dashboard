# Bookmarked Sales Dashboard

**Owner:** Zig — CRO Agent, Bookmarked Hermes Fleet  
**Live Dashboard:** https://bookmarked-corp.github.io/bookmarked-sales-dashboard  
**Confluence:** https://bookmarked-team.atlassian.net/wiki/spaces/SH/pages/155222017  
**Last Updated:** Auto-updated daily by Zig

---

## What This Is and Why It Exists

Bookmarked is moving all sales reporting to a centralized, automated system. This repo is the single source of truth for all sales pipeline data.

**The problem it solves:** Jan Gilboy (Sales Leader) has been using Claude Desktop to manually pull HubSpot data and post dashboards to Confluence. That works but it requires a human to trigger it, it can't run on a schedule, and it doesn't give the full Bookmarked Hermes fleet access to the live data.

**The solution:** Zig (CRO Agent, Bookmarked Hermes) takes over ownership of all sales data. Zig pulls directly from HubSpot daily, writes structured data to this repo, and publishes a live HTML dashboard automatically. Jan interacts with Zig directly for any updates, changes, or questions — she no longer has to run her own Claude process for this.

**What Jan's Claude needs to do first (one-time):** Document the complete methodology it currently uses — every pipeline, every weighting, every formula, every external data source — into this repo and into the Confluence page. That documentation becomes Zig's operating instructions. Once Zig has it, he owns it.

---

## Who Has Access

| Person / Agent | Role | Access Level |
|----------------|------|--------------|
| Zig (`Hermes-Bookmarked-Team`) | CRO Agent — owns this repo, runs daily cron | Admin |
| Dewey (`Hermes-Bookmarked-Team`) | CEO Agent — receives Zig's reports | Admin |
| Jan Gilboy (`jgilboy`) | Sales Leader — can update methodology, ask Zig for changes | Write |
| Steve Wandler | Founder — reads via team dashboard and Founder Cockpit | Read (via GitHub Pages) |

---

## Repo Structure

```
bookmarked-sales-dashboard/
│
├── data/
│   └── sales-data.json               ← Zig writes here daily (live HubSpot output)
│
├── methodology/
│   ├── config.json                   ← Stage weightings, pipeline IDs, exclusion rules, formulas
│   ├── METHODOLOGY.md                ← Human-readable full methodology (Jan's Claude documents this)
│   └── external-sources/
│       └── README.md                 ← Any non-HubSpot data sources (spreadsheets, Sheets, etc.)
│
├── docs/
│   ├── index.html                    ← Live team-facing HTML dashboard (GitHub Pages)
│   └── assets/                       ← CSS, fonts, icons
│
├── changelog/
│   └── CHANGELOG.md                  ← Zig writes here every time something changes
│
└── README.md                         ← This file
```

---

## The Full Data Flow (Once Zig Is Running)

```
Daily at 6am CT — Zig pulls live deal data from HubSpot
    ↓
Reads methodology from methodology/config.json
    ↓
Applies weightings, exclusions, and calculation formulas
    ↓
Writes output to data/sales-data.json
    ↓
Commits to this repo → GitHub Pages auto-publishes docs/index.html
    ↓
Zig writes a changelog entry to changelog/CHANGELOG.md
    ↓
Zig updates the Confluence page with latest summary + changelog entry
```

**When Jan wants a methodology change:**
```
Jan asks Zig (via Slack) → Zig updates methodology/config.json
    ↓
Zig writes changelog entry explaining what changed and why
    ↓
Zig updates Confluence page
    ↓
Next daily run picks up the new methodology automatically
```

**Steve asks Zig a sales question:**
```
Steve → Dewey → Zig reads data/sales-data.json + methodology/config.json
    ↓
Zig answers with live data, no manual pulling required
```

---

## HubSpot Connection

**Portal ID:** 39855019  
**Account:** Bookmarked (standard, USD, America/Chicago)

**Known Pipelines:**

| Pipeline Name | Pipeline ID | In $1M Goal? |
|---------------|-------------|--------------|
| New Business — Texas | `default` | Yes |
| 26/27 Renewals | `798674460` | Yes |
| New Business — Out of State | `67437740` | Partial (FY27 deals excluded from Sept 1 goal) |

**Note:** Utah State Education Agency ($1.3M, pipeline `67437740`) is parked — real book but FY27, not counted toward the $1M-by-Sept-1 goal.

---

## Methodology (PENDING — Jan's Claude to complete)

Full methodology lives in `methodology/METHODOLOGY.md` and `methodology/config.json`.

Jan's Claude is responsible for documenting:
1. Every HubSpot pipeline — name, pipeline ID, included in $1M goal or not
2. Every HubSpot deal property queried — including any custom properties
3. Exact stage weighting table — every stage name, HubSpot stage ID, percentage weight applied
4. All exclusion rules — deal types, stages, amounts, or conditions excluded and why
5. Exact formula for: Banked Revenue, Weighted Forecast, Best Case, Total Pipeline, Gap to $1M
6. Fiscal year definition (appears to be Sep 1 2025 – Aug 31 2026)
7. Any external data sources — Excel, Google Sheets, Google Drive files used to cross-reference or reconcile HubSpot data. For each: file name, exact location in Google Shared Drive / Sales Hub, what it contains, how it affects calculations. If none exist, state that explicitly in `methodology/external-sources/none.md`
8. Path to existing HTML report files (currently: bookmarked-os repo, claude-output/reports/)
9. Current refresh process — how often, what triggers it

**This documentation is Zig's operating instructions. It must be complete before Zig can take over.**

---

## Design System

All HTML (docs/index.html) uses the Bookmarked design system:

| Token | Value |
|-------|-------|
| Primary color | `#12A38C` |
| Background | `#f9f7f2` |
| Text | `#101828` |
| Font | DM Sans, DM Serif Display, DM Mono |

---

## Confluence Page

https://bookmarked-team.atlassian.net/wiki/spaces/SH/pages/155222017

This page mirrors the repo. Zig writes a summary and changelog entry here after every daily run and every methodology change. It is the shared brain between Jan's Claude, Zig, and Steve's Hermes. Jan can read it via her Claude. Steve's Hermes reads it to stay current on sales data.

---

## Confluence Changelog Format

Every Zig update to Confluence must follow this format:

```
[YYYY-MM-DD] — [TYPE: DAILY_RUN | METHODOLOGY_CHANGE | BUG_FIX | SETUP]
Summary: one line — what happened
Details: what changed, what data was pulled, any anomalies flagged
Next run: date/time CT
```

---

## Known Data Quality Issues (as of June 22, 2026)

| Issue | Deals Affected | Current Handling |
|-------|---------------|-----------------|
| 3 TX net-new deals in unmapped stage | Tomball, San Benito, Lovejoy (~$22K) | Flagged — stage not mapped to weighting |
| 2 renewal deals tagged "Testing" | Canyon ISD ($17K), Splendora ISD ($7.8K) | Likely exclude — confirm with Jan |
| 2 renewals with no close date | Copperas Cove ISD, Erath Excels Academy | Cannot include in Best Case calc |
| Krum ISD renewal at $0 | 1 deal | Valid won deal but $0 amount |
| Jefferson County FL duplicates | 2 records ($2 and $1,500) | Confirm which is canonical |
| Utah SEA ($1.3M) parked | 1 deal | Real book, FY27 — exclude from Sept 1 goal |
| Canyon ISD dealtype = Testing | 1 deal | Exclude from all calculations |
| Splendora ISD dealtype = Testing | 1 deal | Exclude from all calculations |

Zig should flag any new data quality issues in the changelog and surface them to Jan via Slack.
