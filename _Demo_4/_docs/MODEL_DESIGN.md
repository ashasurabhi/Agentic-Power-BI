# Model design: Demo_4 campaign model

Written for: the person who approves the design before the build. Design only. No tables, relationships or measures have been created, and the existing live model (see section 9) was not inspected.

Inputs: [MODEL_BRIEF.md](MODEL_BRIEF.md), [DATA_PROFILE.md](DATA_PROFILE.md). Anything not in the brief is marked **[ASSUMPTION]** and collected in section 9.

---

## 1. Shape

A star schema with one fact table, two dimensions, and a table that only holds measures. Import mode, from the one CSV.

```
   Campaign (5,000 rows)             Date (393 rows)
   key: Campaign_ID                  key: Date
          │ 1                               │ 1
          │                                 │
          ▼ *                               ▼ *
        ┌──────────── Campaign Results (188,400 rows) ────────────┐
        │ Campaign_ID, Date, daily spend/revenue/clicks/...       │
        └──────────────────────────────────────────────────────────┘

   Measures (no rows; holds every measure; no relationships)
```

## 2. Tables

| # | Table | Type | Grain | Rows | Source |
|---|---|---|---|---|---|
| 1 | Campaign Results | **Fact** | One campaign on one day | 188,400 | CSV |
| 2 | Campaign | **Dimension** | One campaign | 5,000 | CSV (deduplicated) |
| 3 | Date | **Dimension** (date table) | One calendar day | 393 | Generated |
| 4 | Measures | Measure container (neither) | None | 0 | Empty table |

### 2.1 Campaign Results (fact)

**Grain:** one campaign on one day. **Unique key:** `Campaign_ID` + `Date` (0 duplicates in the file). There is no single-column key and the model does not need one.

| Column | Source column | Type | Notes |
|---|---|---|---|
| Campaign_ID | Campaign_ID | Text | Join to Campaign |
| Date | Date | Date | Parsed **day-first** (dd/mm/yyyy). Join to Date. |
| Daily Spend | Acquisition_Cost | Fixed decimal | `$` and commas stripped. Daily spend, additive. |
| Daily Revenue | Revenue | Fixed decimal | `$` and commas stripped. |
| Daily Clicks | Clicks | Whole number | |
| Daily Impressions | Impressions | Whole number | |
| Daily Conversions | Conversions | Whole number | A conversion is a purchase. |

Dropped at load: `Conversion_Rate`, `Engagement_Score` (brief, section 5). The campaign attributes are not carried on this table. They live on Campaign.

Columns are renamed `Daily …` so that the measures can take the plain business names (Spend, Revenue and so on). A measure and a column cannot share a name without ambiguity in DAX **[ASSUMPTION]**.

### 2.2 Campaign (dimension)

**Grain:** one campaign. **Key:** `Campaign_ID` (unique, 5,000 values, CMP-00001 to CMP-05000). Built from the fact source by taking the distinct combinations of the columns below. Every attribute is constant within a campaign (0 exceptions in the profile), so one row per `Campaign_ID` results, and the load should be checked for exactly 5,000 rows.

| Column | Source column | Type | Notes |
|---|---|---|---|
| Campaign_ID | Campaign_ID | Text | Key |
| Company | Company | Text | The five advertisers |
| Campaign_Type | Campaign_Type | Text | Top level of the hierarchy |
| Channel_Used | Channel_Used | Text | Lower level of the hierarchy |
| Customer_Segment | Customer_Segment | Text | |
| Target_Audience | Target_Audience | Text | Kept, not used by the four questions |
| Duration (Days) | Duration | Whole number | "45 days" converted to 45. Summarization set to none. |
| Start_Date | Start_Date | Date | Parsed day-first. Attribute only. Not related to Date. |
| Type and Channel | calculated | Text | e.g. "Display / Website". Label so that Instagram under Influencer and under Social Media are not mixed up in a visual **[ASSUMPTION]**. |

Hierarchy: Campaign_Type → Channel_Used. This is needed because Instagram appears under two types, so a channel alone does not identify a row in a within-type comparison.

### 2.3 Date (dimension)

**Grain:** one calendar day. **Key:** `Date`. Marked as the date table.

**Generation:** a calculated table (DAX `CALENDAR`) from 1 Jan 2021 to 28 Jan 2022, taken as the minimum and maximum of `Campaign Results[Date]` so that it follows the data and has every day. That is 393 days with no gaps (brief, section 6). It deliberately does not extend to whole years or whole weeks, so the first and last weeks are partial. Partial weeks are kept (brief).

| Column | Definition |
|---|---|
| Date | The day. Key. |
| Year | Calendar year. No fiscal year (brief). |
| Month Number | 1 to 12. |
| Month | Month name, sorted by Month Number. |
| Year Month | e.g. "2021-03", for sorting across years. |
| Week Start | The Monday on or before the date. The week key for Q4. Can fall before 1 Jan 2021 for the first week (Monday 28 Dec 2020). |
| Week Label | Text of the week start, sorted by Week Start. |
| Days In Week | Number of calendar-table days in that week (7, except 1 Jan 2021's week with 4 and the last week with 5). **[ASSUMPTION]** |
| Is Current Week | True for the week containing the last date in `Campaign Results` (Monday 24 Jan to Friday 28 Jan 2022). Evaluated at refresh. |

`Days In Week` is an addition so that partial weeks can be labelled. The brief asks for them to be kept, not labelled.

## 3. Relationships

| # | From (many) | To (one) | Join columns | Cardinality | Filter direction | State |
|---|---|---|---|---|---|---|
| R1 | Campaign Results | Campaign | `Campaign_ID` = `Campaign_ID` | Many to one | Single, Campaign → Campaign Results | **Active** |
| R2 | Campaign Results | Date | `Date` = `Date` | Many to one | Single, Date → Campaign Results | **Active** |

No bidirectional relationships. Measures has none.

### Inactive relationships

**None.** One was considered and rejected: Campaign[Start_Date] → Date[Date]. The user confirmed that no launch-date view is wanted ("campaigns started this week", ROAS by launch month), so `Start_Date` stays an attribute with no relationship. It would also create a second date path from Campaign Results through Campaign, which would make "which date am I filtering on" ambiguous. If a "campaigns starting this week" view is wanted later, it would be an inactive relationship used with a measure, or a separate role-playing date table. That is a new requirement, not part of this brief.

## 4. Measures

All sit in the Measures table. Ratios divide sums of numerators and denominators and return blank when the denominator is zero. Currency measures use the `$` symbol. AUD is not stated anywhere in the model.

**Base (additive)**

1. **Spend**: sum of `Daily Spend`.
2. **Revenue**: sum of `Daily Revenue`.
3. **Conversions**: sum of `Daily Conversions`.
4. **Clicks**: sum of `Daily Clicks`.
5. **Impressions**: sum of `Daily Impressions`.

**Ratios**

6. **ROAS**: Revenue ÷ Spend.
7. **CPA**: Spend ÷ Conversions.
8. **CTR**: Clicks ÷ Impressions.
9. **CVR**: Conversions ÷ Clicks.
10. **CPC**: Spend ÷ Clicks.

**Counts**

11. **Active Campaigns**: distinct count of `Campaign Results[Campaign_ID]` in the current filter context. This counts campaigns with at least one row in the filter, for example in the week. It is deliberately taken from the fact, not from Campaign, which ignores dates.

**Q3: campaigns that return less than they cost**

12. **Campaigns Below ROAS 1**: number of campaigns, in the current filter context, whose ROAS is below 1.0. A campaign with no rows or with zero spend is not counted.
13. **Share of Campaigns Below ROAS 1**: measure 12 ÷ Active Campaigns.
14. **Spend in Campaigns Below ROAS 1**: Spend of those same campaigns.
15. **Share of Spend in Campaigns Below ROAS 1**: measure 14 ÷ Spend.

**Q1 and Q2: ranks by ROAS** (no flag, no benchmark)

16. **ROAS Rank in Type**: rank of a channel's ROAS among the channels of its own campaign type, in the current filter context (the Channel filter is removed, the Type filter is kept). Blank if the type has fewer than two channels to compare **[ASSUMPTION]**.
17. **ROAS Rank across Types**: rank of a campaign type's ROAS among all types **[ASSUMPTION: the brief ranks channels within type and is silent on ranking types]**.
18. **ROAS Rank of Segment**: rank of a customer segment's ROAS among all segments.

**Not built, by the brief:** CAC, any profit or margin measure, revenue per conversion, anything using `Engagement_Score` or `Conversion_Rate`, any week-over-week or year-over-year comparison, any flag or label such as "underperforming".

Display: ratio and currency measures use a format string that shows K or M automatically (thousands when the number is small enough, millions otherwise). ROAS to two decimals and CTR/CVR as percentages **[ASSUMPTION]**.

## 5. Q4 and the weekly view

Weekly trend: `Date[Week Start]` on the axis, measures 1, 2, 6, 7 and 11. The current-week default is `Date[Is Current Week]`. The last week has 5 days and few campaigns still running, so its totals will be low (brief, section 6). Ratios are less affected.

## 6. Data preparation (design, not built)

- **One staged source query** reads the CSV with every column as text, then two queries derive from it by reference: Campaign Results (keeps the key, date and measure columns) and Campaign (keeps the attribute columns, removes duplicates on `Campaign_ID`).
- Dates parsed with a day-first locale (English Australia, `dd/mm/yyyy`). A US locale would swap day and month silently for any day of 12 or less.
- `$` and thousands commas stripped before converting to numbers.
- Checks to run at load: Campaign = 5,000 rows; Campaign Results = 188,400 rows; no key duplicates on `Campaign_ID` + `Date`; no `Campaign_ID` in Campaign Results missing from Campaign; no date outside the Date table.

## 7. Field visibility and summarization

The brief says not to hide any field, so no column is hidden, including the key columns (`Campaign_ID` on both tables, `Date`). To stop wrong implicit sums, `Duration (Days)` and the date and text columns are set to **no summarization**. The `Daily …` columns are additive, so they keep Sum **[ASSUMPTION: that visible additive columns with default Sum are acceptable]**.

## 8. Descriptions

Every measure and the renamed columns get a one-line description. `Daily Spend` says: "Spend per campaign per day. Source column Acquisition_Cost. This is spend, not a cost per acquisition." The ratio measures say that they must not be averaged.

---

## 9. What the brief does not tell me, and what I would have to assume

**Not in the brief at all**

1. **Currency symbol.** Resolved: `$`, with no currency code anywhere in the model, descriptions included.
2. **Number formats of ratios.** Only totals have an "automatic K/M" rule. I would show ROAS with 2 decimals, CPA and CPC as currency, CTR and CVR as percentages with 1 decimal for CTR and 2 for CVR.
3. **Naming.** The brief gives metric names but not column names. I would rename the fact columns `Daily …` and put the plain names on measures in a separate Measures table.
4. **Ranking types.** The brief ranks channels within type and does not say whether types are ranked against each other (measure 17).
5. **What a rank shows for single-channel types** (Email, Search), for ties, and for items with no spend. I would return blank for a set of fewer than two, and give tied values the same rank.
6. **Minimum evidence for a rank or for Q3.** None given. I would apply none, so a campaign with one day of data can rank or count as below 1. Campaign ROAS spans 0 to 35.
7. **Refresh.** The brief wants a weekly report but the file ends 28 Jan 2022 and does not change. I would assume a manual refresh and no schedule. The source file path is also not given.
8. **Sort orders** for segments, types, channels and companies. I would sort alphabetically.
9. **Report layout and defaults.** The brief covers the model and the questions. It does not say what the manager sees first. I assume `Is Current Week` as the default filter on the weekly page, and nothing else.
10. **Partial-week label.** The brief keeps partial weeks, but not whether to mark them. `Days In Week` is my addition.
11. **Hiding keys and raw columns.** I read "do not hide any fields" as including keys and the raw `Daily …` columns.

**Inherited from the brief's open items**

12. **Revenue is gross or net, attributed or not.** Treated as gross, as recorded.
13. **How Q3 behaves under a date filter.** I assume it is computed over the rows in the current filter. A campaign is judged on the days in view, not its whole life. In a week-by-week view the same campaign can fall below 1 one week and not the next.
14. **The stop-a-campaign rule** is undefined. Nothing is built for it.
15. **Revenue per conversion** was not confirmed as a measure. Not included.

**About the existing model**

16. My memory notes record a Demo_4 model already built live in Power BI Desktop (Campaign Results, Campaign, Date, no measures), unsaved. I have not looked at it. It may differ from this design (column names, the `Conversion_Rate` and `Engagement_Score` columns, how Date was made). Before building I would inspect it and decide whether to change it or replace it. This design assumes either is allowed.

**Untested statements from the profile that the design inherits**

17. That every campaign attribute is constant per `Campaign_ID` (verified: 0 exceptions) and that `Campaign_ID` + `Date` is unique (verified).
18. That `Start_Date` is always on or before `Date` and inside the campaign window (verified for this file; not enforced in the model).
19. Not tested: whether segment, channel and company differences are independent of each other, whether the late-2021 ROAS decline is real or a change in the mix of campaigns, and whether returns fall at higher spend. The model reports ROAS as recorded and makes no claim about cause.
