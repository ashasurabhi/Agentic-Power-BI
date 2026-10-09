# Report style standard: MIP 

How each kind of element looks, for any report. It does not say where elements go on a page; that comes from each report's own spec and design brief.


## 1. Overall look

Clean and light. White page, thin grey outline around every object, one strong blue for data, navy for a second series, and large plain numbers. No shadows. No fills other than white and a light grey title band on cards. Object titles sit inside the object, top left. The page title is top left and the logo is top right.

## 2. Page frame

| Element | Standard |
|---|---|
| Background | White `#FFFFFF` |
| Page title | Top left. Plain name of the view, regular weight, `#252423`, about 3% of page height. No subtitle. |
| Logo | [assets/MIP_logo.png](assets/MIP_logo.png) (1441 × 964 px). Top right, aligned to the right content margin. About 7% of page height, aspect ratio kept. No recolour, outline or shadow. Clear space on the left and below of at least half the logo height. The image has white padding around the mark, so size and align the visible mark, not the image edge. |
| Margins and gaps | Set once per report from the canvas size, then used for every object. Equal gaps between objects in both directions. |

## 3. Layout principles

These apply to any page and say how to arrange elements, not which ones to use.

1. **Order by importance.** Filters, then headline numbers, then charts, then tables and notes.
2. **Align.** Objects in a row share top and bottom edges, and edges line up from row to row. One grid for the report.
3. **Equal gaps.** One gap size for the whole report.
4. **Group by behaviour.** A control that affects only some visuals sits with those visuals, and its label says what it affects.
5. **Size by weight.** The most important item is the largest. Equal items, such as a row of KPI cards, have equal sizes.

## 4. Colour

| Role | Hex | Used for |
|---|---|---|
| Data blue | `#118DFF` | Primary series, bars, markers, table header underline |
| Data navy | `#12239E` | Second series (for example forecast against actual) |
| Text | `#252423` | Titles, card values, labels |
| Secondary text | `#605E5C` | Axis labels, legend text, captions |
| Outline | `#D5D5D5` | Border around every object |
| Title band | `#E6E6E6` | Fill of the title band on KPI cards |
| Last year (LY) grey | `#8A8886` | Last-year or prior-period comparison series in any chart |
| Slicer outline | `#B0B6DE` | Visual border of the rounded rectangle behind each slicer |
| Gridline | `#EEEEEE` | Chart gridlines |
| Black | `#000000` | Dashed trend line, bullet bar and target tick |
| Logo green | `#008000` | In the logo only |
| Logo navy | `#000080` | In the logo only |

**Status bands.** 

Actual and forecast are always blue and navy, in that order, in every chart that has both. Last year (LY) is always grey `#8A8886`, in every chart that compares with last year. LY is drawn behind the current series with the same line weight, and labelled "LY" in the legend. Grey is used for LY only, never for a different measure, so that grey always means "last year".

## 5. Typography

| Use | Style |
|---|---|
| Page title | About 24 px on a 737 px page, regular |
| KPI card value | Largest type in the report, about 40 px, regular, centred. Looks like DIN, the Power BI default for card values. |
| KPI card label | About 15 px, regular, centred |
| Object titles (chart, table) | About 15 px, semibold, left aligned |
| Slicer title (text of the rounded rectangle) | About 13 px, semibold |
| Axis labels, legend, data labels | About 11 to 12 px, regular |
| Axis title | About 11 px, semibold, centred under the axis |

No text below 11 px at screenshot scale. On a larger canvas, scale every size by the same factor.

## 6. Element standards

### 6.1 Every object (container)

- 1 px `#D5D5D5` border, corner radius about 4 px, no shadow, white fill.
- Title inside the object, top left, 15 px semibold. A KPI card's title is centred in its band instead.
- Transparent background with no border only for a block of charts meant to read as one group.

### 6.2 Filters and slicers

| Rule | Standard |
|---|---|
| Default type | **Dropdown.** Keeps filters to one compact row. |
| Construction | Every filter is a **group** named after its title (for example "Client"). The group holds two items: a **rounded rectangle** shape and the **native slicer** placed inside it. |
| Rounded rectangle | Shape type rounded rectangle, white fill, shape outline off. Its **text is the slicer title**: 13 px semibold `#252423`, left aligned, vertically centred, about 12 px left margin. Its **visual border** is 1 px `#B0B6DE` with a corner radius of about 4 px. The shape's name in the selection pane is the title. |
| Native slicer | Mode Dropdown, header hidden, no border, no fill, named "Slicer". Sits inside the rectangle, to the right of the title text, with a small gap to the right edge. Padding is set explicitly so the 32 px dropdown fits the rectangle height. Alt text names the field, because the header is hidden. |
| Size | Same height for every filter (about 48 px at 1080 px page height). The title area and the dropdown have the same widths in every filter, so the dropdowns line up. |
| Value | The dropdown's own selector box is the inner box, with a chevron at the right. The unfiltered state reads "All". |
| Layering | Rectangle at the back, slicer on top. Tab order: rectangle first, then slicer. |
| Selection | Multi-select by default. Single select only where exactly one value is needed (for example one week), and the title says so. |
| Order of values | Alphabetical, or natural order for dates and ranks. Newest first for long date lists. |
| Search | On for any list longer than about 10 values. |
| Alternatives | A list or tile slicer only when there are five or fewer values and space is free. A range slider for a continuous period. |
| Placement | Together in one row at the top, in a fixed order for the whole report, all the same height. |
| Cascading | A slicer that narrows another sits before it, left to right. |

### 6.3 KPI cards

| Rule | Standard |
|---|---|
| Structure | Title band at the top (`#E6E6E6`, label centred), value below, centred, on white. |
| Value | Largest type in the report, regular weight, `#252423`. |
| Size | All cards in a row have identical width and height. |
| Number format | Comes from the model. Do not override it on the card. |
| Change or reference line | If used, under the value in 15 px secondary text, centred, one neutral colour. |

### 6.4 Tables

| Rule | Standard |
|---|---|
| Frame | Standard container (6.1), title top left. A small dropdown filter may sit on the title line to the right. |
| Column header | Plain row, text `#252423`, one thin `#118DFF` line under the whole header. A small sort arrow on the sorted column. |
| Body | No row banding, no cell borders. A faint `#EEEEEE` line between rows is allowed. |
| Text columns | Left aligned. |
| Number columns | Right aligned, header right aligned too, formats from the model. |
| Row height | Room for 12 px text with space above and below, equal for every row. |
| Totals | Only if a total is meaningful. A ratio total is a ratio of sums, not a sum of ratios. Final row, semibold. |
| Conditional colour | None, unless the report is allowed to judge (section 8). |
| Long lists | Scroll inside the object. Do not shrink the text. |

### 6.5 Bar charts (horizontal)

Single colour `#118DFF`, no bar outline, thin bars with spacing about equal to the bar. Category labels at the left in 11 px. Value labels at the end of each bar, one decimal. Negative values extend left of a zero line. Light gridlines. Scroll bar when the list is long.

### 6.6 Line charts

2 px line with round markers, blue for actual and navy for forecast. Data labels on selected points only (start, end, turning points). Legend at the top left under the title, round swatches. Axis titles shown. A black dashed trend line where a trend is wanted. Light gridlines. Date axis showing years.

### 6.7 Bullet charts

One row per category, each with its own scale along the bottom. Five bands behind a black bar for the actual value and a black tick for the target. Category label at the left, scroll bar at the right. Uses the status bands, so see section 8.

### 6.8 Text and notes

Footnotes and notes in 11 to 12 px secondary text `#605E5C`, left aligned, no border, below the content they explain.

## 7. Rules to carry into every new report

1. White page, `#D5D5D5` outline around every object, no shadows.
2. Title top left, logo top right.
3. Filters in one row, in a fixed order, as dropdowns by default, each built as a group of a titled rounded rectangle with the native slicer inside it (6.2).
4. KPI cards: equal size, grey title band, large centred value.
5. Blue `#118DFF` for actual, navy `#12239E` for forecast or the second series, grey `#8A8886` for last year (LY).
6. Data labels only where they add something.
7. Every object has a title inside it, and a legend when it has two or more series.
8. Rows of objects share edges, and gaps are equal.
9. Number formats come from the model, never from the visual.
10. Tables: header with one blue underline, no banding, numbers right aligned.

## 8. Where this standard meets the Demo_4 rules

Demo_4 has rules of its own ([REPORT_SPEC.md](REPORT_SPEC.md), [REPORT_DESIGN.md](REPORT_DESIGN.md)) that take priority where they apply. This standard no longer sets layout, so the earlier layout conflicts are gone.

| Topic | This standard | Demo_4 rule | Resolution |
|---|---|---|---|
| Judging colours | Status bands in bullet charts | No judging colours (REPORT_SPEC section 2) | Do not use bullet charts or status bands on Demo_4 pages. Everything else here can apply. |
| KPI change line | Not shown | `▲`/`▼` change callout under each card value | Use the rule in 6.3: under the value, 15 px, secondary text, centred, neutral colour. |
| Second series colour | Navy `#12239E` | Grey `#616161` for Spend on V7 | Use navy. Grey `#8A8886` now means last year (LY) only, so it cannot also mean Spend. The spec already uses navy for Spend; REPORT_DESIGN.md still says grey and needs updating. |
| Line markers and labels | Round markers, selected data labels | No markers, no data labels | **Open.** Suggest no markers on series over about 20 points. Demo_4 charts have 55. |
| Table style | Header with blue underline | Header in secondary text, `#E6E6E6` row lines (REPORT_DESIGN section 4, rule 8) | Use this standard (6.4); update REPORT_DESIGN rule 8. |
| Page title size and frame | About 24 px on a 737 px page | 28 px on 1920 × 1080 | Same ratio once scaled. No conflict. |

REPORT_DESIGN.md has not been changed.

## 9. Open items

1. **Fonts.** Confirm the card-value and body fonts in the original report.
2. **Sizes.** Measured from a scaled screenshot. Take exact values from the original report file if available.
