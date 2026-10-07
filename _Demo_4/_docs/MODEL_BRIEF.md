# Model brief: Demo_4 campaign model

Written for: the builder of the semantic model and report. It records the answers given in the brief interview on 2026-10-07. Nothing here was inferred silently. Anything not answered is listed under "Open items".

Source: `_data/marketing_campaign_dataset.csv`. Profile: [DATA_PROFILE.md](DATA_PROFILE.md). Question analysis: [DATA_QUESTIONS.md](DATA_QUESTIONS.md).

---

## 1. Users and decisions

| Item | Decision |
|---|---|
| User | A marketing manager. One person, across all five advertisers (`Company`). No row-level security. |
| Cadence | Weekly report. |
| Decision 1 | Move spend between channels. |
| Decision 2 | Stop a campaign. **The rule for this is undefined** (see section 5). |
| Decision 3 | Pick the next campaign's customer segment. |

`Company` is an advertiser. The five are five separate advertisers, not clients, competitors or the data owner.

## 2. Questions the model must answer

These are the first four questions in List 1 of DATA_QUESTIONS.md.

**Q1. Where should spend move between campaign types and channels?**
- Columns: `Campaign_Type`, `Channel_Used`, Spend, Revenue, Conversions.
- Rank by **ROAS**, with CPA shown alongside. CPA never decides the rank.
- Channels are compared **within** their campaign type (Display: Website vs YouTube; Influencer: Instagram vs YouTube; Social Media: Facebook vs Instagram). Email and Search have one channel each, so there is nothing to compare within them.
- **Rank only. No flag and no benchmark.** The portfolio-ROAS flag (below 2.48) was considered and withdrawn. If a flag is wanted later, it is a new decision.

**Q2. Which customer segments return the most per dollar?**
- Columns: `Customer_Segment`, Spend, Revenue, Conversions.
- Rank by ROAS. CPA is shown alongside.

**Q3. Which campaigns return less revenue than they cost, and how much spend is in them?**
- Campaign level (`Campaign_ID`), ROAS below 1.0 over the campaign's days to date.
- Shown as a count of campaigns, share of campaigns, and share of spend.
- **No "underperforming" label.** It is a plain fact: revenue is less than spend.

**Q4. How have spend and return moved over time?**
- Weekly, weeks starting Monday. Measures: Spend, Revenue, ROAS, CPA, Active Campaigns.
- Active Campaigns is the distinct count of `Campaign_ID` with at least one row in the week.
- No year-on-year. The file has no second January.

## 3. Metric definitions

Every ratio is calculated from summed numerators and denominators at the level shown. A ratio is never summed, and never averaged across rows or campaigns. A divide by zero returns blank.

| Metric | Definition |
|---|---|
| Spend | Σ `Acquisition_Cost`. **Daily spend**, confirmed by the user. Additive. |
| Revenue | Σ `Revenue`. Daily revenue for the campaign. Gross or net is not stated. Treat as gross, as recorded. |
| Conversions | Σ `Conversions`. A conversion is a **purchase**. |
| Clicks | Σ `Clicks`. |
| Impressions | Σ `Impressions`. |
| ROAS | Revenue ÷ Spend. |
| CPA | Spend ÷ Conversions. |
| CTR | Clicks ÷ Impressions. |
| CVR | Conversions ÷ Clicks. |
| CPC | Spend ÷ Clicks. |
| Active Campaigns | Distinct count of `Campaign_ID` in the current filter context. |
| Campaigns below ROAS 1 | Count of campaigns whose ROAS in the current filter context is below 1.0. Companion measures: share of campaigns, share of spend. |

Naming: `Acquisition_Cost` is misleading, because it is spend and not a cost per acquisition. In the model, use **Spend** as the business name. Do not use "CAC" anywhere.

## 4. Judgment words

| Word | Status |
|---|---|
| Best / worst, "return most" | **Defined.** Highest and lowest ROAS, by rank. Within campaign type for channels. |
| Return | ROAS. |
| Underperforming | **Not defined.** The user declined to define it. The model must not label anything with it. |
| Efficient | Not used. Not defined. Do not use it. |
| On track | Not used. No targets exist in the data. Do not use it. |
| Typical | Not used. Not defined. Do not use it. |
| Moved (Q4) | Week-by-week values of the Q4 measures. No threshold for a "significant" move. |

## 5. Rulings on profile ambiguities

| Issue (profile) | Ruling |
|---|---|
| Grain | One row per campaign per day. `Campaign_ID` + `Date` is the key. |
| `Date` vs `Start_Date` | The calendar relates on `Date`. `Start_Date` is a campaign attribute and does not relate to the calendar. (The `Date` relationship was the builder's default. The user then confirmed that no launch-date view is wanted, so `Start_Date` stays unrelated.) Both are text in dd/mm/yyyy format and must be parsed **day-first**. |
| Daily spend | `Acquisition_Cost` is daily spend. Additive. Called Spend. |
| Currency text | `Acquisition_Cost` and `Revenue` contain `$` and commas and must be converted to numbers. |
| Rate columns | `Conversion_Rate` is **left out** of the model. Rates are measures built from sums. |
| `Engagement_Score` | **Left out** of the model. |
| Duration, `Start_Date`, Clicks, Impressions, Company, Target_Audience | **Not hidden.** The user said not to hide any field. All are visible. `Duration` must be converted from text such as "45 days" into a number. |
| Campaign counts | Distinct count of `Campaign_ID`. Never a row count. |
| Type vs channel | Type → Channel hierarchy. Comparisons are within type. |
| Audience vs segment | Both are kept as separate fields. Only `Customer_Segment` is used in the four questions. |
| Missing reference data | No budget, target, margin or customer data. Not added. See out of scope. |

## 6. Calendar

- **Calendar year and calendar month.** No fiscal year, because nothing in the four questions needs one. The user asked why it would be needed, and no fiscal calendar was defined.
- **Week starts on Monday.**
- **Partial weeks are kept**, not excluded. 1 Jan 2021 is a Friday, so the first week is partial.
- **Current week = the last week in the data**: Monday 24 Jan to Friday 28 Jan 2022 (5 days). It is not the real current date.
- A "vs last week" comparison of totals for the current week will show a large fall, because the week has 5 days and few campaigns still running. This is an artefact, not performance. Ratios (ROAS, CPA) are less affected.
- The calendar covers every day from 1 Jan 2021 to 28 Jan 2022 (393 days, no gaps).

## 7. Currency and display

- Currency symbol: **`$`**. No currency code is shown anywhere in the model or report. No conversion.
- Display: automatic units. Use thousands (K) when the number is small enough, and millions (M) when K would show too many digits.

## 8. Out of scope

The model will not answer these. The data cannot support them (see List 3 in DATA_QUESTIONS.md).

- Profit, margin or true ROI
- Customer acquisition cost (CAC) and customer counts
- Reach, frequency, lifetime value
- Incrementality (whether a channel causes the revenue)
- Budget versus actual, or any target
- Forecasting
- The rule for stopping a campaign (the decision itself is acknowledged, but its rule is undefined)
- Anything involving `Engagement_Score` or the raw `Conversion_Rate`

## 9. Open items

These were not answered. A default is stated where one is needed to build. Confirm or change them before building.

1. **Stop-a-campaign rule.** Threshold, window and minimum evidence are undefined. The user said to leave this. The model will show only the Q3 fact (ROAS below 1.0).
2. **Revenue basis.** Daily revenue for the campaign, but gross or net, and attributed or not, is unknown. Treated as gross, as recorded.
3. **Q3 time scope.** "Over the campaign's days to date" was confirmed, but not how it behaves with a date filter. Default: ROAS is computed over the rows in the current filter context, and campaigns with no rows in the context are not counted. Small campaigns and short windows will swing the result (campaign ROAS ranges from 0 to 35).
4. **Minimum evidence for ranking.** No minimum spend or days before a campaign or channel is ranked. None applied.
5. **Revenue per conversion.** It explains the segment result in Q2 (about $130 for Foodies, $643 for Tech Enthusiasts). It was not requested or confirmed as a measure. Not included unless added.
6. **Existing model.** Memory notes record a Demo_4 model built live in Power BI Desktop (Campaign Results, Campaign, Date, no measures), unsaved. It was not inspected. It may still contain `Conversion_Rate` and `Engagement_Score`, which are to be removed when building.
7. **Untested assumptions from the profile.** Whether segment, channel and company differences are independent of each other, whether the late-2021 ROAS decline is real or a change in campaign mix, and whether returns diminish at higher spend. The model reports ROAS as recorded and makes no claim about cause.
