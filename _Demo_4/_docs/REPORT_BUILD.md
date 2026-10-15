# Report build plan: Demo_4 weekly overview

| | |
|---|---|
| Report | `_Demo_4/_report/Campaign Analysis.pbip` |
| Inputs | [REPORT_SPEC.md](REPORT_SPEC.md), [REPORT_DESIGN.md](REPORT_DESIGN.md), [REPORT_STYLE.md](REPORT_STYLE.md) |
| Status | **Built 2026-10-09** and checked at the default state in Desktop. D1 to D4 decided. Differences from this plan are in section 8. Not yet click-tested. |
| Last updated | 2026-10-09 |
| Where this plan wins | Slicer construction and theme use below replace REPORT_DESIGN.md where they differ. Geometry otherwise follows REPORT_DESIGN.md section 2.1. |
| Tooling | `powerbi-report-author` 0.4.0 (validate, preview, catalog). Visual types and roles below come from its catalog, not from memory. |

---

## 1. Decisions

| # | Decision | Status |
|---|---|---|
| D1 | **Slicer construction** (the existing MIP method, as in the "Client" slicer). Each filter is a group named after its title, holding two items: a rounded rectangle shape whose text is the slicer title and which carries the visual border, and the native dropdown slicer placed inside that rectangle with its own header hidden. This keeps slicers at 48 px and puts the label inside the box, as REPORT_STYLE.md 6.2 asks. | **Decided 2026-10-09** |
| D2 | **Theme.** REPORT_DESIGN.md says no custom theme. REPORT_STYLE.md sets house colours for every future MIP report. **Decided:** add one small theme that extends Fluent2 with the style colours (blue, navy, LY grey, text, outline) and the card and slicer borders, so later pages match without per-visual formatting. | **Decided 2026-10-09** |
| D3 | **The two Q1 tables on "Page 1"** (campaign-type ROAS rank, channel rank in type). They are not in this build. Q1 gets its own page later. | **Done by you in Desktop 2026-10-09.** Both visual files were still on disk when checked, so the deletion has not been saved. Save the report in Desktop. After that, if the two files remain, they are removed as part of step 2. |
| D4 | **Card title band.** The style wants a grey band with the label centred. The new card visual has none of its own. **Decided:** use the card's container title, set to the measure name with a `#E6E6E6` background, centred. Fallback only if that cannot be formatted: a rectangle behind the card. | **Decided 2026-10-09** |

## 2. Pages

One page: **Weekly overview**. It is the existing "Page 1", renamed, kept at 1920 × 1080 with "Fit to page". No other pages are created. Q1 to Q3 are not in this build.

## 3. Visuals and bindings

23 visuals plus 5 groups. Columns sit at x = 40, 413, 786, 1159 and 1532, each 348 wide. **Geometry is provisional** and is fixed by screenshot review.

All bindings use `_Measures`, `Date` and `Campaign`. No new measures or columns are needed. The 13 added measures are saved in `_Measures.tmdl`.

### 3.1 Page items

| ID | Visual (tool type) | Binds to | x, y, w, h | Filter and interaction |
|---|---|---|---|---|
| V12 | Text box | Static text "Weekly overview" | 40, 24, 900, 40 | none |
| V13 | Image | `assets/MIP_logo.png`, copied into the report's resources | 1768, 8, 112, 75 | none |
| S2 to S5, S1 | Five slicer groups, each a rounded rectangle with the title plus the native slicer (section 3.2) | see 3.2 | see 3.2 | see 3.2 |
| V1 | New card (`cardVisual`), label hidden, left aligned | `_Measures[Selected Week Label]` | 413, 144, 1467, 48 | follows S1 |
| V2 | New card | Value `_Measures[Spend (Selected Week)]`; reference label `_Measures[Spend Change vs Previous Week]` | 40, 200, 348, 144 | follows S1 |
| V3 | New card | `Revenue (Selected Week)`; `Revenue Change vs Previous Week` | 413, 200, 348, 144 | follows S1 |
| V4 | New card | `ROAS (Selected Week)`; `ROAS Change vs Previous Week` | 786, 200, 348, 144 | follows S1 |
| V5 | New card | `CPA (Selected Week)`; `CPA Change vs Previous Week` | 1159, 200, 348, 144 | follows S1 |
| V6 | New card | `Active Campaigns (Selected Week)`; `Active Campaigns Change vs Previous Week` | 1532, 200, 348, 144 | follows S1 |
| V7 | Line chart, no markers, no data labels | X `Date[Week Start]`, continuous date axis. Y `[Spend]` (navy), `[Revenue]` (blue). Legend top left. | 40, 368, 1840, 152 | visual filter `Date[Days In Week]` = 7; ignores S1 |
| V8 | Line chart | X `Date[Week Start]`. Y `[ROAS]` (blue). | 40, 532, 1840, 152 | same filter; ignores S1 |
| V9 | Line chart | X `Date[Week Start]`. Y `[CPA]` (blue). | 40, 696, 1840, 152 | same filter; ignores S1 |
| V10 | Line chart, axis labels shown | X `Date[Week Start]`. Y `[Active Campaigns]` (blue), zero baseline. | 40, 860, 1840, 152 | same filter; ignores S1 |
| V11 | Text box | Static partial-weeks note (spec, section 3, notes) | 40, 1020, 1840, 36 | none |

### 3.2 Slicer groups (D1)

Each group is named after its title (for example "Client" in the existing MIP report, which holds "Client" and "Slicer"). Each holds two items and has no formatting of its own. All five groups are built the same way.

| Group (and shape title) | Group position (x, y) | Native slicer | Field | Selection |
|---|---|---|---|---|
| Company | 40, 80 | S2 | `Campaign[Company]` | Multi |
| Campaign Type | 413, 80 | S3 | `Campaign[Campaign_Type]` | Multi; narrows S4 |
| Channel | 786, 80 | S4 | `Campaign[Channel_Used]` | Multi |
| Customer Segment | 1159, 80 | S5 | `Campaign[Customer_Segment]` | Multi |
| Week (cards only) | 40, 144 | S1 | `Date[Week Label]` (sorted by `Week Start`, newest first, search on) | Single, selection not required |

**The two items inside every group**

| Item | Name in the selection pane | Position | Format |
|---|---|---|---|
| Rounded rectangle (`shape`, shape type `rectangleRounded`) | The slicer title, for example "Company" | The full 348 × 48, at the back | Text = the slicer title, 13 px semibold `#252423`, left aligned with a 12 px left margin, vertically centred. White fill. Shape outline off. **Visual border on:** 1 px `#B0B6DE`, radius 4 (REPORT_STYLE.md 6.2). |
| Native slicer (`slicer`, mode `Dropdown`) | "Slicer" | On top of the rectangle, 196 × 48, starting 144 px in (8 px clear of the right edge) | Header hidden. No border, no fill. Padding declared explicitly as 8/8/8/8, because a 48 px slicer holds only the 32 px dropdown plus that padding. |

The dropdown's own selector box shows as the inner box with the chevron, as in the existing report. The title text sits to the left of it, inside the rectangle. All five use the same split (title area 144 px, dropdown 196 px) so the dropdowns line up down the page.

**Reading order for tabbing:** the rectangle first, then its slicer. The slicer gets alt text naming its field, because its own header is hidden.

### 3.3 Roles confirmed from the tool's catalog

| Visual type | Roles used |
|---|---|
| `cardVisual` | `Data` (measure). The change callout is bound as the reference label, not a second `Data` measure. |
| `lineChart` | `Category` (X axis), `Y` (measures). Axis type `Scalar` for a continuous date axis. |
| `slicer` | `Values` (one column). Mode `Dropdown`. |
| `shape` | No data roles. Formatting objects used: `shape` (type `rectangleRounded`, rounded corners), `text` (title, size, bold, alignment, margins), `fill`, `outline`; the border is the container border. |
| `textbox`, `image` | No data roles. |

### 3.4 Interactions (saved in the page definition)

| From | To | Setting |
|---|---|---|
| S1 | V7 to V10 | Off |
| S1 | V1 to V6 | On |
| S2 to S5 | everything | On |
| S3 | S4 | On (the cascade) |
| V7 to V10 | each other and the cards | Off |
| Cards | everything | Off |

The rectangles take no part in interactions.

## 4. Build order

Every step ends with the tool's validator, a reload, and screenshots of the affected page, which the tool requires before a step counts as done. Pause for review after steps 3 and 6.

| Step | Work | Notes |
|---|---|---|
| 1 | **Prepare.** You save the report in Desktop, so the deletion of the two tables (D3) reaches the files and Desktop has no unsaved changes. Approve the screenshot folder. | The tool refuses to reload while Desktop has unsaved changes. Desktop and the files must agree before any file is written, or the edit and the unsaved deletion will conflict. |
| 2 | **Page setup.** Rename the page. Confirm the two table files are gone (D3), and remove them if not. Add the theme (D2). | |
| 3 | **Frame.** V12 title, V13 logo. | Optionally trim the logo's white padding in a copy. The original stays. |
| 4 | **Slicer groups.** Build the Company group first and check it renders and validates: rectangle with title and border, the slicer on top, then the group. Then clone for Campaign Type, Channel and Customer Segment, then Week. | They control everything below, so they come first. See risk R1 for the group fallback. |
| 5 | **Cards.** V1, then V2 as the template for V3 to V6. | Confirm the reference-label binding on V2 before cloning. |
| 6 | **Charts.** V7 as the template, then V8, V9, V10, each with the full-weeks filter. | |
| 7 | **Note and interactions.** V11, then the interaction table above. | |
| 8 | **Accessibility pass.** Alt text on every visual (each slicer gets alt text naming its field, because its header is hidden), tab order in reading order, a visible label on every slicer. | Reading order: title, filters left to right, week slicer, caption, cards, charts, note. |
| 9 | **Verify** (section 5). | |
| 10 | **Close out.** Update statuses and the change log in REPORT_SPEC.md. Update REPORT_DESIGN.md to match. | Nothing is published unless you ask. |

## 5. Acceptance checks

**Default state** (nothing selected):

| Card | Value | Change |
|---|---|---|
| Spend | $187.8K | ▼ 49.8% |
| Revenue | $342.5K | ▼ 55.2% |
| ROAS | 1.82 | ▼ 10.8% |
| CPA | $179.56 | ▲ 12.6% |
| Active Campaigns | 41 | ▼ 48.8% |

- The caption reads "Week of 17 Jan 2022 to 23 Jan 2022, compared with the week of 10 Jan 2022".
- The charts show 55 weekly points, from 4 Jan 2021 to 17 Jan 2022.

**Other states**

| Action | Expected |
|---|---|
| Choose 28 Dec 2020 in S1 | Change callouts blank. Caption says there is no earlier week. |
| Choose 24 Jan 2022 in S1 | Cards show the partial week. Spend change is ▼ 75%. |
| Select Influencer in S3 | The Channel slicer narrows. |
| Select one company | Cards and charts change. Charts still show all full weeks. |
| Click a point on a chart | Nothing else on the page changes. |
| Clear S1 | Cards return to the 17 Jan 2022 week. |

**Slicer groups**

- Each group shows one outline (the rectangle's border), with no second border around the dropdown.
- The five dropdowns line up down the page, and no label text is clipped.
- The label and dropdown are one unit: moving the group moves both.

All five default-state values were checked against the source CSV on 2026-10-09 (REPORT_SPEC.md, section 5).

## 6. Risks

| # | Risk | Mitigation |
|---|---|---|
| R1 | **Groups.** The tool's catalog and reference guides do not describe visual groups. The report format supports them (the existing MIP report uses them), but the validator has to confirm the group definition. | Build the Company group first. If a group cannot be validated, leave the rectangle and slicer ungrouped, with the same names and positions, and tell you. They would then not move as one object. |
| R2 | The reference label is listed by the tool as an unknown property type, so its exact encoding for a measure must be confirmed. | Confirm on V2 first. If it cannot be bound, show the change as a second value on the card and tell you. |
| R3 | The `▲`/`▼` format string has not been seen rendered (spec, known issue 4). | Check on V2 in the first screenshot. |
| R4 | The four stacked plot areas may differ by a few pixels at the left edge (design brief, open question 2). | Accept, or set the same Y-axis label width on all four. |
| R5 | **Slicer fit.** The 48 px slicer only fits with the header hidden and 8/8 padding. If theme padding changes, the validator reports the slicer below its height floor. | Declare padding on the slicer itself. Re-check after any theme change (D2). |
| R6 | The slicer sits on top of the rectangle's border zone (both are 48 px high), so a click on the top or bottom edge goes to the slicer, not the rectangle. | Harmless: the rectangle has no action. Check that the border still shows through the transparent slicer in the screenshot. |
| R7 | Card height of 144 px may clip the value or callout. | Set by screenshot review in step 5. |

## 7. Files this build touches

All inside `_Demo_4/_report/Campaign Analysis.Report/`. No semantic model files change.

| What | Change |
|---|---|
| Page definition (`pages/76578af62b77711c5f77/page.json`) | Display name, background, interactions |
| Visual folders under `pages/.../visuals/` | 28 new (23 visuals and 5 groups). The 2 Q1 table folders are removed once your Desktop deletion is saved (D3). |
| `report.json` | Theme reference (D2) |
| Theme file and logo under `StaticResources/RegisteredResources/` | 2 new |

## 8. As built (2026-10-09)

The page was generated by a script kept outside the repo, validated, loaded into Desktop and checked in screenshots. Default-state values match the acceptance table in section 5: $187.8K, $342.5K, 1.82, $179.56 and 41, with changes ▼ 49.8%, ▼ 55.2%, ▼ 10.8%, ▲ 12.6% and ▼ 48.8%. The caption reads "Week of 17 Jan 2022 to 23 Jan 2022, compared with the week of 10 Jan 2022". Screenshots were deleted afterwards.

| Item | Plan | As built | Why |
|---|---|---|---|
| Change callouts | Reference label inside each card | A second transparent card over the lower part of each KPI card, grouped with it | The reference-label property did not render in Desktop, in five encodings tried. |
| Slicer height | 48 px | 56 px, 6 px border radius | Your request. Card borders also set to 6 px. |
| Slicer groups | Group of rectangle plus slicer | As planned. Children positioned relative to the group. | Verified in Desktop. |
| Shape outline | Off | Off, and forced to zero weight and fully transparent | `show: false` alone left a blue inner line. |
| Week slicer selection | Single select, not required | "Multi-select with CTRL" setting | The strict single-select setting forces a selection and would pick the partial last week. |
| Card number font | DIN | Segoe UI Semibold | DIN fell back to a serif font. |
| Card and chart number units | Model format | Display units set to None | Automatic units scaled the value a second time ("$0.0KK"). |
| X axis | Continuous date axis, labels on V10 | Same. V7 to V9 have no X axis. | A categorical axis printed all 55 dates. A short chart hid the labels, so V10 is 190 px high. |
| Chart heights | 152 each | V7 156, V8 120, V9 120, V10 190 | To fit the labelled bottom chart above the note. |
| Chart subtitle | (not planned) | Off | Desktop adds a subtitle repeating the title. |
| Theme | Custom MIP theme | `MIP-a7c3e915.json`: colours, text classes, card border | D2. The theme could not carry the 6 px radius (the validator rejected `radius`), so radius is set on each visual. |
| Logo | Copy into the report | Registered as `MIP_logo17600000000000001.png` | Standard Desktop naming. |
| Header | Title top left, 28 px; filters at y = 80 | Title centred, 36 px, title case; 2 px grey header line at y = 90; filters at y = 104; caption at y = 168 | Your request: the filters touched the logo. The line needed the shape outline forced to zero weight, or it drew blue. |
| Slicer position | 56 px slicer filling the rectangle | 48 px slicer placed 8 px below the rectangle's top (rectangle 56 px), 8 px padding | Centres the dropdown on the title text. The validator rejects a dropdown under 48 px. |
| Filter row | Four filters in a row, Week beside the caption | All five in one row; caption under the row at the left | Your request. |
| Charts | Borderless, one stack | Bordered like the cards; two-column placement set by you in Desktop; heights raised to 176 to 190 px; X axis on all four | Your changes and request. |
| Change callout text | `▼ 49.8%` | `▼ 49.8% vs LW`, set in the five change measures' format strings | The card ignored a visual-level format code. |

**Still to do**
- Click-test the states in section 5 (week choices, filters, cascade, clicking a chart).
- Save the report in Desktop (Ctrl+S). Desktop's state now matches disk, so saving is safe.
- Update REPORT_DESIGN.md to match (spec, known issue 6).
- Do not rerun the generator script: the page has since been edited in Desktop (spec, decision 19).
