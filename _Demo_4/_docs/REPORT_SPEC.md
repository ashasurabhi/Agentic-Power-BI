# Report spec: Demo_4 weekly overview

| | |
|---|---|
| Report | `_Demo_4/_report/Campaign Analysis.pbip` |
| Scope of this build | One page: Weekly overview (answers Q4). Q1 to Q3 are not in this build. |
| Inputs | [MODEL_BRIEF.md](MODEL_BRIEF.md), [MODEL_DESIGN.md](MODEL_DESIGN.md), report interview 2026-10-09 |
| Style reference | [REPORT_STYLE.md](REPORT_STYLE.md) (MIP house style). It applies to every visual here. Where this spec is silent on how something looks, the style standard decides. Exceptions are in section 3.1. |
| Layout | [REPORT_DESIGN.md](REPORT_DESIGN.md). It predates the style standard, so where the two differ (section 9, item 6) this spec and REPORT_STYLE.md win. |
| Last updated | 2026-10-09 (page built) |
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

- No targets, benchmarks, flags or judging colours. Arrows and deltas use one neutral colour. Chart series use the style standard's blue and navy, which carry no good or bad meaning.
- Follow [REPORT_STYLE.md](REPORT_STYLE.md) for colour, type, containers, slicers, cards and charts. Do not use its status bands or bullet charts on this page.
- Do not use "efficient", "underperforming", "on track", "typical" or "CAC" in any title, label or note.
- `$` only. No currency code.
- Charts show full Monday-to-Sunday weeks only (V7 to V10). Cards and the Week slicer can show partial weeks.

## 3. Visual register

Reading order, top to bottom. All visuals respond to S2 to S5. **Week slicer (S1) affects cards only.**

| ID | Visual | Type | Fields | Visual-level filter | Week slicer (S1) | Status |
|---|---|---|---|---|---|---|
| V12 | Page title | Text box, centred in the header, 36 px Segoe UI Semibold | Static text "Weekly Overview" (title case) | none | n/a | Built |
| V14 | Header line | Shape (rectangle, 2 px high, logo green `#008000`), full content width, directly under the header at y = 90 | Decorative | none | n/a | Built |
| V13 | Logo | Image | [assets/MIP_logo.png](assets/MIP_logo.png) (1441 × 964 px; has white padding around the mark) | none | n/a | Built |
| V1 | Week caption | Text card | `Selected Week Label` | none | n/a (readout) | Built |
| V2 | Spend | Card, grouped with a callout card | `Spend (Selected Week)`; callout `Spend Change vs Previous Week` | none | Applies | Built |
| V3 | Revenue | Card, grouped with a callout card | `Revenue (Selected Week)`; callout `Revenue Change vs Previous Week` | none | Applies | Built |
| V4 | ROAS | Card, grouped with a callout card | `ROAS (Selected Week)`; callout `ROAS Change vs Previous Week` | none | Applies | Built |
| V5 | CPA | Card, grouped with a callout card | `CPA (Selected Week)`; callout `CPA Change vs Previous Week` | none | Applies | Built |
| V6 | Active Campaigns | Card, grouped with a callout card | `Active Campaigns (Selected Week)`; callout `Active Campaigns Change vs Previous Week` | none | Applies | Built |
| V7 | Spend and Revenue by week | Line chart, 2 series | Axis `Date[Week Start]`; `Spend`, `Revenue` | `Date[Days In Week]` = 7 | **Off** | Built |
| V8 | ROAS by week | Line chart | Axis `Date[Week Start]`; `ROAS` | `Date[Days In Week]` = 7 | **Off** | Built |
| V9 | CPA by week | Line chart | Axis `Date[Week Start]`; `CPA` | `Date[Days In Week]` = 7 | **Off** | Built |
| V10 | Active Campaigns by week | Line chart | Axis `Date[Week Start]`; `Active Campaigns` | `Date[Days In Week]` = 7 | **Off** | Built |
| V11 | Partial-week note | Text box | Static text, below | none | n/a | Built |

**Notes**

- **V8 and V9 are separate charts**, stacked, each with its own title. The scales differ and a line chart has no secondary axis.
- **Change callouts are built as a second, transparent card** laid over the lower part of each KPI card and grouped with it (group named after the measure). The card's own reference-label property did not render a callout in Desktop, so it is not used. The callout card binds to the change measure and follows S1 like the card beneath it. It is inset 10 px on the sides and bottom so the KPI card's border stays visible. The "vs LW" (versus last week) text is part of the change measures' format string.
- **Chart axes:** continuous date axis on `Week Start` with month labels on all four charts. Desktop hides the labels when a chart is too short, so each chart needs about 176 px or more (190 px for V7, V9 and V10, 176 px for V8). V7's Y axis shows the measure's own K/M format (for example "$10,000K") with the visual's display units set to None, because automatic units scaled the value a second time.
- **Change callout**: percentage change from the previous week, with `▲` for an increase and `▼` for a decrease in the format string (the minus sign is dropped). One neutral colour, no up/down colouring, because direction is not good or bad (a rise in CPA is a cost rise, a rise in Spend is neither). Blank when there is no previous week or its value is zero.
- **Chart axis** is `Week Start` (date axis), not `Week Label`, so weeks sit on an even time scale.
- **V11 text**: "The charts show full Monday-to-Sunday weeks only. The first week (Fri 1 Jan to Sun 3 Jan 2021, 3 days) and the last week (Mon 24 Jan to Fri 28 Jan 2022, 5 days) are partial and are left off the charts. They are still in the Week slicer, and their totals are low for that reason. Ratios are less affected." If the data refreshes with new dates, update the dates in this text.
- No data labels. Tooltips show the week and value.

### 3.1 Style applied

Each row says which part of [REPORT_STYLE.md](REPORT_STYLE.md) governs the element, and any exception for this page.

| Element | Style rule | Exception or choice on this page |
|---|---|---|
| All visuals and slicers | Container, 6.1: 1 px `#D5D5D5` border, 6 px radius, no shadow, title inside top left | The four charts V7 to V10 have the same container as the cards: 1 px `#D5D5D5` border, 6 px radius, white fill, title inside. |
| V12 Page title | Section 2: top left, regular, `#252423` | None. |
| V13 Logo | Section 2: top right, about 7% of page height, no recolour | None. |
| V2 to V6 cards | 6.3: grey `#E6E6E6` title band with the label centred; large centred value on white; equal sizes | The change callout sits under the value in 15 px secondary text `#605E5C`, centred, one neutral colour (the rule in 6.3). |
| V1 caption | 6.8 notes: 12 px to 14 px secondary text, left aligned, no border | None. |
| V7 to V10 line charts | 6.6: 2 px line, legend top left, axis titles, light gridlines `#EEEEEE`, date axis | **No markers** (55 points) and **no data labels** (the cards give the latest value). Series colour: see below. |
| Series colours | Section 4: blue `#118DFF` first, navy `#12239E` second | V7: Revenue blue, Spend navy. V8, V9, V10: one series, blue. |
| S1 to S5 slicers | 6.2: dropdown, label inside the box, "All" when clear, `#B0B6DE` border | S1 is single select; S2 to S5 are multi-select. Search on for S1 (57 weeks). |
| V11 note | 6.8: 11 to 12 px secondary text, below the charts | None. |
| Number formats | Style rule 9: from the model | The change callouts use the format string on the measure (`▲ 0.0% vs LW;▼ 0.0% vs LW;0.0% vs LW`). |
| Tables | 6.4 | None on this page. |

## 4. Slicers and filters

All five are dropdowns in **one row** just below the header line (y = 104), in this order: Company, Campaign Type, Channel, Customer Segment, Week. S1 is in the row at your request, even though it affects only the cards ([REPORT_STYLE.md](REPORT_STYLE.md), section 3, rule 4 would place it with the cards). Its title reads "Week (cards only)" so the difference is clear. Each box is 348 px wide: a 150 px title area and a 190 px dropdown.

| ID | Slicer | Field | Selection | Affects | Status |
|---|---|---|---|---|---|
| S1 | Week | `Date[Week Label]` (sorted by `Week Start`) | Single select, **selection not required** | Cards V1 to V6 only. Edit interactions to V7 to V10: off. | Built |
| S2 | Company | `Campaign[Company]` | Multi-select | All visuals | Built |
| S3 | Campaign Type | `Campaign[Campaign_Type]` | Multi-select | All visuals; narrows S4 | Built |
| S4 | Channel | `Campaign[Channel_Used]` | Multi-select, cascades from S3 by leaving the default interaction between S3 and S4 on | All visuals | Built |
| S5 | Customer Segment | `Campaign[Customer_Segment]` | Multi-select | All visuals | Built |

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
| `Spend Change vs Previous Week` | Percentage change; format `▲ 0.0% vs LW;▼ 0.0% vs LW;0.0% vs LW`. | V2 |
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
| 10 | The page follows [REPORT_STYLE.md](REPORT_STYLE.md). | Confirmed (2026-10-09) |
| 11 | V7 uses navy for Spend and blue for Revenue (the style's second-series colour), not the grey in REPORT_DESIGN.md. | **Assumption** |
| 12 | No markers and no data labels on V7 to V10, because 55 points would crowd. The style standard shows markers and selected labels. | **Assumption** |
| 13 | The logo is `assets/MIP_logo.png` (the supplied file, renamed). | Confirmed (2026-10-09) |
| 15 | Each filter is a group: a rounded rectangle titled with the filter name (6 px visual border) plus the native dropdown slicer with its header hidden, 56 px high. Built this way to match the existing MIP report. | Confirmed (2026-10-09) |
| 16 | The Week slicer uses "Multi-select with CTRL" (`singleSelect`), not "Single select" (`strictSingleSelect`). The strict form forces a selection and would pick the newest week, the partial 24 Jan 2022, which breaks the default (item 9). A Ctrl-click can add a second week; the measures then fall back to the latest full week. | **Assumption** |
| 18 | All five filters are in one row (S1 included). | Confirmed (2026-10-09) |
| 19 | **The page was moved in Desktop after the build.** Chart positions are now a two-column arrangement you set (V7 and V9 left, V8 and V10 right). The files are the source of truth. The generator script is out of date and must not be rerun, or it will overwrite your placement. | Constraint |
| 21 | Header: title centred and larger ("Weekly Overview", 36 px), a 2 px grey line under the header at y = 90, filter row moved down to y = 104 so it clears the logo, week caption moved to y = 168. | Confirmed (2026-10-09) |
| 20 | Chart heights were raised only as far as the date labels need: V7 190, V8 176, V9 190, V10 190. Positions were not changed. | **Assumption** |
| 17 | Card values use Segoe UI Semibold, not DIN, because DIN did not render in this Desktop (fell back to a serif font). Card display units are set to None so the measure's own K/M format applies. | **Assumption** |
| 14 | The slicer, card change line and notes rules in REPORT_STYLE.md (sections 6.2, 6.3 and 6.8) are used here as written. They were not seen in the source screenshot, so they are not confirmed against a live MIP report. | **Assumption** |

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
| 1 | The two Q1 tables on the old "Page 1". | **Resolved 2026-10-09.** Deleted by you; the page is now "Weekly overview" (page folder `b37c668e8890066b493a`). |
| 2 | MODEL_DESIGN.md says the first week has 4 days. It has 3 (checked against `Days In Week`). | Correct MODEL_DESIGN.md. |
| 3 | The default week (17 Jan 2022) shows large falls against 10 Jan: Spend ▼ 49.8%, Active Campaigns ▼ 48.8%. The week is full, so this is not a partial-week effect. Campaigns are ending in the final weeks of the file. V11 covers partial weeks only. | Decide whether V11 should also say that the final weeks of the file are low because campaigns are ending. |
| 4 | The arrow format string. | **Resolved 2026-10-09.** ▲ and ▼ render in the callout cards. |
| 5 | The logo file `assets/MIP_logo.png` has white padding around the mark. | Optionally trim the padding before building V13. |
| 6 | REPORT_DESIGN.md differs from the style standard on: Spend colour on V7 (grey, not navy), card layout (left aligned, white, not a centred title band), table rule (section 4, rule 8), and slicer placement (S1 in the filter strip area). It has no logo or page-title rule. | Update REPORT_DESIGN.md to match this spec. |
| 7 | The card-value font is unconfirmed (REPORT_STYLE.md, section 9). Segoe UI Semibold is used. | Take it from the original MIP report. |
| 8 | **Not click-tested.** The build was checked at the default state only (screenshots and values). Choosing a week, choosing filters, the Type-to-Channel cascade, and "clicking a chart changes nothing" were not exercised, because the preview tool cannot click. | Try these in Desktop (REPORT_BUILD.md, section 5). |
| 9 | The report validator could not fetch the online JSON schema, so it skipped schema checks. Role, property and reference checks did run, with no errors. | Re-run validate when the schema is reachable. |
| 10 | V7's Y axis reads "$10,000K", "$5,000K", "$0K". The charts are not aligned to a common right edge: the Active Campaigns chart ends about 8 px before the ROAS chart above it. | Cosmetic. Align when the final layout is chosen. |
| 11 | The default week shows large falls (see item 3). The page note still covers partial weeks only. | Unchanged; decide on the wording of V11. |

## 10. Change log

| Date | Change |
|---|---|
| 2026-10-09 | Header: centred title in title case, header line (V14), filter row and caption moved down; slicers centred in their boxes (decision 21). |
| 2026-10-09 | Filters in one row; borders and X axes on all four charts; "vs LW" on the callouts; callout cards inset; chart heights raised for labels; decisions 18 to 20. |
| 2026-10-09 | Page built and checked in Desktop (REPORT_BUILD.md, section 8): all visuals and slicers marked Built; callouts built as grouped overlay cards; decisions 15 to 17; known issues 1, 4 resolved, 8 to 11 added. |
| 2026-10-09 | Adopted REPORT_STYLE.md as the style reference: added the style row, section 3.1, page title (V12) and logo (V13), dropdown slicers, and decisions 10 to 14. |
| 2026-10-09 | Spec created from interview. Added arrows to callouts; charts changed to full weeks only; 13 measures built and saved; spec restructured as a maintained register. |
