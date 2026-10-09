# Report spec: Demo_4 weekly overview

| | |
|---|---|
| Report | `_Demo_4/_report/Campaign Analysis.pbip` |
| Scope of this build | One page: Weekly overview (answers Q4). Q1 to Q3 are not in this build. |
| Inputs | [MODEL_BRIEF.md](MODEL_BRIEF.md), [MODEL_DESIGN.md](MODEL_DESIGN.md), report interview 2026-10-09 |
| Last updated | 2026-10-09 |
| Source of truth for DAX and formats | `Campaign Analysis.SemanticModel/definition/tables/_Measures.tmdl`. This spec does not repeat DAX. |

**How to keep this spec current.** A new or changed visual is a row in section 3. A new measure is a row in section 5. A new decision or assumption is a row in section 7. Every change gets a line in section 9. Status values: `Not built`, `Built`, `Changed`.

---

## 1. Purpose

| Item | Answer |
|---|---|
| Question | **Q4: How have spend and return moved over time?** Weekly, weeks start Monday. No year-on-year (the file has no second January). |
| User | One marketing manager across all five advertisers. No row-level security. |
| Cadence | Weekly, usually opened alone on a Monday. |
| Decision | Whether spend and return are moving in the right direction, before deciding where to look on other pages. The page decides nothing itself. |
| What they do differently | If ROAS drops while Spend holds, they dig into types, channels and campaigns. If both move together, they read it as mix or volume. They judge by eye; the page applies no threshold. |

## 2. Rules for the page

- No targets, benchmarks, flags or judging colours. Arrows, deltas and lines use one neutral colour.
- Do not use "efficient", "underperforming", "on track", "typical" or "CAC" in any title, label or note.
- `$` only. No currency code.
- Charts show full Monday-to-Sunday weeks only (V7 to V10). Cards and the Week slicer can show partial weeks.

## 3. Visual register

Reading order, top to bottom. All visuals respond to S2 to S5. **Week slicer (S1) affects cards only.**

| ID | Visual | Type | Fields | Visual-level filter | Week slicer (S1) | Status |
|---|---|---|---|---|---|---|
| V1 | Week caption | Text card | `Selected Week Label` | none | n/a (readout) | Not built |
| V2 | Spend | Card + change callout | `Spend (Selected Week)`; callout `Spend Change vs Previous Week` | none | Applies | Not built |
| V3 | Revenue | Card + change callout | `Revenue (Selected Week)`; callout `Revenue Change vs Previous Week` | none | Applies | Not built |
| V4 | ROAS | Card + change callout | `ROAS (Selected Week)`; callout `ROAS Change vs Previous Week` | none | Applies | Not built |
| V5 | CPA | Card + change callout | `CPA (Selected Week)`; callout `CPA Change vs Previous Week` | none | Applies | Not built |
| V6 | Active Campaigns | Card + change callout | `Active Campaigns (Selected Week)`; callout `Active Campaigns Change vs Previous Week` | none | Applies | Not built |
| V7 | Spend and Revenue by week | Line chart, 2 series | Axis `Date[Week Start]`; `Spend`, `Revenue` | `Date[Days In Week]` = 7 | **Off** | Not built |
| V8 | ROAS by week | Line chart | Axis `Date[Week Start]`; `ROAS` | `Date[Days In Week]` = 7 | **Off** | Not built |
| V9 | CPA by week | Line chart | Axis `Date[Week Start]`; `CPA` | `Date[Days In Week]` = 7 | **Off** | Not built |
| V10 | Active Campaigns by week | Line chart | Axis `Date[Week Start]`; `Active Campaigns` | `Date[Days In Week]` = 7 | **Off** | Not built |
| V11 | Partial-week note | Text box | Static text, below | none | n/a | Not built |

**Notes**

- **V8 and V9 are separate charts**, shown side by side under one heading "ROAS and CPA by week". The scales differ and a line chart has no secondary axis.
- **Change callout**: percentage change from the previous week, with `▲` for an increase and `▼` for a decrease in the format string (the minus sign is dropped). One neutral colour, no up/down colouring, because direction is not good or bad (a rise in CPA is a cost rise, a rise in Spend is neither). Blank when there is no previous week or its value is zero.
- **Chart axis** is `Week Start` (date axis), not `Week Label`, so weeks sit on an even time scale.
- **V11 text**: "The charts show full Monday-to-Sunday weeks only. The first week (Fri 1 Jan to Sun 3 Jan 2021, 3 days) and the last week (Mon 24 Jan to Fri 28 Jan 2022, 5 days) are partial and are left off the charts. They are still in the Week slicer, and their totals are low for that reason. Ratios are less affected." If the data refreshes with new dates, update the dates in this text.
- No data labels. Tooltips show the week and value.

## 4. Slicers and filters

| ID | Slicer | Field | Selection | Affects | Status |
|---|---|---|---|---|---|
| S1 | Week | `Date[Week Label]` (sorted by `Week Start`) | Single select, **selection not required** | Cards V1 to V6 only. Edit interactions to V7 to V10: off. | Not built |
| S2 | Company | `Campaign[Company]` | Multi-select | All visuals | Not built |
| S3 | Campaign Type | `Campaign[Campaign_Type]` | Multi-select | All visuals; narrows S4 | Not built |
| S4 | Channel | `Campaign[Channel_Used]` | Multi-select, cascades from S3 by leaving the default interaction between S3 and S4 on | All visuals | Not built |
| S5 | Customer Segment | `Campaign[Customer_Segment]` | Multi-select | All visuals | Not built |

Instagram appears under two types and YouTube under two types. Choosing a channel with no type selected includes both types. That is correct.

| Filter level | Filter |
|---|---|
| Visual | V7 to V10: `Date[Days In Week]` = 7. Others: none. |
| Page | None. A page filter on week would defeat S1. |
| Report | None. |

## 5. Measures used

All are in `_Measures`. DAX and format strings are in the TMDL.

**Existing (from the base model, used as is)**

| Measure | Used by |
|---|---|
| `Spend`, `Revenue`, `ROAS`, `CPA`, `Active Campaigns` | V7 to V10 |

**Added for this page: Built and saved 2026-10-09**

| Measure | Role | Used by |
|---|---|---|
| `Latest Full Week Start` | Monday of the latest week with 7 days in the Date table. Found from the data. | Helper |
| `Selected Week Start` | The week in S1, else `Latest Full Week Start`. | Helper |
| `Selected Week Label` | Caption: which week, and which week it is compared with. | V1 |
| `Spend (Selected Week)` | Spend for the selected week. | V2 |
| `Revenue (Selected Week)` | Revenue for the selected week. | V3 |
| `ROAS (Selected Week)` | ROAS for the selected week. | V4 |
| `CPA (Selected Week)` | CPA for the selected week. | V5 |
| `Active Campaigns (Selected Week)` | Active Campaigns for the selected week. | V6 |
| `Spend Change vs Previous Week` | Percentage change; format `▲ 0.0%;▼ 0.0%;0.0%`. | V2 |
| `Revenue Change vs Previous Week` | As above. | V3 |
| `ROAS Change vs Previous Week` | As above. | V4 |
| `CPA Change vs Previous Week` | As above. | V5 |
| `Active Campaigns Change vs Previous Week` | As above. | V6 |

**Why the "(Selected Week)" measures exist.** The base measures react to S1 and show all weeks when S1 is empty. The selected-week measures clear the filter on `Date` and set the week themselves, so (a) the cards default to the latest full week with no hard-coded date and (b) the previous week stays reachable when S1 has a week selected. New week-bound measures should follow this pattern: `VAR _week = [Selected Week Start]`, then `CALCULATE ( …, REMOVEFILTERS ( 'Date' ), 'Date'[Week Start] = _week )`.

**Verification.** On 2026-10-09 the base measures and the selected-week and change measures matched values computed from the source CSV at total, type, channel, segment and week level (weekly check covered the first 9 and last 8 weeks). Re-run this check if the data or the measure logic changes.

**Not used on this page (exist in the model for Q1 to Q3):** `Conversions`, `Clicks`, `Impressions`, `CTR`, `CVR`, `CPC`, `Campaigns Below ROAS 1`, `Share of Campaigns Below ROAS 1`, `Spend in Campaigns Below ROAS 1`, `Share of Spend in Campaigns Below ROAS 1`, `ROAS Rank in Type`, `ROAS Rank across Types`, `ROAS Rank of Segment`.

## 6. Default state when the page opens

- S1 cleared. Cards show the latest full Monday-to-Sunday week, found from the data (currently the week of Mon 17 Jan 2022, compared with Mon 10 Jan 2022). Not the partial week of 24 Jan.
- S2 to S5 cleared (All).
- Charts show every full week (currently Mon 4 Jan 2021 to Mon 17 Jan 2022, 55 weeks).
- Choosing a partial week in S1 compares a short week with the week before it. The change is an artefact; V11 says partial weeks are low.

## 7. Decisions and assumptions

| # | Item | Status |
|---|---|---|
| 1 | S1 drives cards only; charts show all full weeks. | Confirmed (interview) |
| 2 | Cards default to the latest full week, not hard coded. | Confirmed |
| 3 | Callouts show change from the previous week, with arrows, no judging colours. | Confirmed (arrows added on request) |
| 4 | Callouts are **percentage** change, not absolute. | **Assumption** |
| 5 | Charts leave off both partial weeks. The request said "the partial week" (singular). To drop only the last week, change the V7 to V10 filter to `Date[Is Current Week]` is false. | **Assumption** |
| 6 | "Full week" means 7 days in the Date table (`Days In Week` = 7), not 7 days of campaign rows. Same in this file. | **Assumption** |
| 7 | No minimum campaigns or spend before a ratio is shown. With a narrow filter a week can swing widely (brief, open item 4). | Inherited |
| 8 | V1 caption wording. | Not reviewed |
| 9 | S1 must not require a selection, or the latest-full-week default stops applying. | Constraint |

## 8. Traceability: brief questions to pages

| Brief question | Page | Status |
|---|---|---|
| Q1. Where should spend move between campaign types and channels? | none | Not in this build |
| Q2. Which customer segments return the most per dollar? | none | Not in this build |
| Q3. Which campaigns return less revenue than they cost, and how much spend is in them? | none | Not in this build |
| Q4. How have spend and return moved over time? | Weekly overview | In this build: V1 to V11, S1 to S5 |

## 9. Known issues and open items

| # | Item | Action |
|---|---|---|
| 1 | The report has one page, named "Page 1", with two tables that are not in this spec: a campaign-type ROAS rank table and a channel-rank-within-type table. They relate to Q1, which is not in this build. | Decide whether to remove them or move them to a later Q1 page. Rename the page "Weekly overview". |
| 2 | MODEL_DESIGN.md says the first week has 4 days. It has 3 (checked against `Days In Week`). | Correct MODEL_DESIGN.md. |
| 3 | The default week (17 Jan 2022) shows large falls against 10 Jan: Spend ▼ 49.8%, Active Campaigns ▼ 48.8%. The week is full, so this is not a partial-week effect. Campaigns are ending in the final weeks of the file. V11 covers partial weeks only. | Decide whether V11 should also say that the final weeks of the file are low because campaigns are ending. |
| 4 | The arrow format string has not been checked in a rendered card. | Check when V2 to V6 are built. |

## 10. Change log

| Date | Change |
|---|---|
| 2026-10-09 | Spec created from interview. Added arrows to callouts; charts changed to full weeks only; 13 measures built and saved; spec restructured as a maintained register. |
