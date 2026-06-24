# Changelog

All changes to the dashboard, methodology, or data pipeline are documented here.

Format:
```
[YYYY-MM-DD] — [TYPE: DAILY_RUN | METHODOLOGY_CHANGE | BUG_FIX | SETUP]
Summary: one line
Details: what changed, what data was pulled, any anomalies
Next run: date/time
```

---

## Log

### [2026-06-24] — METHODOLOGY_CHANGE
**Summary:** Weight divergence RESOLVED — Jan ratified the dashboard weights (10/35/50/65/80/90) as canonical.
**Details:** Open Item 1 closed. The dashboard stage weights are the source of truth (they produced the published $738,991 weighted forecast). Zig must set HubSpot's `hs_deal_stage_probability` on every stage to MATCH these across all 6 revenue pipelines — NOT adopt the lower stored HubSpot probs (10/25/40/60/85/95) and NOT re-baseline the forecast. Updated `config.json` (divergence_flag → status RESOLVED + action_for_zig) and `METHODOLOGY.md` §3. "Quote Requested" weighting (Open Item 2) remains open pending Jan.
**Next run:** Zig's first automated run, after he aligns HubSpot win-probs to the canonical weights and Jan sets the "Quote Requested" weight.

### [2026-06-24] — METHODOLOGY_EXPORT
**Summary:** Jan's Claude documented the complete dashboard methodology — Zig's operating instructions are now in place.
**Details:** Populated `methodology/METHODOLOGY.md`, `methodology/config.json`, and `methodology/external-sources/` (incl. `none.md`). Documented all 10 pipelines (3 included / 7 excluded with reasons), every deal property queried, the full stage-weighting table with per-pipeline stage IDs, all exclusion rules, exact formulas (Banked, Weighted Forecast, High-Confidence Floor, Best Case, Ceiling, Total Pipeline, Gap to $1M), FY26 definition (Sep 1 2025 – Aug 31 2026), edge cases (Conroe multi-year, $0 consortium, Krum $0, no-close-date), refresh process, and output paths. Confirmed **no external data source feeds any calculation** (HubSpot-only; ESC 16 Sheet deprecated 2026-06-11). **Two open items flagged for Jan:** (1) weight divergence — Jan's dashboard weights (10/35/50/65/80/90) vs Zig's HubSpot win-probs (10/25/40/60/85/95) must be reconciled; (2) new "Quote Requested" stage (added 6/24) needs a weight assigned (~55–60% recommended). Mirror written to Confluence page 155222017.
**Next run:** Zig's first automated daily run, once the two open items are resolved with Jan.

### [2026-06-24] — SETUP
**Summary:** Repo created and scaffolded.  
**Details:** Steve Wandler + personal Hermes created the repo structure. Methodology templates populated with known data (pipeline IDs, known exclusions, known weightings). Awaiting Jan's Claude to complete the methodology documentation.  
**Next run:** After Jan's Claude completes methodology documentation.
