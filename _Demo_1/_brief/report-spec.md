# Report Spec

> Canonical path: `C:\Agentic-Power-BI\_Demo_1\_brief\report-spec.md`. Status: **awaiting approval**. Nothing has been built.
> Later authoring steps must carry this absolute path, because the working directory is not `_Demo_1`.

## Report identity
- Report name: Campaign Analysis (existing PBIP `C:\Agentic-Power-BI\_Demo_1\_report\Campaign Analysis.pbip`, one blank 1920 x 1080 page)
- Semantic model: Campaign Analysis (open in Power BI Desktop, saved as `Campaign Analysis.SemanticModel`)
- Audience: marketing leadership (page 1) and marketing analysts (pages 2 and 3). **Assumed, not stated by the user.**
- Primary purpose: show where 2021 campaign spend went and test whether any channel, campaign type, audience, segment, city, language or company performs differently, so budget shifts are only made on real differences.
- Delivery target: local PBIP first. Publishing to Fabric is out of scope until decided separately.

## Semantic model binding
- Entry mode:          Local Model
- Workspace name:      n/a
- Workspace ID:        n/a
- Model name:          Campaign Analysis
- Model ID:            n/a
- connectionString:    byPath `../Campaign Analysis.SemanticModel` (already in `definition.pbir`)

## User decisions and constraints
- Scope: three pages. Portfolio summary, an explore-anything page, and a benchmark scan. Deferred: drill-through pages, tooltip pages, bookmarks, mobile layout.
- Page count: 3
- Interactivity: navigation model is the auto page navigator, top centre-right of every page. Page 1 has one Month slicer. Page 2 has a metric selector and a breakdown selector (field parameters) plus a five-slicer left rail with a Reset button. Page 3 has a single metric selector. No drill-through. No bookmarks.
- Design direction: Corporate Cool, cool-grey surface, slate text, hue per measure.
- Publishing: none in this pass.
- Tooling: Power BI Desktop is open with the model. The modeling MCP is connected. Report authoring uses `powerbi-report-author` against the PBIP.
- Model edit permissions: approved in principle for the additions under *Model requirements*; each write still needs the normal permission prompt.
- Accessibility: WCAG AA text contrast, 3:1 non-text contrast, alt text on every visual, data labels as a second cue wherever colour carries meaning, tab order set to reading order.
- Data caveats:
  1. **The data behaves like randomly generated data.** Measured on the full year, no member of any dimension differs from the portfolio average by more than 0.69% on ROI, click-through rate, conversion rate, engagement or cost per click. Every channel shows about 10% click-through, 8% conversion and ROI near 5.0. The report is designed to show that honestly and must not manufacture winners.
  2. **No baseline exists.** One year only (2021), no plan, target or prior period. Comparisons are against the portfolio average. KPI cards cannot show period-over-period deltas.
  3. **Volume follows the calendar.** About 548 campaigns start every day, so monthly counts and spend rise and fall with days in the month (February is lowest).
  4. Conversion rate has no stated denominator, and ROI has no stated definition (ratio, 5.0 taken to mean 5x). Currency is assumed USD from the `$` in the source.
  5. The Date table spans 2020 to 2030 but data exists only for 2021, so no Year slicer is used.

## Narrative
- Core story: 2021 spend was steady and evenly spread, and returns are the same everywhere you look.
- Audience promise: leaders see the answer in one glance; analysts can prove it themselves by cutting any metric by any dimension.
- Key questions answered:
  1. How much did we spend, what did we get, and was it steady month to month?
  2. Where did the money go?
  3. Does any channel, type, audience, segment, city, language or company beat the average?
  4. Where do I look if I want to check?

## Design identity (from the `design` mode Step 1)
- Tone: Corporate Cool. Cool grey `#F1F5F9` surface, white 1px-bordered visuals with 8px radius, slate `#334155` text, Segoe UI.
- Signature: every measure keeps one fixed hue on every page, and every comparison bar is neutral slate against a dashed 0% "portfolio average" baseline, so the eye never lands on a manufactured winner.
- Brownfield delta: n/a (greenfield, the page is blank).

## Page plan (archetypes from the `design` mode Step 3)
1. **Spend and returns held steady across all of 2021**
   - Archetype: Executive Summary
   - Layout variant: B (KPI-Strip). Five comparable KPIs and no single hero metric.
   - Purpose: one-glance status for leadership.
   - Visuals: five KPI cards (# Campaigns, Total Acquisition Cost, Click-Through Rate, Average Conversion Rate, Average ROI); monthly spend line; ROI vs portfolio by channel; spend by channel; spend by campaign type.
   - Fields/measures: existing measures plus `ROI vs Portfolio (%)`.
   - Slicers/interactions: Month Name dropdown (only slicer). Cross-filtering into the ROI-vs-portfolio chart is switched off.
2. **Pick a metric and a breakdown to see where performance differs**
   - Archetype: Analytical Canvas
   - Layout variant: A (Filter-Rail). Five dimension slicers plus Reset exceed the inline limit and fill more than half the rail.
   - Purpose: let analysts test any metric against any dimension.
   - Visuals: ranked bar of the chosen metric by the chosen breakdown; monthly trend of the chosen metric; Channel x Campaign Type cross-tab; campaign detail table.
   - Fields/measures: `Metric` and `Break Down By` field parameters over existing measures and columns.
   - Slicers/interactions: metric selector, breakdown selector, rail (Channel, Campaign Type, Target Audience, Customer Segment, City), Reset button. Company, Language, Duration and Month are in the filter pane.
3. **No segment strays more than 1% from the portfolio average**
   - Archetype: Comparative Benchmark
   - Layout variant: A (Side-by-Side), adapted: eight small panels against one baseline, no headline chart, because there is no derived insight to justify one.
   - Purpose: prove or disprove that any segment outperforms.
   - Visuals: eight variance bar panels (Channel, Campaign Type, Company, Duration, Target Audience, Customer Segment, City, Language) on identical fixed axes.
   - Fields/measures: `Variance Metric` field parameter over five new `... vs Portfolio (%)` measures.
   - Slicers/interactions: one metric selector. No date slicer, so the title stays true. Panel-to-panel cross-filtering is switched off.

## Design system summary
- Theme name + base palette: `Campaign Corporate Cool`, adapted from `references/design/assets/base.json`. Surface `#F1F5F9`, visuals `#FFFFFF`, borders `#E2E8F0`, text `#334155`, secondary text `#475569`.
- Color semantics: Total Acquisition Cost slate `#334155`. Average ROI and ROI vs portfolio cyan `#0E7490`. Click-Through Rate indigo `#4338CA`. Conversion rate amber `#B45309`. # Campaigns mid-slate `#64748B`. Comparison bars neutral slate. No red/green semantics anywhere, because no value is good or bad.
- Typography pairing: Segoe UI Semibold for titles and KPI values, Segoe UI for body. Four sizes only: 32 (KPI values), 20 (page title), 12 (visual titles), 10 (axis, labels, captions).
- Layout pattern: 1920 x 1080, 12 x 12 grid, 32px margin, 24px gutter, 8px snap, one header band per page.
- Accessibility commitments: contrast pairs verified (slate on grey 7.0:1, secondary text on grey 6.9:1, cyan on white 5.4:1, amber on white 5.0:1, indigo on white 7.9:1). Data labels on all bars. Bars start at zero. No dual axes. No gauges, pies or heat-map gradients.

## Model requirements
- Existing measures: `# Campaigns`, `Total Acquisition Cost`, `Average Acquisition Cost`, `Total Clicks`, `Total Impressions`, `Click-Through Rate`, `Average Conversion Rate`, `Average ROI`, `Cost-Weighted ROI`, `Average Engagement Score`, `Cost per Click`, `Estimated Conversions`, `Cost per Estimated Conversion`.
- New measures (table `Campaigns`, folder `Benchmark`, each `(measure - CALCULATE(measure, ALLSELECTED())) / CALCULATE(measure, ALLSELECTED())` via DIVIDE, format `+0.0%;-0.0%;0.0%`): `ROI vs Portfolio (%)`, `Click-Through Rate vs Portfolio (%)`, `Conversion Rate vs Portfolio (%)`, `Engagement Score vs Portfolio (%)`, `Cost per Click vs Portfolio (%)`. The `ALLSELECTED()` form was already tested and returns correct values.
- New field parameter tables: `Metric` (Average ROI, Cost-Weighted ROI, Click-Through Rate, Average Conversion Rate, Average Engagement Score, Cost per Click, Average Acquisition Cost); `Variance Metric` (the five new measures); `Break Down By` (Channel, Campaign Type, Target Audience, Customer Segment, City, Language, Company, Duration Days).
- New calculated columns: none.
- Relationship/sort requirements: none. Month Name already sorts by Month Number.
- Follow-ups after build: save the model to the PBIP again so the new objects persist.

## Canonical design contract

```yaml
Design Brief:
  generated_by: powerbi-report-cli
  contract_version: 1
  mode: greenfield
  design_identity:
    tone: "Corporate Cool: cool grey #F1F5F9 surface, white visuals with 1px #E2E8F0 border and 8px radius, slate #334155 text, Segoe UI Semibold titles, Segoe UI body, solid low-opacity gridlines"
    signature: "Every measure keeps one fixed hue on every page; every comparison bar is neutral slate against a dashed 0% portfolio-average baseline, so no manufactured winner ever draws the eye"
  archetype: "Executive + Drill: Executive Summary landing, Analytical Canvas exploration, Comparative Benchmark scan"
  navigation_model: page_navigator
  color_map:
    - measure: Campaigns[# Campaigns]
      color: "#64748B"
      tint: "#E2E8F0"
    - measure: Campaigns[Total Acquisition Cost]
      color: "#334155"
      tint: "#E2E8F0"
    - measure: Campaigns[Click-Through Rate]
      color: "#4338CA"
      tint: "#E0E7FF"
    - measure: Campaigns[Average Conversion Rate]
      color: "#B45309"
      tint: "#FEF3C7"
    - measure: Campaigns[Average ROI]
      color: "#0E7490"
      tint: "#CFFAFE"
    - measure: Campaigns[ROI vs Portfolio (%)]
      color: "#0E7490"
      tint: "#CFFAFE"
  pages:
    - name: "Spend and returns held steady across all of 2021"
      role: landing
      archetype: Executive
      layout_variant: B
      variant_rationale: "Five KPIs of comparable importance and no single hero metric; there is no prior period or target, so the KPI strip carries scope captions instead of deltas."
      navigation:
        - element: page_navigator
          action: PageNavigation
          target: "all pages"
          label: "Page navigator"
      page_background: "#F1F5F9"
      layout_summary: "KPI strip, monthly spend line with a ROI-vs-portfolio check beside it, then two spend-allocation bars."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:       [1, 1, 6, 2]
            nav:          [6, 1, 9, 2]
            filters:      [9, 1, 13, 2]
            kpis:         [1, 2, 13, 4]
            trend:        [1, 4, 8, 9]
            movers:       [8, 4, 13, 9]
            spend_channel: [1, 9, 7, 13]
            spend_type:   [7, 9, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "Spend and returns held steady across all of 2021"
            purpose: "State the finding before any chart."
          - id: page_nav
            region: nav
            kind: pageNavigator
            purpose: "Move between the three pages."
            alt_text: "Page navigation: Portfolio summary, Explore, Benchmark scan"
          - id: month_slicer
            region: filters
            kind: slicer
            field_bindings: Date[Month Name]
            slicer_type: dropdown
            insight_basis: "Data is annual grain (all 2021), so a month dropdown is used instead of a Year or date-range slicer."
          - id: kpi_campaigns
            region: kpis
            kind: cardVisual
            purpose: "How many campaigns ran?"
            field_bindings: Campaigns[# Campaigns]
            color_strategy: measure_match
            reference_label: "Campaigns in selected months"
            slot: 1
            of: 5
          - id: kpi_spend
            region: kpis
            kind: cardVisual
            purpose: "How much was spent?"
            field_bindings: Campaigns[Total Acquisition Cost]
            color_strategy: measure_match
            reference_label: "Acquisition cost in selected months"
            slot: 2
            of: 5
          - id: kpi_ctr
            region: kpis
            kind: cardVisual
            purpose: "How often did an impression become a click?"
            field_bindings: Campaigns[Click-Through Rate]
            color_strategy: measure_match
            reference_label: "Clicks / impressions, selected months"
            slot: 3
            of: 5
          - id: kpi_conversion
            region: kpis
            kind: cardVisual
            purpose: "What is the typical campaign conversion rate?"
            field_bindings: Campaigns[Average Conversion Rate]
            color_strategy: measure_match
            reference_label: "Mean of campaign rates, selected months"
            slot: 4
            of: 5
          - id: kpi_roi
            region: kpis
            kind: cardVisual
            purpose: "What is the typical campaign return?"
            field_bindings: Campaigns[Average ROI]
            color_strategy: measure_match
            reference_label: "Mean of campaign ROI (ratio), selected months"
            slot: 5
            of: 5
          - id: monthly_spend
            region: trend
            kind: lineChart
            purpose: "Was spend steady month to month?"
            title: "Monthly spend tracks days in the month, not performance"
            field_bindings: { Category: "Date[Date] at month grain", Y: "Campaigns[Total Acquisition Cost]" }
            value_axis: { start: 0 }
            color_strategy: measure_match
            alt_text: "Line of monthly acquisition cost across 2021, between about 191 and 214 million dollars; February is lowest because it has the fewest days."
          - id: roi_vs_portfolio_channel
            region: movers
            kind: barChart
            purpose: "Does any channel earn a different return from the portfolio average?"
            title: "ROI vs portfolio average, by channel"
            field_bindings: { Category: "Channel[Channel]", Y: "Campaigns[ROI vs Portfolio (%)]" }
            sort_policy: value_desc
            value_axis: { start: -0.05, end: 0.05 }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Average ROI of all campaigns in the selected months"
            interactions: "receives slicer filters only; cross-filtering from other visuals off"
            alt_text: "Comparing ROI across 6 channels against the portfolio average. All channels sit within about one third of one percent of it."
          - id: spend_by_channel
            region: spend_channel
            kind: barChart
            purpose: "Where did the money go, by channel?"
            title: "Acquisition spend by channel"
            field_bindings: { Category: "Channel[Channel]", Y: "Campaigns[Total Acquisition Cost]" }
            sort_policy: value_desc
            value_axis: { start: 0 }
            color_strategy: measure_match
            alt_text: "Acquisition spend across 6 channels, each about 411 to 421 million dollars."
          - id: spend_by_type
            region: spend_type
            kind: barChart
            purpose: "Where did the money go, by campaign type?"
            title: "Acquisition spend by campaign type"
            field_bindings: { Category: "Campaign Type[Campaign Type]", Y: "Campaigns[Total Acquisition Cost]" }
            sort_policy: value_desc
            value_axis: { start: 0 }
            color_strategy: measure_match
            alt_text: "Acquisition spend across 5 campaign types, each about 500 million dollars."
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: trend, pct_of_content: 27 }
          balance_rationale: "KPI strip (2 rows) gives every card value plus caption. The monthly line gets the largest region because it proves the headline; the variance check beside it answers 'does anything stand out'. Two allocation bars share the bottom band equally. No footer or spare band."
    - name: "Pick a metric and a breakdown to see where performance differs"
      role: detail
      archetype: Analytical
      layout_variant: A
      variant_rationale: "Five dimension slicers plus a Reset button exceed the three-slicer inline limit and fill about 55% of the rail height, so a filter rail is justified."
      navigation:
        - element: page_navigator
          action: PageNavigation
          target: "all pages"
          label: "Page navigator"
        - element: button
          action: ClearAllSlicers
          label: "Reset filters"
      page_background: "#F1F5F9"
      layout_summary: "Filter rail on the left; a hero bar driven by two field parameters; trend and cross-tab beneath; campaign detail table at the bottom."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:  [1, 1, 6, 2]
            nav:     [6, 1, 9, 2]
            filters: [9, 1, 13, 2]
            rail:    [1, 2, 3, 13]
            hero:    [3, 2, 13, 6]
            trend:   [3, 6, 8, 9]
            matrix:  [8, 6, 13, 9]
            detail:  [3, 9, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "Pick a metric and a breakdown to see where performance differs"
            purpose: "Tell the analyst what the page is for."
          - id: page_nav
            region: nav
            kind: pageNavigator
            purpose: "Move between the three pages."
            alt_text: "Page navigation: Portfolio summary, Explore, Benchmark scan"
          - id: metric_slicer
            region: filters
            kind: slicer
            field_bindings: Metric[Metric]
            slicer_type: dropdown
            default_selection: "Average ROI"
            slot: 1
            of: 2
          - id: breakdown_slicer
            region: filters
            kind: slicer
            field_bindings: "Break Down By[Break Down By]"
            slicer_type: dropdown
            default_selection: "Channel"
            slot: 2
            of: 2
          - id: rail_channel
            region: rail
            kind: slicer
            field_bindings: Channel[Channel]
            slicer_type: dropdown
            slot: 1
            of: 6
          - id: rail_type
            region: rail
            kind: slicer
            field_bindings: "Campaign Type[Campaign Type]"
            slicer_type: dropdown
            slot: 2
            of: 6
          - id: rail_audience
            region: rail
            kind: slicer
            field_bindings: "Target Audience[Target Audience]"
            slicer_type: dropdown
            slot: 3
            of: 6
          - id: rail_segment
            region: rail
            kind: slicer
            field_bindings: "Customer Segment[Customer Segment]"
            slicer_type: dropdown
            slot: 4
            of: 6
          - id: rail_city
            region: rail
            kind: slicer
            field_bindings: Location[City]
            slicer_type: dropdown
            slot: 5
            of: 6
          - id: rail_reset
            region: rail
            kind: actionButton
            action: ClearAllSlicers
            label: "Reset filters"
            slot: 6
            of: 6
            alt_text: "Clears every slicer on this page"
          - id: metric_by_breakdown
            region: hero
            kind: barChart
            purpose: "How does the chosen metric compare across the chosen breakdown?"
            title: "Selected metric by selected breakdown"
            field_bindings: { Category: "Break Down By (field parameter)", Y: "Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: 0 }
            color_strategy: none
            color_note: "Bars use the theme accent cyan #0E7490 as the single hue for whichever metric is selected; the measure-specific hues apply only where a measure is fixed."
            alt_text: "Bar chart of the selected metric across the selected breakdown; values differ by well under one percent."
          - id: metric_by_month
            region: trend
            kind: lineChart
            purpose: "Does the chosen metric drift over the year?"
            title: "Selected metric by month"
            field_bindings: { Category: "Date[Date] at month grain", Y: "Metric (field parameter)" }
            value_axis: { start: 0 }
            color_strategy: none
            alt_text: "Line of the selected metric by month across 2021, close to flat."
          - id: channel_by_type
            region: matrix
            kind: pivotTable
            purpose: "Is there any hot spot where a channel and campaign type combine?"
            title: "Channel by campaign type, selected metric"
            field_bindings: { Rows: "Channel[Channel]", Columns: "Campaign Type[Campaign Type]", Values: "Metric (field parameter)" }
            conditional_formatting: "none, deliberately: a colour gradient would stretch differences of about 0.5% into a false heat map"
            alt_text: "Cross-tab of the selected metric for 6 channels by 5 campaign types; all cells are close in value."
          - id: campaign_detail
            region: detail
            kind: tableEx
            purpose: "Which individual campaigns sit behind the current selection?"
            title: "Campaign detail"
            field_bindings:
              - "Campaigns[Campaign ID]"
              - "Date[Date]"
              - "Company[Company]"
              - "Campaign Type[Campaign Type]"
              - "Channel[Channel]"
              - "Target Audience[Target Audience]"
              - "Customer Segment[Customer Segment]"
              - "Location[City]"
              - "Campaigns[Acquisition Cost]"
              - "Campaigns[Clicks]"
              - "Campaigns[Impressions]"
              - "Campaigns[Conversion Rate]"
              - "Campaigns[ROI]"
              - "Campaigns[Engagement Score]"
            sort_policy: category_asc
            alt_text: "Campaign-level table, one row per campaign, with the fields behind every measure."
        space_audit:
          content_cell_count: 110
          placed_cell_count: 110
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: hero, pct_of_content: 36 }
          balance_rationale: "Content excludes the header band and the 22-cell rail. The hero bar and the detail table each take 36%; the trend and cross-tab are 14% each, above the 3 x 3 floor. Rail is 6 items (5 dropdowns at about 96px plus Reset) filling about 55% of its height."
    - name: "No segment strays more than 1% from the portfolio average"
      role: detail
      archetype: Comparative
      layout_variant: A
      variant_rationale: "Eight peer dimensions of 4 to 6 members each are compared against a single baseline (portfolio average). Side-by-side small panels on shared axes fit; a headline chart or callout is dropped because there is no derived insight beyond what the panels show."
      navigation:
        - element: page_navigator
          action: PageNavigation
          target: "all pages"
          label: "Page navigator"
      page_background: "#F1F5F9"
      layout_summary: "Eight identical variance panels in a 4 x 2 grid on one fixed axis, plus a one-line method note."
      layout_contract:
        canvas: { width: 1920, height: 1080, margin: 32, gutter: 24, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:      [1, 1, 6, 2]
            nav:         [6, 1, 9, 2]
            filters:     [9, 1, 13, 2]
            scan_channel:  [1, 2, 4, 7]
            scan_type:     [4, 2, 7, 7]
            scan_company:  [7, 2, 10, 7]
            scan_duration: [10, 2, 13, 7]
            scan_audience: [1, 7, 4, 12]
            scan_segment:  [4, 7, 7, 12]
            scan_city:     [7, 7, 10, 12]
            scan_language: [10, 7, 13, 12]
            method:        [1, 12, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "No segment strays more than 1% from the portfolio average"
            purpose: "State the finding before any chart. Verified true for the full year on all five metrics (largest gap 0.69%)."
          - id: page_nav
            region: nav
            kind: pageNavigator
            purpose: "Move between the three pages."
            alt_text: "Page navigation: Portfolio summary, Explore, Benchmark scan"
          - id: variance_metric_slicer
            region: filters
            kind: slicer
            field_bindings: "Variance Metric[Variance Metric]"
            slicer_type: dropdown
            default_selection: "ROI vs Portfolio (%)"
          - id: scan_channel_bar
            region: scan_channel
            kind: barChart
            purpose: "Does any channel differ from the portfolio average?"
            title: "Channel"
            field_bindings: { Category: "Channel[Channel]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Channel variance vs portfolio average, all within a fraction of one percent."
          - id: scan_type_bar
            region: scan_type
            kind: barChart
            purpose: "Does any campaign type differ from the portfolio average?"
            title: "Campaign type"
            field_bindings: { Category: "Campaign Type[Campaign Type]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Campaign type variance vs portfolio average, all within a fraction of one percent."
          - id: scan_company_bar
            region: scan_company
            kind: barChart
            purpose: "Does any company differ from the portfolio average?"
            title: "Company"
            field_bindings: { Category: "Company[Company]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Company variance vs portfolio average, all within a fraction of one percent."
          - id: scan_duration_bar
            region: scan_duration
            kind: barChart
            purpose: "Does any campaign length differ from the portfolio average?"
            title: "Duration (days)"
            field_bindings: { Category: "Campaigns[Duration Days] as categorical axis", Y: "Variance Metric (field parameter)" }
            sort_policy: category_asc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Duration variance vs portfolio average for 15, 30, 45 and 60 days, all within a fraction of one percent."
          - id: scan_audience_bar
            region: scan_audience
            kind: barChart
            purpose: "Does any target audience differ from the portfolio average?"
            title: "Target audience"
            field_bindings: { Category: "Target Audience[Target Audience]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Target audience variance vs portfolio average, all within a fraction of one percent."
          - id: scan_segment_bar
            region: scan_segment
            kind: barChart
            purpose: "Does any customer segment differ from the portfolio average?"
            title: "Customer segment"
            field_bindings: { Category: "Customer Segment[Customer Segment]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Customer segment variance vs portfolio average, all within a fraction of one percent."
          - id: scan_city_bar
            region: scan_city
            kind: barChart
            purpose: "Does any city differ from the portfolio average?"
            title: "City"
            field_bindings: { Category: "Location[City]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "City variance vs portfolio average, all within a fraction of one percent."
          - id: scan_language_bar
            region: scan_language
            kind: barChart
            purpose: "Does any language differ from the portfolio average?"
            title: "Language"
            field_bindings: { Category: "Language[Language]", Y: "Variance Metric (field parameter)" }
            sort_policy: value_desc
            value_axis: { start: -0.02, end: 0.02, shared_across_panels: true }
            reference_line: { value: 0, style: dashed, label: "Portfolio average" }
            data_labels: "+0.0%;-0.0%;0.0%"
            color_strategy: none
            comparison_basis: "Portfolio average of all 2021 campaigns"
            alt_text: "Language variance vs portfolio average, all within a fraction of one percent."
          - id: method_note
            region: method
            kind: textbox
            text: "Each bar is a member's value as a % difference from the portfolio average. All eight panels share one fixed axis of -2% to +2%. 2021 data only; no target or prior-year baseline exists."
            purpose: "State the baseline, the axis and the data window so the flat result is read as a finding, not a rendering fault."
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: scan_channel, pct_of_content: 11 }
          balance_rationale: "Eight equal peer panels (15 cells each, above the 3 x 3 floor and readable at 4 x 5) plus a one-row method note that carries real text. Equal size is deliberate: no dimension is more important than another."
  interaction_pattern:
    drill_targets: []
    cross_filter_rules: "Page 1: Filter between charts, except cross-filtering into roi_vs_portfolio_channel is off (its baseline uses ALLSELECTED and would shift). Page 2: Filter (default). Page 3: None between panels; each panel is an independent comparison against the same baseline."
    slicer_sync: "None. The Page 1 Month slicer does not sync to Page 3, so the Page 3 title stays true."
    filter_pane: "Page 2 exposes Company, Language, Duration Days and Month Name."
  accessibility:
    alt_text_strategy: "chart+structure with the notable finding; state the measured spread rather than the chart type"
    contrast_notes: "All text pairs at least 4.5:1 on their surface. Data marks at least 3:1 on white. Cyan, indigo and amber are separated by luminance as well as hue, and every bar carries a data label, so nothing depends on colour alone."
    tab_order: "Title, navigator, slicers, then visuals in reading order (left to right, top to bottom) on every page."
  theme:
    base: "references/design/assets/base.json adapted; keep the base textbox, cardVisual, table and hidden-header safeguards. The report currently uses the Fluent2-CY26SU08 base theme; register the adapted theme as a custom theme."
    user_overrides: "None. No existing brand or theme to preserve."
    name: "Campaign Corporate Cool"
    dataColors: ["#0E7490", "#334155", "#4338CA", "#B45309", "#64748B", "#94A3B8"]
    text_ramp: { title: 20, kpi_value: 32, visual_title: 12, body: 10 }
```

## Implementation notes
- Model changes: add the five variance measures and three field-parameter tables through the modeling MCP (`table_operations` `CreateFieldParameter`; load the semantic-model skill's `field-parameters.md` first). Test each measure with a DAX query, then re-save the PBIP.
- PBIR/report authoring: replace the single blank "Page 1" with three pages named as above; register the adapted theme; author through `powerbi-report-author`, not hand-edited JSON.
- Validation: validate PBIR after each page; confirm every bar chart has `valueAxis.start = 0` (or the fixed symmetric range for variance bars), every panel on page 3 has the identical fixed axis, and every visual has alt text.
- Desktop screenshot verification: screenshot all three pages. Check no scrollbars on textboxes, slicers fully visible, no overlap under the header band, and that the variance bars render as near-zero slivers with readable labels (that is the intended finding).
- Publishing boundary: none. Local PBIP only.
- Risks:
  1. Field parameters with mixed formats (%, $, ratio) can show the wrong number format on the page 2 charts; verify visually and set per-visual format if needed.
  2. `Duration Days` is numeric and may render as a continuous axis on page 3; force a categorical axis.
  3. The 200,000-row detail table needs a sort and virtualisation check.
  4. Titles on pages 1 and 3 are static claims, true on the unfiltered 2021 data. Page 1 stays true with Month filters; page 3 has no date filter by design.
  5. If the data is later replaced with real campaign data, pages 1 and 3 titles must be rewritten; the layout and measures carry over unchanged.
