# Decisions

Decisions made while building the model from [MODEL_DESIGN.md](MODEL_DESIGN.md). Decisions only, not a log of actions.

## 2026-10-07

### Build target
- **Decided:** build in the open, untitled Power BI Desktop model (local port 49801).
- **Why:** the design names no target. The model was empty (0 tables), and the one my notes describe as already built was gone, so nothing was overwritten. The tool's instance list returned nothing, but a Desktop process was running and its local Analysis Services port was found directly.
- **Rejected:** a PBIP folder scaffolded on disk (more files, nothing in the design asks for it).
- **Consequence:** the model is unsaved. It has not been saved to a PBIX or PBIP.

### Staged source query
- **Decided:** one shared query, `Campaign Source`, reads the CSV with all columns as text. Campaign and Campaign Results derive from it. The file path is written into it directly.
- **Why:** the design (section 6) specifies one staged query. A path parameter is not in the design.
- **Rejected:** a `Data Folder` or file-path parameter (an extra object). Separate CSV reads in each table (the design asks for one staged source). The modelling guideline against a shared query behind table queries was overruled by the design.

### Dates
- **Decided:** dates are parsed in the query with an explicit day-first format and the `en-AU` culture.
- **Why:** the profile showed that a wrong locale silently swaps day and month for days of 12 or less. Naming the format and culture makes the result independent of the machine's settings.
- **Rejected:** relying on the model's default type detection.
- **Display:** date columns show as `dd/MM/yyyy`. The design is silent. This matches the source and `en-AU`.

### Currency columns
- **Decided:** `Daily Spend` and `Daily Revenue` load as fixed decimal numbers (model type Decimal, four decimal places).
- **Why:** they are currency and must sum exactly. Totals match the profile to the cent (spend $162,217,067.59, revenue $402,771,884.80).
- **Rejected:** floating point.

### Date table
- **Decided:** the Date table is a DAX calculated table, `CALENDAR` from the minimum to the maximum `Campaign Results[Date]`. It is marked as the date table with data category Time.
- **Why:** the design says so.
- **Rejected:** a Power Query calendar (the modelling guideline prefers it).
- **Result:** 393 rows, 01/01/2021 to 28/01/2022, 57 weeks.

### Date table columns
- **Decided:** `Is Current Week` is a true/false column. `Week Label` is formatted `dd mmm yyyy`. `Year Month` is `yyyy-mm`. `Month` is the full month name.
- **Why:** the design gives the meaning but not the format. True/false matches "True for the week that…".
- **Rejected:** "Yes"/"No" text for `Is Current Week`. A `Week Label` such as "Week of …" (longer, no gain).

### Summarization
- **Decided:** summarization is off for `Duration (Days)`, `Year`, `Month Number`, `Days In Week`, `Date`, `Week Start` and all text columns. The five `Daily …` columns keep Sum.
- **Why:** the design (section 7) turns summarization off for date and text columns and for `Duration (Days)`, and keeps Sum on the additive columns. Year, month number and days in week are numbers that must not be added up, so I treated them the same way.
- **Rejected:** hiding the `Daily …` columns, as the modelling guideline suggests. The brief says no field is hidden. `discourageImplicitMeasures` was left as it was, because the design does not ask for it.

### Relationships
- **Decided:** two active, single-direction, many-to-one relationships, named `Campaign Results to Campaign` and `Campaign Results to Date`.
- **Why:** the design (section 3). Checked after build: 0 fact rows without a campaign, 0 without a date. 2021 spend plus 2022 spend equals total spend.
- **Rejected:** any inactive relationship (the user confirmed that no launch-date view is wanted).

### Hierarchy
- **Decided:** one hierarchy on Campaign, `Type and Channel Hierarchy`, with levels `Campaign Type` then `Channel`.
- **Why:** the design asks for a Type → Channel hierarchy. The names are mine.
- **Rejected:** the level names `Campaign_Type` and `Channel_Used`, which carry the source's underscores.

### Descriptions
- **Decided:** descriptions on the renamed `Daily …` columns, `Duration (Days)`, `Start_Date`, all new Date columns, `Type and Channel`, and every table.
- **Why:** the design (section 8) requires descriptions on renamed columns and measures. Table and Date column descriptions are an addition so that the grain is recorded.
- **Rejected:** none.

### The Measures table: name
- **Decided:** the measure table is named `_Measures`. The user chose this name after the engine rejected `Measures` as a reserved word. I capitalised it to match the other table names.
- **Why:** the design's name could not be built. The user named the replacement.
- **Rejected:** putting the measures on Campaign Results (the design keeps them in a separate container).

### The Measures table: placeholder column
- **Decided:** `_Measures` has one column, `Placeholder`, and it is hidden. The table has no rows.
- **Why:** Desktop cannot hold a table with no columns, and the design asks for an empty container.
- **Rejected:** leaving it visible (a field with no data would appear in the field list). This is the one hidden field. It departs from "do not hide any field", and it holds no data from the file.

## 2026-10-07: measures

### Display of totals
- **Decided:** Spend, Revenue, Conversions, Clicks, Impressions and Spend in Campaigns Below ROAS 1 use a dynamic format string. A value of 1,000,000 or more shows in M, 1,000 or more in K, otherwise in full (currency to two decimals), each to one decimal place for K and M.
- **Why:** the brief says totals display with automatic units, K or M.
- **Rejected:** a fixed K or M format (wrong for part of the range). Visual-level display units (they would not travel with the measure). A B (billions) tier, because the brief names K and M only, so Impressions of 4.9 billion shows as 4,893.5M.
- **Fixed format strings:** ratios, rates and counts do not scale. ROAS is `0.00`, CPA and CPC are `$#,##0.00`, CTR is `0.0%`, CVR is `0.00%`, counts and ranks are whole numbers. The design left these open.
- **Technical note:** a measure cannot have both a fixed and a dynamic format string. The scaling commas have to sit before the decimal point, so the K and M formats are `#,0,.0\K` and `#,0,,.0\M`.

### Q3 counting rules
- **Decided:** a campaign is counted below ROAS 1 when its spend is above zero and Revenue ÷ Spend is under 1, over the rows in the current filter. Campaigns are taken from Campaign Results, not Campaign.
- **Why:** the design says a campaign with no rows or zero spend is not counted. In DAX an empty ratio compares as less than 1, so it needs the explicit spend test.
- **Rejected:** counting from Campaign, which ignores the date filter.

### Ranks
- **Decided:** a rank is blank unless it applies. `ROAS Rank in Type` needs one type and one channel in view and at least two channels with a ROAS. `ROAS Rank across Types` is blank when a channel is in view. `ROAS Rank of Segment` needs one segment in view. Other filters (date, company) still apply to every rank. Ties take the same rank, and the next rank is skipped.
- **Why:** the design says blank for fewer than two channels, and the brief says channels are compared within type. The conditions stop a channel being ranked against another type's channels.
- **Rejected:** removing the date filter from the comparison set (the ranking would not match the numbers on screen). Dense ranking (not stated in the design, so I used the default).
- **Tested:** within-type ranks are Display Website 1, YouTube 2; Influencer YouTube 1, Instagram 2; Social Media Facebook 1, Instagram 2; Email and Search blank. Types: Email 1, Display 2, Social Media 3, Influencer 4, Search 5. Segments: Tech Enthusiasts 1 to Foodies 5.

### Checked against the profile
- Whole-file values match: Spend $162,217,067.59, Revenue $402,771,884.80, ROAS 2.48, CPA $129.04, CTR 0.69%, CVR 3.72%, CPC $4.80, 5,000 active campaigns, 1,731 below ROAS 1 (34.6% of campaigns, 33.5% of spend). All 18 measures are in a ready state with no errors.

### Design error found
- **Found:** `MODEL_DESIGN.md` says the first week has 4 days. It has 3 (Friday 01/01/2021 to Sunday 03/01/2021). The current week has 5 days, as the design says. `Days In Week` is correct in the model. The design text was not changed.
