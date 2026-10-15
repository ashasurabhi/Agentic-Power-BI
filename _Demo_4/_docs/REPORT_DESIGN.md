# Report design brief: Demo_4 weekly overview

| | |
|---|---|
| Report | `_Demo_4/_report/Campaign Analysis.pbip` |
| Implements | [REPORT_SPEC.md](REPORT_SPEC.md): visuals V1 to V11, slicers S1 to S5. IDs below are the spec's IDs. |
| Canvas | 1920 × 1080 px, 16:9, "Fit to page" (the page is already set this way). All sizes below are px on that canvas. |
| Theme | `Fluent2-CY26SU09`, already applied. No custom theme. Colours below are values from that theme. |
| Status | Design only. No report files have been created or changed. |

---

## 1. Design intent

The manager opens this on a Monday, alone, to answer one question: **did last week go the way the weeks before it did?** The page therefore reads in three steps, top to bottom:

1. **What was last week?** Five numbers, each with its change from the week before.
2. **How did it get there?** Four trend lines over the full period, stacked on one shared time axis.
3. **Where do I narrow this?** Filters, kept small and out of the way.

Nothing on the page says whether a number is good or bad. The manager judges by eye (spec, section 1), so the design shows size and direction only and adds no judging colour, target or flag.

## 2. Page 1: Weekly overview

### 2.1 Layout

Margins 40 px left and right, 24 px top and bottom. A 5-column grid: columns 348 px wide, gutters 25 px, columns start at x = 40, 413, 786, 1159, 1532. Five columns because there are five cards. Everything else snaps to the same columns.

```
y=24   ┌──────────────────────────────────────────────────────────────────────────────┐
       │ Weekly overview                                              (page title)    │
y=80   ├─────────────┬─────────────┬─────────────┬─────────────┬────────────────────────┤
       │ S2 Company  │ S3 Type     │ S4 Channel  │ S5 Segment  │                        │  filters: all visuals
y=144  ├─────────────┼─────────────┴─────────────┴─────────────┴────────────────────────┤
       │ S1 Week     │ V1 caption: Week of 17 Jan 2022 to 23 Jan 2022, compared with…  │  selected week
y=200  ├─────────────┼─────────────┬─────────────┬─────────────┬────────────────────────┤
       │ V2 Spend    │ V3 Revenue  │ V4 ROAS     │ V5 CPA      │ V6 Active Campaigns    │  cards
y=344  ├─────────────┴─────────────┴─────────────┴─────────────┴────────────────────────┤
y=368  │ V7  Spend and Revenue by week                                                    │
y=532  │ V8  ROAS by week                                                                 │  trend,
y=696  │ V9  CPA by week                                                                  │  shared
y=860  │ V10 Active Campaigns by week                                                     │  time axis
y=1012 ├──────────────────────────────────────────────────────────────────────────────────┤
y=1020 │ V11 note on partial weeks                                                        │
y=1056 └──────────────────────────────────────────────────────────────────────────────────┘
```

| ID | Item | x | y | w | h |
|---|---|---|---|---|---|
| Title | Page title | 40 | 24 | 900 | 40 |
| S2 | Company | 40 | 80 | 348 | 48 |
| S3 | Campaign Type | 413 | 80 | 348 | 48 |
| S4 | Channel | 786 | 80 | 348 | 48 |
| S5 | Customer Segment | 1159 | 80 | 348 | 48 |
| S1 | Week | 40 | 144 | 348 | 48 |
| V1 | Week caption | 413 | 144 | 1467 | 48 |
| V2 | Spend | 40 | 200 | 348 | 144 |
| V3 | Revenue | 413 | 200 | 348 | 144 |
| V4 | ROAS | 786 | 200 | 348 | 144 |
| V5 | CPA | 1159 | 200 | 348 | 144 |
| V6 | Active Campaigns | 1532 | 200 | 348 | 144 |
| V7 | Spend and Revenue by week | 40 | 368 | 1840 | 152 |
| V8 | ROAS by week | 40 | 532 | 1840 | 152 |
| V9 | CPA by week | 40 | 696 | 1840 | 152 |
| V10 | Active Campaigns by week | 40 | 860 | 1840 | 152 |
| V11 | Partial-week note | 40 | 1020 | 1840 | 36 |

The fifth column of the filter strip (x = 1532) is left empty on purpose, so the filters line up with the cards below them and there is room for one more filter later.

### 2.2 Hierarchy: what the eye reads first

| Rank | Item | How the design makes it so |
|---|---|---|
| 1 | Cards V2 to V6 | Largest type on the page (40 px values), full-width row directly under the caption. A change callout sits under each value. |
| 2 | Caption V1 and week slicer S1 | Sit immediately above the cards, because they say *which* week the numbers belong to. Without the caption, the cards have no date. |
| 3 | Spend and Revenue chart V7 | First chart, the only one with two lines, and the same left-to-right order as the first two cards. |
| 4 | V8, V9, V10 | Same height and style as V7, in the same order as cards V4 to V6. |
| 5 | Filters S2 to S5, note V11 | Smallest type, grey. They must be findable, not noticed. |

Chart order matches card order (Spend, Revenue, ROAS, CPA, Active Campaigns), so a column in the cards maps to a row in the trend.

### 2.3 What is above the fold

The page is one 16:9 screen and does not scroll, so nothing is below the fold at the intended size. If the report is opened in a small window, Fit to page scales it down and the order of importance stays the same. In priority order, the first 350 px of the canvas (y = 24 to 344) must hold everything needed to answer the Monday question, **without clicking anything**:

- the title,
- the week the cards describe (V1),
- all five cards with their change from the previous week (V2 to V6).

The trend charts, the filters other than S1, and the note come after that.

### 2.4 Visual choice for each item, and why

| ID | Visual | Why this type | Rejected |
|---|---|---|---|
| Title | Text box | The page needs a name. | A banner image or logo: nothing in the brief asks for branding. |
| V1 | Text card showing `Selected Week Label` | It is a sentence that changes with S1, so a measure in a text visual is the only way to keep it current. The wording gives both weeks, so the manager can see what the change is measured against. | A static title: it would go out of date. |
| V2 to V6 | New Card visual, one measure each, with the change measure as the reference label | A single number is the message, and the change is a secondary fact about it. The reference label keeps the two in one object, so they cannot drift apart or be separated by a resize. | A multi-row card: every number would have equal weight. KPI visual: it implies a target, and the brief has none. Sparkline in the card: not in the spec, and the trend is directly below. |
| V7 | Line chart, two series (Spend, Revenue) | Weekly values are a continuous series, and the gap between two lines of the same unit is the point. Both are in `$`, so one axis is honest. | Columns: 55 columns each with a second series gets crowded. Dual-axis combo: unnecessary because the units match. |
| V8 | Line chart, one series (ROAS) | A ratio over time. | Putting it on V7's axis: ROAS is about 2, Spend is in the millions, so it would be a flat line. |
| V9 | Line chart, one series (CPA) | Same reason, and a different unit (`$` per conversion) from ROAS (a plain ratio). A line chart has no secondary axis, so they are separate charts. | One chart with two axes: unreadable at this height, and invites reading ROAS and CPA as related lines. |
| V10 | Line chart, one series (Active Campaigns) | A count over time. Line, not columns, to match the other three so the stack reads as one family. | Columns: would look like a different kind of measure. |
| V11 | Text box | A footnote. Static text, read once. | A tooltip or info icon: the note must be visible without clicking (spec, section 1). |
| S1 | Dropdown, single select, searchable, newest first | 57 weeks do not fit as a list, and the manager usually leaves it empty. Newest first because recent weeks are the ones picked. | List or tile slicer: too tall. Between-slicer or date range: S1 is a single week, not a range. |
| S2 to S5 | Dropdown, multi-select | Five or fewer values each, but four slicers in list form would take the height of the cards. Dropdowns keep the filter strip to one row of 48 px. | Lists: take too much space. Hierarchy slicer for S3/S4: spec asks for two slicers; the default interaction between them gives the cascade. |

**Chart details that follow from the above**

- Line charts, line weight 2 px, no markers, no area fill, no data labels. Tooltips show the week (`Week Label`) and the value.
- X axis is `Week Start` as a date axis. Axis labels (month and year) show on V10 only, the bottom chart. V7 to V9 keep light vertical gridlines at the same positions so the eye can follow a week down the stack.
- Y axis: V7 and V10 start at **0** (amounts and counts, where the size of the swing matters). V8 and V9 use an automatic range (ratios that move in a narrow band; a zero baseline would flatten them). Every Y axis shows its numbers, so the baseline is never hidden.
- Y axis number format comes from the model (K/M units, `$`, `0.00`). Do not override it on the chart.
- Chart titles are plain language and name what is plotted, for example "Spend and Revenue by week". No chart title contains "efficient", "underperforming", "on track", "typical" or "CAC".

### 2.5 Interactions

| From | To | Setting |
|---|---|---|
| S1 | V7 to V10 | Off |
| S1 | V2 to V6, V1 | On (they use the week) |
| S2 to S5 | everything | On |
| S3 | S4 | On (this is the cascade) |
| V7 to V10 | each other and the cards | **Off.** Clicking a point would filter the other charts to one week and collapse them. |
| Cards | everything | Off. A card is a readout, not a filter. |

## 3. Style rules (tokens)

All values come from the Fluent2 theme already in the report. Use them by name, not by new hex codes.

**Colour**

| Token | Value | Used for |
|---|---|---|
| Page background | `#FFFFFF` | Page |
| Text | `#242424` | Titles, card values, axis labels |
| Secondary text | `#616161` | Card labels, change callouts, captions, note, slicer headers |
| Accent | `#118DFF` (theme colour 1) | The one series on V8 to V10, Revenue on V7 |
| Second series | `#616161` | Spend on V7 |
| Border | `#D1D1D1` | Card and slicer outline, 1 px |
| Gridline | `#E6E6E6` | Chart gridlines, horizontal and vertical |

No red, green, amber or any conditional colour. No theme "good", "bad" or "neutral" colours. `#118DFF` on white has a contrast of about 3.4:1, which is fine for a line but too low for text, so **no text is set in the accent colour**.

**Type** (Segoe UI, the theme font)

| Use | Size | Weight |
|---|---|---|
| Page title | 28 | Semibold |
| Chart title | 16 | Semibold |
| Card value | 40 | Semibold |
| Change callout | 16 | Regular, secondary text colour |
| Card label, caption V1, slicer text | 14 | Regular |
| Axis labels, note V11 | 12 | Regular |

Nothing is below 12.

**Space and shape**

- Margins 40 (sides) and 24 (top, bottom). Gutters 25 between cards and slicers, 24 between bands (filters, week, cards, trend), 12 between stacked charts.
- Cards and slicers: white fill, 1 px `#D1D1D1` border, 4 px corner radius, no shadow.
- Charts: no border, no fill, so they sit on the page. The stack of four charts is one block.
- Card content, left aligned: label (14, secondary) on top, value (40) in the middle, change callout (16, secondary) at the bottom. All five cards use the same layout so the values line up across the row.

## 4. Consistency rules across pages

This build has one page. These rules apply to every page added later, including the Q1 to Q3 pages that are not in this build, so that they look like the same report.

1. **Same frame.** Same canvas, margins, 5-column grid, page title position (40, 24) and title size.
2. **Same filter strip.** Filters sit in a single row at y = 80, in the order Company, Campaign Type, Channel, Customer Segment. A page that does not need one of them leaves the slot empty rather than moving the others. A control that behaves differently from the rest (like S1, which affects cards only) is placed *with the thing it controls*, not in the filter strip, and its title says what it affects.
3. **Same meaning for filters.** Company, Type, Channel and Segment filter everything on the page. If a page ever needs a filter to leave some visuals alone, say so in the slicer title.
4. **One colour per measure, report-wide.** Revenue is the accent colour and Spend is secondary grey on every page. A new measure takes a theme colour not yet used and keeps it everywhere.
5. **Neutral colour only.** No judging colours on any page. A ranking (Q1, Q2) is shown as a rank number, not as a colour. A below-1 count (Q3) is shown as a number, not a red marker. This is the brief's "rank only, no flag, no benchmark" rule applied to the visuals.
6. **Same number formats.** Use the model's format strings. Never set a format on a visual that differs from the measure.
7. **Same words.** Measure names are the business names in the model (Spend, Revenue, ROAS, CPA, Active Campaigns). Do not rename a measure on a visual, and do not use "efficient", "underperforming", "on track", "typical" or "CAC".
8. **Same cards, same charts.** A card or line chart on a later page uses the sizes, type and line style in section 3. A page with a table follows the table rule: header in secondary text, row lines `#E6E6E6`, no banding colour, numbers right aligned.
9. **Same partial-week treatment.** Anywhere weeks are plotted, only full Monday-to-Sunday weeks are plotted (`Date[Days In Week]` = 7), and the page carries the note. A week-based card may show a partial week if the user picks one.
10. **Same page name pattern.** A noun phrase naming the view ("Weekly overview"), not a question or a verdict. Page 1 is currently named "Page 1" and needs renaming.
11. **Same reading order.** Tab order follows reading order: title, filters left to right, week slicer, caption, cards left to right, charts top to bottom, note.

## 5. Accessibility

- Direction is carried by the `▲` / `▼` shape, not by colour.
- The two series in V7 differ in lightness as well as hue (accent blue against mid grey), and the legend names them. The legend sits at the top left of V7.
- Every visual has alt text stating what it shows, for example "Line chart of weekly ROAS, full weeks only".
- All text is 12 px or larger. Secondary text `#616161` on white is about 6:1.
- Every slicer has a visible title.

## 6. Additions this brief implies for the spec

These are not in REPORT_SPEC.md today. Add them before building.

1. **Interaction settings** from section 2.5, in particular chart-to-chart off. Spec section 4 only covers S1.
2. **Slicer style** (dropdown) and S1 sort order (newest first). The spec says S1 is sorted by `Week Start` and does not give a direction.
3. **Y axis rule** (zero baseline on V7 and V10, automatic on V8 and V9).
4. **Page rename** to "Weekly overview" (spec, known issue 1).

## 7. Open design questions

| # | Question | Default used above |
|---|---|---|
| 1 | The charts do not show which week the cards describe, because they ignore S1. A highlighted marker on the selected week would link them, but needs another measure and is not in the spec. | No marker. V1 states the week in words. |
| 2 | Stacked, aligned charts (this brief) trade height for a shared time axis. Plot areas may differ by a few pixels in left edge because Y-axis label widths differ. | Accept the difference. If it bothers, set the same Y-axis label width on all four charts. |
| 3 | Two existing tables on "Page 1" (Q1 ranking) are not part of this layout (spec, known issue 1). | They are not placed. Remove or move them before building this page. |
| 4 | The default week shows large falls, which are not a partial-week effect (spec, known issue 3). Whether V11 should say why changes the footnote text, not the layout. | Footnote as in the spec. |
