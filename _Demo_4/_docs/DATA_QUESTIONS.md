# What the Demo_4 data can and cannot answer

Based on [DATA_PROFILE.md](DATA_PROFILE.md) and a re-read of `_data/marketing_campaign_dataset.csv` (188,400 campaign-day rows, 5,000 campaigns). `Acquisition_Cost` is treated as **daily spend**, as confirmed by the user. The folder is `_docs/`, not `docs/`.

Notation used throughout:
- **Spend** = Σ `Acquisition_Cost`, **Revenue** = Σ `Revenue`, **Conv** = Σ `Conversions`, **Clicks** = Σ `Clicks`, **Impr** = Σ `Impressions`.
- **ROAS** = Revenue ÷ Spend. **CPA** = Spend ÷ Conv. **CVR** = Conv ÷ Clicks. **CTR** = Clicks ÷ Impr. **CPC** = Spend ÷ Clicks. **Revenue per conversion** = Revenue ÷ Conv.
- Always recompute these ratios from summed numerators and denominators. Never average the row-level values.
- **Join path.** There is one flat table. Campaign attributes sit on every row, so no join is needed for them. The only join is to a calendar, which must be related on `Date`, not `Start_Date`. A separate campaign table (grain: one row per `Campaign_ID`) is a modelling convenience, not a requirement.

Whole file: Spend $162.2M, Revenue $402.8M, ROAS 2.48, CPA $129, CVR 3.72%.

---

## List 1: Questions it can answer precisely

"Precisely" means the arithmetic on the recorded values is exact. It does not mean the recorded values mean what the business assumes. See list 3 for the limits.

Ordered by how much a real decision depends on the answer.

**1. Where should spend move between campaign types and channels?**
- Columns: `Campaign_Type`, `Channel_Used`, `Acquisition_Cost`, `Revenue`, `Conversions`. Group by Type, then Channel within Type.
- Spend is almost equal across types ($31.3M to $33.7M), but return is not:

| Type / Channel | ROAS | CPA |
|---|---|---|
| Email / Email | 4.46 | $71 |
| Display / Website | 3.78 | $85 |
| Display / YouTube | 1.89 | $188 |
| Social Media (Facebook 1.84, Instagram 1.69) | 1.69–1.84 | $171–$190 |
| Influencer (YouTube 1.86, Instagram 1.52) | 1.52–1.86 | $170–$213 |
| Search / Google Ads | 1.58 | $199 |

- Equal spend with a 3x spread in return is the largest decision in the file. Search (Google Ads) is the least efficient on spend: CPC $17.87 against $3 to $6 for the others, offset by the highest CVR (9.0%). Email has the highest CTR (2.6%).

**2. Which customer segments return the most per dollar?**
- Columns: `Customer_Segment`, `Revenue`, `Acquisition_Cost`, `Conversions`.
- CPA is flat across segments ($122 to $136), but revenue per conversion is not:

| Segment | Revenue per conversion | ROAS |
|---|---|---|
| Tech Enthusiasts | $643 | 4.73 |
| Outdoor Adventurers | $394 | 3.22 |
| Fashionistas | $261 | 2.00 |
| Health & Wellness | $195 | 1.53 |
| Foodies | $130 | 1.01 |

- Foodies earn almost exactly what is spent on them. Segment drives revenue per conversion, not the cost of getting a conversion.

**3. Which campaigns return less revenue than they cost, and how much spend is in them?**
- Columns: `Campaign_ID`, `Revenue`, `Acquisition_Cost`. Aggregate to campaign level first, then filter on ROAS.
- 34.6% of campaigns have ROAS < 1. They hold 33.5% of total spend. Campaign ROAS ranges from 0 to 35, median 1.49, 25th percentile 0.76.
- "Less revenue than cost" is not "loses money". See 3.1.

**4. How have spend and return moved over calendar time?**
- Columns: `Date` (to a calendar), `Acquisition_Cost`, `Revenue`, `Conversions`, distinct `Campaign_ID` (active campaigns).
- Monthly ROAS drifts down from about 2.55 (Jan to Aug 2021) to 2.20 (Nov) and 2.02 (Jan 2022). CPA rises from about $120 to $155.
- The description of what happened is exact. The cause is not (see 2.5).

**5. Which weekdays carry the spend?**
- Columns: `Date` (weekday), `Acquisition_Cost`, `Clicks`.
- Saturday and Sunday average about $440 of spend per campaign-day against about $1,040 on Monday to Thursday and $950 on Friday. Clicks follow (about 90 against 215). This pattern is consistent across the file.
- Use it to explain calendar-day comparisons. It is not a performance difference (see 3.8).

**6. Does campaign length change return?**
- Columns: `Duration` (as a number), `Revenue`, `Acquisition_Cost`.
- No. ROAS is 2.40 / 2.49 / 2.49 / 2.50 for 15 / 30 / 45 / 60 days. Daily spend per campaign is about $835 to $870 regardless of length, so a 60-day campaign costs about 4x a 15-day one ($51.8k against $12.5k).

**7. How do Company and Target_Audience compare?**
- Columns: `Company` or `Target_Audience`, `Revenue`, `Acquisition_Cost`.
- Company ROAS runs 2.36 to 2.58 (spread 0.22). Audience ROAS runs 2.30 (Women 35-44) to 2.64 (Men 18-24). Both spreads are small next to the Type/Channel and Segment spreads in 1 and 2.
- Exact as numbers. Whether the gaps are meaningful is a separate question (2.4).

**8. Is there a ramp-up at the start of a campaign?**
- Columns: `Date` − `Start_Date` (days since start), `Acquisition_Cost`, `Clicks`, `Revenue`.
- Days 0 to 6 average $759 of spend per day against about $885 afterwards (14% lower). Revenue per day then slowly declines from about $2,280 (days 7 to 13) to $1,970 (days 45+).

**9. How many campaigns are running, by anything?**
- Columns: distinct `Campaign_ID` by any attribute or by `Date`.
- Exact, provided it is a distinct count and not a row count. Active campaigns per month: 470 in Jan, about 1,000 from Mar to Nov.
- Every campaign is fully observed: the last campaign finishes on the last date in the file. Nothing is cut off at the end.

---

## List 2: Questions it can answer approximately, with a stated assumption

Ordered by how much a real decision depends on the answer.

**1. Is the marketing profitable?**
- Answer: ROAS 2.48 overall. Profit needs margin.
- **Assumption:** a gross margin on Revenue. Break-even ROAS = 1 ÷ margin. At 50% margin, break-even is 2.0. That puts Social, Influencer and Search (ROAS 1.5 to 1.9) below break-even, with Email (4.46) and Display/Website (3.78) well above.
- **Why it is an assumption:** there is no cost of goods, fees, discounts or margin anywhere in the file. `Revenue` is not stated to be gross, net, or attributed. Every profit conclusion depends on a number the data does not hold.

**2. What does it cost to acquire a customer?**
- Answer: CPA is $129 per conversion.
- **Assumption:** each conversion is one new customer.
- **Why it is an assumption:** there is no customer ID and no new-versus-returning flag. A conversion may be a repeat purchase, a sign-up, or a lead. The column is named "Acquisition_Cost" but is not a cost per acquisition (see 3.2).

**3. What if we move budget from the weakest channels to the strongest?**
- Answer: apply the ROAS table from List 1 to a reallocated budget.
- **Assumption:** the average ROAS applies to the next dollar (constant returns), and that Email and Display/Website can absorb more spend at the same return.
- **Why it is an assumption:** the file shows what was returned on what was spent. It does not show what an extra dollar would return. Spend per campaign-day is narrowly spread ($835 to $870 average by duration), so there is little variation in spend level to test for diminishing returns. I did not test this within the rows.

**4. Which audience or segment should we favour?**
- Answer: Tech Enthusiasts and Outdoor Adventurers return most.
- **Assumption:** the differences are real, and not an artefact of which channels or companies each segment happened to be run through, and the gap in revenue per conversion reflects the segment's buying value.
- **Why it is an assumption:** segment, audience, channel and company are all assigned per campaign, so they are confounded in principle. I did not test whether they are independent of each other. The five Company and five Audience spreads are small enough that I would not call a winner without a significance test.

**5. Is performance getting worse over time?**
- Answer: ROAS falls from about 2.55 to 2.20, then 2.02, and CPA rises from $120 to $155.
- **Assumption:** months are comparable. That is, the mix of campaigns running in each month is the same.
- **Why it is an assumption:** the file has only 13 months, so seasonality cannot be separated from trend. The later months also contain different campaigns (cohort ROAS by start month falls from about 2.7 in May to 2.2 in Oct), and Jan 2022 is only 136 campaigns' tail. I did not check whether the decline is mix or a genuine change.

**6. Forecast next quarter's spend and revenue.**
- **Assumption:** 2021 repeats. **Why:** one year of data, synthetic-looking regularity (category counts nearly uniform), and no pipeline, budget or plan to anchor it.

**7. Which individual campaigns were the best?**
- Answer: rank campaigns by ROAS or revenue.
- **Assumption:** a campaign's ROAS reflects the campaign and not chance.
- **Why it is an assumption:** campaign ROAS ranges from 0 to 35, and daily values within a campaign are volatile (Conversions of 0 on one day, 2 on another). Short and low-click campaigns will dominate the extremes. I did not test how much of the spread is noise.

**8. Does revenue arrive when the spend does?**
- **Assumption:** the `Revenue` on a row belongs to that day's spend. **Why:** the file does not say whether revenue is attributed to the campaign on the day of conversion, the day of click, or the day of the order.

**9. Is Engagement_Score a quality signal?**
- Answer: ROAS rises with score (2.05 at 3 to 2.65 at 7), but the row-level correlation with revenue is about 0.01.
- **Assumption:** it measures something about audience quality.
- **Why it is an assumption:** its definition is not given, 59% of rows are 5, and the extremes are tiny (7 rows at 8, 613 at 3).

---

## List 3: Questions it cannot answer but looks like it can

These give a plausible wrong number and no error. Ordered by how much a real decision depends on the answer.

**1. "Are we making money?" / "What is the ROI?"**
- The file produces ROAS 2.48, or "revenue $402.8M less spend $162.2M = $240.6M". That is revenue less advertising only. It ignores cost of goods, fulfilment, payment fees, discounts, staff and agency fees. A $240.6M figure presented as profit or net return is wrong by an unknown, probably large, amount.
- Foodies (ROAS 1.01) look break-even and are almost certainly loss-making. High-ROAS segments may look better than they are if margins differ by segment.

**2. "What is our customer acquisition cost?"**
- The column called `Acquisition_Cost` averages **$861** per row. Someone will read that as CAC. It is the average daily spend. Using it as CAC overstates the cost about 6.7x against the $129 spend-per-conversion figure, which is itself only a cost per conversion (see 2.2).

**3. "Is Email really the best channel?" and "Is Search the worst?"**
- ROAS says Email is 2.8x Search. That does not say Email *causes* more revenue. There is no control group, holdout or baseline. Email goes to an existing list, whose members may have bought anyway. Search captures people already looking. The file measures credited revenue, not incremental revenue.
- `Campaign_Type` and `Channel_Used` are not independent: Email is only ever on Email, and Search is only ever on Google Ads. The effect of the *type* cannot be separated from the effect of the *channel* for those two. Only Display, Influencer and Social Media have more than one channel per type.

**4. "How did spend and revenue grow month on month?"**
- Monthly totals run Jan $5.4M, Feb $12.1M, Mar $15.6M. This is the number of active campaigns (470 in Jan, about 1,000 from Mar), which in turn reflects how the file starts. It is not growth in performance or scale. Likewise the Dec and Jan 2022 falls come from no campaign starting after 30 Nov. Year-on-year is impossible: there is no second January, and Jan 2021 and Jan 2022 are both partial.

**5. "What is the average conversion rate?"**
- `AVERAGE(Conversion_Rate)` over rows is **4.54%**. The correct rate (Conversions ÷ Clicks) is **3.72%**. The simple average overstates by about 22%, because low-click days with a high rate count as much as high-click days. The same trap applies to any ratio (CPC, CTR, ROAS) averaged across rows or campaigns, and to `Engagement_Score` (an unweighted mean).

**6. "Which campaign length, company or channel brings in the most revenue?" and "How many campaigns per channel?"**
- Row counts and revenue totals by `Duration` look like a result: 60-day campaigns hold 75,780 rows and $163M of revenue against 18,720 rows and $38M for 15-day. That is length, not performance (ROAS is flat, see 1.6). Row counts by any attribute are campaign-days, not campaigns. Website and Facebook look half as big as the others because each belongs to a single campaign type, not because they are smaller.

**7. "How many customers did we reach?" / "What is the frequency?"**
- Impressions (4.9 billion in total) are not people. Conversions are not unique customers. The file has no customer, device or user key, so reach, frequency, unique buyers, repeat rate and lifetime value cannot be computed. Dividing impressions by anything produces a number with no meaning as a count of people.

**8. "Which day of the week performs best?" and "Did performance drop at the weekend?"**
- Weekend spend and clicks are about half of weekday levels. Totals by weekday therefore differ by 2x. That is a scheduling pattern. The rates (CVR, ROAS) are not shown to differ, and I did not compute them by weekday.

**9. "Does higher engagement drive revenue?"**
- Average ROAS rises with score, so a chart will show a trend. The row-level correlation is about 0.01, and the two extreme scores (3 and 8) have 613 and 7 rows. The picture is real but the evidence is thin. The definition of the score is unknown.

**10. "Are we over or under budget?" / "Who hit target?"**
- There is no budget, target, forecast or plan in the file. Comparing spend with itself, or with an assumed budget, will produce a variance that means nothing.

---

## The single addition that unlocks the most

**Add a gross margin (or cost of goods) per `Customer_Segment`**: a five-row table with segment and margin %, or a margin column on a campaign or conversion basis. Segment is the right level because revenue per conversion varies 5x between segments ($130 to $643), and costs most plausibly vary with it.

What it unlocks:
- **List 2.1 (profitability)** moves from an assumption to a fact. List 3.1 (ROI/profit) stops being a plausible wrong number.
- **List 2.3 (reallocate budget)** and **2.4 (favour a segment)** change from a revenue decision to a profit decision. At a 50% margin (break-even ROAS 2.0), three of the five campaign types (Influencer, Search, Social Media) fall below break-even, and so do Foodies, Health & Wellness and Fashionistas (the last is exactly at 2.00).
- It reorders all of List 1.1 to 1.3. A channel or a segment with ROAS 1.5 to 1.9 may be losing money while one with 3.8 may not be.

**Runner-up:** a customer ID, or at least a **new-versus-returning flag** on conversions. It would settle 2.2 and 3.2 (CAC), 3.7 (reach and unique buyers) and part of 3.3 (whether Email revenue comes from existing customers). It was not chosen because it answers questions about *who*, where margin changes *whether the spend was worth it*, which is the larger decision.

Neither addition fixes 2.5 and 2.6 (trend and forecast), which need more history, or 3.10 (budget), which needs a plan table.
