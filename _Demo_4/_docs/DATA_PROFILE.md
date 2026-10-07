# Data Profile: _Demo_4/_data

Profiled 2026-10-07. Read-only; nothing was built or changed.

`_data/` holds **one file**: `marketing_campaign_dataset.csv`. There are no other tables, so there is no calendar, campaign master, company, channel or budget reference data. Anything described as a "mismatch between tables" below is therefore a mismatch inside this one file, or a table that is missing.

Note: git shows this file as modified against the last commit. The working copy (188,400 rows) differs from the committed version, which had about 200,000 rows. This profile covers the working copy.

---

## 1. marketing_campaign_dataset.csv

| Item | Value |
|---|---|
| Size | 26.3 MB, ASCII, comma-delimited, header row |
| Rows | **188,400** (188,401 lines including the header) |
| Columns | 16 |
| Full-row duplicates | 0 |
| Blanks | **0 in every column** (0.00%). Every column was read as text. No cell is empty or whitespace-only, and no cell has leading or trailing whitespace. |

### Grain of one row

**One campaign on one calendar day.** Evidence:

- 5,000 distinct `Campaign_ID` values (CMP-00001 to CMP-05000, all matching the pattern `CMP-nnnnn`) account for all 188,400 rows.
- Every campaign has exactly as many rows as its `Duration` (15, 30, 45 or 60). This holds for 100% of campaigns.
- Within a campaign, `Date` runs from `Start_Date` + 0 to `Start_Date` + (Duration − 1) days with no gaps. This holds for 100% of campaigns.
- `Clicks`, `Impressions`, `Conversions`, `Revenue`, `Acquisition_Cost`, `Engagement_Score` and `Conversion_Rate` vary row to row within a campaign (about 100% of campaigns).

This grain is inferred from the data. The file has no documentation. See U1.

### Candidate keys

| Key | Result |
|---|---|
| `Campaign_ID` alone | **Not unique.** All 5,000 IDs repeat (15 to 60 rows each, mean 37.7). |
| `Campaign_ID` + `Date` | **Unique.** 0 duplicates. This is the natural row key. |
| `Campaign_ID` + `Start_Date` + `Date` | Unique, but redundant because `Start_Date` is constant per campaign. |
| Single-column key | None. There is no row ID. |

### Column classification

**Campaign attributes.** These are constant within a `Campaign_ID` (0 campaigns with more than one value). They are descriptive and repeat on every daily row.

| Column | Distinct | Values / range |
|---|---|---|
| Campaign_ID | 5,000 | CMP-00001 to CMP-05000, complete range |
| Company | 5 | DataTech Solutions 38,745 rows; TechCorp 38,100; Alpha Innovations 37,845; NexGen Systems 37,245; Innovate Industries 36,465 |
| Campaign_Type | 5 | Influencer 39,060; Email 38,160; Social Media 37,680; Display 37,020; Search 36,480 |
| Target_Audience | 5 | Men 18-24 39,105; Men 25-34 38,400; Women 35-44 37,335; All Ages 37,065; Women 25-34 36,495 |
| Channel_Used | 6 | Instagram 38,955; Email 38,160; YouTube 37,755; Google Ads 36,480; Website 18,645; Facebook 18,405 |
| Customer_Segment | 5 | Foodies 38,790; Fashionistas 38,790; Outdoor Adventurers 37,350; Health & Wellness 36,915; Tech Enthusiasts 36,555 |
| Duration | 4 | "15 days" 18,720 rows; "30 days" 36,210; "45 days" 57,690; "60 days" 75,780 (text with a unit suffix) |
| Start_Date | 334 | 01/01/2021 to 30/11/2021 (text, dd/mm/yyyy) |

Campaign_Type and Channel_Used are not independent (the Channel_Used values are as above):

| Campaign_Type | Channel_Used values |
|---|---|
| Email | Email only |
| Search | Google Ads only |
| Display | Website, YouTube |
| Influencer | Instagram, YouTube |
| Social Media | Facebook, Instagram |

Campaigns per Campaign_Type: Influencer 1,035; Email 1,003; Social Media 1,003; Display 996; Search 963.

**Daily row attributes**

| Column | Distinct | Range / values |
|---|---|---|
| Date | 393 | 01/01/2021 to 28/01/2022, every day present. This is a continuous 393-day span with no missing dates. (text, dd/mm/yyyy) |
| Engagement_Score | 6 | Integers 3 to 8. 5 → 111,565 rows; 6 → 45,828; 4 → 28,733; 7 → 1,654; 3 → 613; 8 → 7 |

**Numeric facts** (all stored as text in the CSV. Currency columns carry `$` and thousands commas, and are quoted when ≥ $1,000.)

| Column | Min | Median | Mean | Max | Zeros | Whole-file total |
|---|---|---|---|---|---|---|
| Impressions | 27 | 13,098.5 | 25,974 | 745,832 | 0 | 4,893,483,341 |
| Clicks | 1 | 130 | 179.4 | 3,004 | 0 | 33,794,477 |
| Conversions | 0 | 4 | 6.67 | 292 | 7,574 | 1,257,130 |
| Revenue ($) | 0.00 | 965.07 | 2,137.86 | 139,328.20 | 7,574 | 402,771,884.80 |
| Acquisition_Cost ($) | 12.72 | 693.56 | 861.02 | 9,529.77 | 0 | 162,217,067.59 (daily spend, additive; see A3) |
| Conversion_Rate | 0 | 0.0303 | 0.0454 | 0.6667 | 7,574 | not summable (ratio) |
| Engagement_Score | 3 | 5 | 5.10 | 8 | 0 | not summable (score) |

Distinct counts: Conversion_Rate 2,664; Acquisition_Cost 114,377; Clicks 1,438; Impressions 65,820; Conversions 174; Revenue 138,776.

Internal consistency checks that passed:
- Clicks ≤ Impressions on every row, and Conversions ≤ Clicks on every row.
- Conversion_Rate = Conversions ÷ Clicks, to within 0.00005 (a rounding difference at 4 decimals). It is not Conversions ÷ Impressions.
- Revenue = 0 exactly when Conversions = 0 (7,574 rows). There are no rows with revenue but no conversions, or conversions but no revenue.

### Date ranges

| Column | Min | Max |
|---|---|---|
| Start_Date | 01/01/2021 | 30/11/2021 |
| Date | 01/01/2021 | 28/01/2022 |

- Both columns are dd/mm/yyyy. Every value matches the pattern and parses as day-first. Day-part values reach 31 and month-part values reach 12, so the format is unambiguous.
- Date − Start_Date ranges from 0 to 59 days and is never negative.
- Date is always inside the campaign window. Only 1,659 rows (0.9%) fall in 2022, all in January. Every `Start_Date` is in 2021.
- Rows per month of Date: Jan 8,595; Feb 14,178; Mar 17,290; Apr 17,166; May 16,934; Jun 16,713; Jul 18,037; Aug 17,413; Sep 16,687; Oct 18,080; Nov 17,303; Dec 10,004; Jan 2022 1,659 (the 1,659 is also counted in the 2022 total above). The edges are thin because campaigns only start between January and November and ramp in and out.

---

## 2. Structural problems and ambiguities

Items marked **[UNSURE]** are things I could not settle from the data alone. I have not resolved them.

### Grain and structure

**S1. Single flat table, no dimension tables.** Campaign attributes (8 columns) are repeated on all 188,400 daily rows. No campaign master, date table, company, channel, audience or segment table exists in `_data/`. Any dimension must be derived from this file. The Campaign grain (5,000) and the fact grain (188,400) are mixed in the same table.

**S2. Two date columns with different roles, and the name `Date` is ambiguous.** `Start_Date` is a campaign attribute (the date the campaign began). `Date` is the daily row date. A date relationship to a calendar must use `Date`, not `Start_Date`. Using `Start_Date` would collapse each campaign into one day. **[UNSURE]** whether `Start_Date` is meant to be a second, role-playing date dimension or just an attribute.

**S3. Row grain is inferred, not documented. [UNSURE]** The data strongly suggests daily rows (see Grain above). Nothing in the file states whether each row is a daily figure, a running cumulative figure, or something else. Some metrics (Clicks, Impressions) behave like daily values, since they bounce around rather than growing. I did not test every metric for cumulative behaviour.

**S4. Duration is text and redundant.** Values like "45 days". It equals the row count per campaign, so it can be derived. It would need a number extracted before it can be used numerically or sorted correctly (as text, "15 days" < "30 days" < "45 days" < "60 days" happens to sort fine, but this is accidental).

### Data type problems (cannot be used as-is)

**T1. Currency stored as text.** `Acquisition_Cost` and `Revenue` contain `$`, thousands commas, and quotes. They must be cleaned before they can be summed.

**T2. Dates stored as text, day-first.** `Start_Date` and `Date` are dd/mm/yyyy strings. A loader with a US locale will misread them, and any day ≤ 12 will silently swap day and month rather than fail. This is a real risk, because only dates with day > 12 would error.

**T3. Percentages stored as decimals.** `Conversion_Rate` is 0.0625, not 6.25%. Format, not value, is the issue.

### Columns that cannot be summed as they stand

**A1. Conversion_Rate** is a per-row ratio. Summing it or averaging it simple-mean (mean 0.0454) gives a different answer from the weighted rate, Σ Conversions ÷ Σ Clicks = **0.0372**. It must be recomputed from Conversions and Clicks at any aggregation level.

**A2. Engagement_Score** is a 3–8 integer score. It is not additive. **[UNSURE]** how it is calculated and whether an average is meaningful, or whether it should be weighted (by impressions or clicks). It has only 6 distinct values, and 59% of rows are 5.

**A3. Acquisition_Cost is daily spend (confirmed by the user, not derivable from the data).** It is additive across rows. The name is misleading: it is not a per-acquisition cost. Total spend is $162.2M against $402.8M revenue. Cost per acquisition must be derived as Σ Acquisition_Cost ÷ Σ Conversions (about $129), and ROAS as Σ Revenue ÷ Σ Acquisition_Cost (about 2.48). Both must be calculated at the aggregation level, not averaged per row. Rows with zero conversions still carry spend, so CPA is undefined (divide by zero) at row level for those 7,574 rows. The column should probably be renamed, for example Daily Spend, in any model. Revenue ÷ Conversions per row ($41 to $1,854, mean $321) is revenue per conversion and is a different measure.

**A4. Campaign attributes cannot be summed or counted by row.** Counting rows by Company or Channel counts campaign-days, not campaigns. Campaign counts need a distinct count of `Campaign_ID`. Also, Duration "weights" campaigns by length in any row-based count (60-day campaigns have 4× the rows of 15-day ones).

**A5. Distinct-count and share metrics need care.** Because every campaign repeats 15–60 times, any measure that is a count of rows is a count of campaign-days.

### Columns needing a decision before use

**D1. Channel_Used vs Campaign_Type overlap.** Campaign_Type "Email" has Channel_Used "Email" in 100% of its rows, and "Search" always has "Google Ads". The two columns partly duplicate each other. Channel_Used "Website" and "Facebook" appear in only about half as many rows as the other channels. Decide which is the primary analytic dimension, or whether they form a hierarchy (Type → Channel).

**D2. Target_Audience vs Customer_Segment** are two separate audience descriptors (demographic vs interest). They are constant per campaign and independent of each other. **[UNSURE]** whether they should be treated as separate dimensions or combined. Target_Audience covers only 5 values, and mixes gender-and-age bands with an "All Ages" catch-all. These overlap in meaning ("All Ages" does not say anything about gender).

**D3. Company.** 5 values, constant per campaign. **[UNSURE]** whether this is the advertiser (the client running the campaign) or the owner of the data. The names look like generic placeholders.

**D4. Engagement_Score has a very thin tail**: 7 rows score 8, and 613 score 3. Do not slice on it without bucketing.

### Outliers and anomalies (not changed or removed)

- **Conversion_Rate > 0.3** on 655 rows, up to 0.6667. That means 2 conversions from 3 clicks. These are small-denominator rows.
- **Acquisition_Cost > $5,000** on 201 rows. **Revenue > $50,000** on 170 rows (maximum $139,328). **Clicks > 1,000** on 748 rows, **Impressions > 100,000** on 8,578 rows. All values are positive and internally consistent. **[UNSURE]** whether they are real spikes or data-generation artefacts. They are not necessarily errors.
- **7,574 rows (4.0%)** have zero conversions, zero revenue and zero conversion rate. This is consistent with a real zero day. Each such row still has Clicks ≥ 1 and an Acquisition_Cost > 0.
- The Date range is all of 2021 plus 28 days of 2022. A year-based view will show 2022 as a stub of 1,659 rows, and the Dec/Jan edges are partial because campaigns do not start after 30 Nov.

### Missing reference data

- No **calendar/date table**. Date covers 393 contiguous days, which makes a calendar easy to derive, but none is provided. There is no fiscal calendar definition.
- No **campaign master** with a budget, a target, a name or an owner. `Campaign_ID` is only a code.
- No **budget, target or forecast** data, so nothing to compare against actuals.
- No lookup for **Channel**, **Audience** or **Segment** (for sort order or grouping).
- **No currency stated**. The `$` sign is assumed to be USD. **[UNSURE]**.
- **No documentation** of column definitions (particularly Engagement_Score, Conversion_Rate and Date).

### Other observations

- Rows are not sorted by `Campaign_ID` or `Date` in the file.
- Campaign_ID is complete: 1 to 5000 with no gaps.
- Starts are spread roughly evenly across Jan–Nov 2021 (411 to 503 campaigns per start month).
- Row counts per category are close to uniform (about 36,000 to 39,000 each), except Website, Facebook (about half) and Duration (weighted by length). The data looks synthetic. **[UNSURE]** whether that matters for the intended demo.
- Existing memory notes record a Demo_4 model built live in Desktop (Campaign Results / Campaign / Date). I did not inspect or touch it. This profile is from the CSV alone, and I did not check whether the model's choices match the points above.
