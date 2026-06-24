# External Sources

Any data used in the sales dashboard that does not come directly from HubSpot must be documented and stored here.

## Status as of 2026-06-24

**There are no external sources feeding the dashboard calculations.** See [`none.md`](./none.md) for the explicit, unambiguous statement and the list of annotation-only context references (which affect zero calculations).

HubSpot (portal `39855019`) is the single source of truth for every number. The legacy ESC 16 Google Sheet tracker was deprecated 2026-06-11.

---

## If an external source is ever added

Adding an external data input is a **methodology change**: update `methodology/config.json`, add a file here documenting it, log it in `changelog/CHANGELOG.md`, and have Jan ratify it. For each source document:

- File name and type (Excel, Google Sheet, CSV, etc.)
- Where it lives (Google Drive link / Shared Drive path / Sales Hub page / local path)
- What data it contains
- Which dashboard calculations it affects
- How often it is updated and by whom
