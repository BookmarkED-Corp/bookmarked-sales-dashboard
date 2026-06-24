# Bookmarked Sales Dashboard

**Owner:** Zig — CRO Agent, Bookmarked Hermes Fleet  
**Live Dashboard:** https://bookmarked-corp.github.io/bookmarked-sales-dashboard  
**Confluence:** https://bookmarked-team.atlassian.net/wiki/spaces/SH/pages/155222017  
**Last Updated:** Auto-updated daily by Zig

---

## What This Is

The single source of truth for Bookmarked sales pipeline data. Pulls live from HubSpot daily. Replaces manual Confluence dashboard updates. Accessible to the full team via the URL above.

This dashboard is also surfaced in Bookmarked's internal reporting tools.

---

## Who Has Access

| Person / Agent | Role | Access Level |
|----------------|------|--------------|
| Zig (`Hermes-Bookmarked-Team`) | CRO Agent — owns this repo | Admin |
| Dewey (`Hermes-Bookmarked-Team`) | CEO Agent — receives Zig's reports | Admin |
| Jan Gilboy (`jgilboy`) | Sales Leader — can update methodology | Write |
| Steve Wandler | Founder — reads via Founder Cockpit | Read (via GitHub Pages) |

---

## Repo Structure

```
bookmarked-sales-dashboard/
│
├── data/
│   └── sales-data.json          ← Zig writes here daily (live HubSpot output)
│
├── methodology/
│   └── config.json              ← Stage weightings, pipeline IDs, exclusion rules
│   └── METHODOLOGY.md           ← Human-readable version of the methodology
│
├── docs/
│   └── index.html               ← Team-facing HTML dashboard (GitHub Pages)
│   └── assets/                  ← CSS, fonts, icons
│
├── changelog/
│   └── CHANGELOG.md             ← Zig writes here every time something changes
│
└── README.md                    ← This file
```

---

## How It Works

### Daily Data Flow
```
6am CT — Zig pulls live deal data from HubSpot
    ↓
Reads methodology from methodology/config.json
    ↓
Applies weightings, exclusions, calculations
    ↓
Writes output to data/sales-data.json
    ↓
Commits to this repo → GitHub Pages auto-publishes
    ↓
Zig writes changelog entry to changelog/CHANGELOG.md
    ↓
Zig updates Confluence page with latest summary
```

### Making Methodology Changes
Jan or Zig can update `methodology/config.json` and `methodology/METHODOLOGY.md`.  
Every change must include a CHANGELOG.md entry explaining what changed and why.  
Steve sees all changes via G-Brain monitoring this repo.

---

## HubSpot Pipelines

**To be populated by Jan's Claude agent.**  
Jan: please have your Claude document the following in `methodology/METHODOLOGY.md`:

1. Every HubSpot pipeline you pull from (name + pipeline ID)
2. Every HubSpot field/property used in calculations
3. Stage weighting table (stage name → percentage)
4. Exclusion rules (which deal types, stages, or conditions get excluded)
5. Calculation formulas:
   - How is **Banked Revenue** calculated?
   - How is **Weighted Forecast** calculated?
   - How is **Best Case** calculated?
   - How is **Total Pipeline** calculated?
6. Any data sources outside HubSpot (spreadsheets, manual adjustments, etc.)
7. How often you currently refresh the data

Write the same information to the Confluence page linked above.  
When done, message Steve: "Done."

---

## Design System

When building the HTML dashboard (`docs/index.html`), use:

| Token | Value |
|-------|-------|
| Primary color | `#12A38C` (gold/teal) |
| Background | `#f9f7f2` (paper) |
| Text | `#101828` (ink) |
| Font | DM Sans, DM Serif Display, DM Mono |
| Style | Matches Founder Cockpit design system |

---

## Confluence Changelog Format

Every time Zig makes a change, write to the Confluence page in this format:

```
[YYYY-MM-DD] — [TYPE: DAILY_RUN | METHODOLOGY_CHANGE | BUG_FIX]
Summary: [one line]
Details: [what changed, what data was pulled, any anomalies]
Next run: [date/time]
```
