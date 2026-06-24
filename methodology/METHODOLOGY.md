# Sales Dashboard Methodology

**Status:** PENDING — awaiting Jan's Claude documentation  
**Owner:** Zig (CRO Agent)  
**Last Updated:** 2026-06-24

---

## Instructions for Jan's Claude

Please populate every section below with exactly how you currently pull and calculate sales data. Be as specific as possible — pipeline IDs, exact HubSpot field names, every formula. This becomes the source of truth Zig uses to recreate and automate the dashboard.

Write the same information to the Confluence page:
https://bookmarked-team.atlassian.net/wiki/spaces/SH/pages/155222017

When complete, message Steve: "Done."

---

## 1. HubSpot Pipelines

*Document every pipeline you pull from. Include pipeline ID, name, and purpose.*

| Pipeline Name | Pipeline ID | Include in Dashboard | Notes |
|---------------|-------------|---------------------|-------|
| | | | |

---

## 2. HubSpot Fields Used

*List every HubSpot deal property your Claude queries when building the dashboard.*

| Field Name | HubSpot Property Key | Used For |
|------------|---------------------|----------|
| Deal Name | `dealname` | |
| Amount | `amount` | |
| Close Date | `closedate` | |
| Deal Stage | `dealstage` | |
| Pipeline | `pipeline` | |
| Owner | `hubspot_owner_id` | |
| Deal Type | `dealtype` | |
| | | |

*Add any additional fields here.*

---

## 3. Stage Weighting Table

*The probability percentage applied to each deal stage for weighted forecast calculation.*

| Stage Name | HubSpot Stage ID | Weight % |
|------------|-----------------|---------|
| | | |

**Note:** A new stage "Quote Requested" was added to HubSpot on 2026-06-24. Please confirm what weighting this should carry.

---

## 4. Exclusion Rules

*Which deals get excluded from the dashboard and why.*

| Rule | Condition | Reason |
|------|-----------|--------|
| | | |

---

## 5. Calculation Formulas

### Banked Revenue
*How is this calculated? Which stages qualify? Which pipeline(s)?*

```
Formula: TBC
```

### Weighted Forecast
*How is this calculated?*

```
Formula: SUM(deal.amount × stage_weight) for all active deals
Pipelines included: TBC
Stages included: TBC
Stages excluded: TBC
```

### Best Case
*How is this calculated?*

```
Formula: TBC
```

### Total Pipeline
*How is this calculated?*

```
Formula: TBC
```

---

## 6. Data Sources Outside HubSpot

*Are there any spreadsheets, manual adjustments, or other data sources used?*

TBC

---

## 7. Known Data Quality Issues

*Document any known issues with the data that require manual handling.*

| Issue | Affected Deals | Current Handling |
|-------|---------------|-----------------|
| Utah SEA ($1.3M) | pipeline 67437740 | Parked — not working pipeline, exclude from forecast |
| Jefferson County FL duplicates | Two records for same district | TBC |
| $0 Consortium deals | Multiple ESC consortium deals | TBC — valid won deals but $0 amount |
| Testing deal type | Canyon ISD, Splendora ISD | Exclude from all calculations |

---

## Changelog

| Date | Changed By | What Changed |
|------|-----------|-------------|
| 2026-06-24 | Steve + Hermes | Repo created, methodology template scaffolded |
| | | |
