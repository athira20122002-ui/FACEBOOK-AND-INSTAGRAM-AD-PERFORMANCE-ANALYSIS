📊 FACEBOOK & INSTAGRAM AD PERFORMANCE ANALYSIS (POWER BI)

An interactive **Power BI dashboard** that analyses paid advertising campaigns on **Facebook and Instagram (Meta)**. It tracks reach, engagement, conversions and budget utilisation, so the marketing team can see which platforms, ad formats, audiences and time slots perform best.

---

## 🧾 Project Overview

This project turns raw Meta ad event data into a decision-ready report. It covers the full analytics workflow:

1. **Requirements:** a Business Requirements Document defining objectives, scope, KPIs and chart needs
2. **Data understanding:** a Domain Knowledge Document describing the tables, fields and relationships
3. **Modelling:** a star schema in Power BI with a Date table
4. **Visualisation:** an interactive dashboard with a dynamic metric selector
5. **Insights:** findings and actionable recommendations for the marketing team

**Highlights**

- 📊 12 KPIs covering reach, engagement, conversion and budget
- 🎛️ Dynamic metric parameter that switches every visual between Impressions, Clicks, Purchases and more
- 👥 Audience analysis by gender, age group and country
- ⏰ Time analysis by hour, week and calendar month
- 🎨 Ad format and platform comparison (Image, Video, Carousel, Story on Facebook and Instagram)

---

## 🎯 Business Objective

The business needs a **performance tracking report** for ad campaigns running on Facebook and Instagram. The report gives visibility into campaign reach, engagement, conversions, and budget utilisation, helping the marketing team to:

- Identify the most effective platform (Facebook vs Instagram)
- Track campaign ROI and optimise budget allocation
- Understand audience engagement patterns

## 🔍 Scope

| In Scope | Out of Scope |
|----------|--------------|
| Paid campaigns on **Facebook** and **Instagram** | Other platforms (Messenger, Audience Network) |
| | Organic engagement (only paid ads are included) |

---

## 🗂️ Dataset & Data Model

The dataset models how Meta ad platforms capture data, across four tables arranged as a **star schema**.

| Table | Role | Description |
|-------|------|-------------|
| `ad_events` | **Fact** | Event-level logs: impression, click, share, comment, purchase (with timestamp, day of week, time of day) |
| `ads` | Dimension | Ad creative details: platform, ad type (Image / Video / Carousel / Story), target gender, target age group, target interests |
| `campaigns` | Dimension | Campaign name, start and end dates, duration, total budget |
| `users` | Dimension | User demographics: gender, age, age group, country, location, interests |

**Relationships**

```
ad_events ──► ads ──► campaigns
    │
    └──────► users
```

A **Date table** is linked to `ad_events` for week, month and calendar analysis.

---

## 📐 KPIs & Definitions

| KPI | Definition | Formula |
|-----|------------|---------|
| Impressions | Times ads were displayed | Count of `event_type = Impression` |
| Clicks | Times users clicked ads | Count of `event_type = Click` |
| Shares | Times ads were shared | Count of `event_type = Share` |
| Comments | User comments on ads | Count of `event_type = Comment` |
| Purchases | Purchases made after seeing ads | Count of `event_type = Purchase` |
| Engagements | Total interactions | Clicks + Shares + Comments |
| **CTR** | % of impressions that resulted in clicks | (Clicks ÷ Impressions) × 100 |
| **Engagement Rate** | % of impressions that resulted in engagements | (Engagements ÷ Impressions) × 100 |
| **Conversion Rate** | % of clicks that resulted in purchases | (Purchases ÷ Clicks) × 100 |
| **Purchase Rate** | % of impressions that resulted in purchases | (Purchases ÷ Impressions) × 100 |
| Total Budget | Total spend allocated to campaigns | Sum of `campaigns.total_budget` |
| Avg. Budget per Campaign | Average budget per campaign | Total Budget ÷ Campaign Count |

---

## 🧮 Sample DAX Measures

Example measures that follow the KPI definitions above. Table and column names follow the dataset documentation, so adjust them if your model differs.

```dax
Impressions = CALCULATE(COUNTROWS(ad_events), ad_events[event_type] = "Impression")
Clicks      = CALCULATE(COUNTROWS(ad_events), ad_events[event_type] = "Click")
Shares      = CALCULATE(COUNTROWS(ad_events), ad_events[event_type] = "Share")
Comments    = CALCULATE(COUNTROWS(ad_events), ad_events[event_type] = "Comment")
Purchases   = CALCULATE(COUNTROWS(ad_events), ad_events[event_type] = "Purchase")

Engagements = [Clicks] + [Shares] + [Comments]

CTR             = DIVIDE([Clicks], [Impressions]) * 100
Engagement Rate = DIVIDE([Engagements], [Impressions]) * 100
Conversion Rate = DIVIDE([Purchases], [Clicks]) * 100
Purchase Rate   = DIVIDE([Purchases], [Impressions]) * 100

Total Budget = SUM(campaigns[total_budget])
Avg Budget per Campaign = DIVIDE([Total Budget], DISTINCTCOUNT(campaigns[campaign_id]))
```

**Dynamic metric selection** (used with a field parameter or a disconnected metric table):

```dax
Selected Metric =
SWITCH(
    SELECTEDVALUE('Metric Selector'[Metric]),
    "Impressions", [Impressions],
    "Clicks",      [Clicks],
    "Shares",      [Shares],
    "Comments",    [Comments],
    "Purchases",   [Purchases],
    [Impressions]
)
```

---

## 📈 Dashboard Visuals

A **dynamic metric parameter** lets the user switch the selected metric (Impressions, Clicks, Purchases, etc.) across the visuals.

| # | Visual | Purpose |
|---|--------|---------|
| 1 | **Donut chart**: Target Gender | Which gender segment contributes most to the selected metric |
| 2 | **Bar chart**: Target Age Group | Which age group is most responsive |
| 3 | **Map**: Country | Geographic view of reach and engagement |
| 4 | **Calendar heat map**: Month | Seasonal trends, peak and low-activity periods |
| 5 | **Stacked column**: Weekly trend by Ad Type | Ad type contribution over weeks |
| 6 | **Area chart**: Hourly trend | User activity patterns across the day |
| 7 | **Matrix**: Ad Type × Platform | Compare ad formats and platforms side by side |

> 📸 **Dashboard preview:** add a screenshot here, e.g. `![Dashboard](images/dashboard.png)`

---

## 💡 Key Insights

**Headline KPIs**

| Impressions | Clicks | Purchases | Engagements | CTR | Engagement Rate | Conversion Rate | Purchase Rate | Total Budget |
|:-----------:|:------:|:---------:|:-----------:|:---:|:---------------:|:---------------:|:-------------:|:------------:|
| 216K | 25.4K | 1.3K | 29K | 11.76% | 13.56% | 5.21% | 0.61% | 2.5M |

- **Strong top of funnel:** CTR (11.76%) and engagement rate (13.56%) show that creatives and targeting attract attention.
- **Weak bottom of funnel:** a purchase rate of only 0.61% shows a sharp drop-off between interest and purchase.
- **Audience:** Female users drive the most engagement (43%), followed by Other/Not specified (35%) and Male (22%). Engagement peaks in the 20–30 age group and drops sharply after 35.
- **Geography:** The US, India, Brazil, Germany and the UK are the major contributors.
- **Timing:** Engagement peaks in the late afternoon and evening (about 15:00–20:00) and is lowest from 00:00–05:00. Weekly engagement is steady.
- **Ad formats:** Video ads lead on CTR, conversion rate and engagement rate, followed by Stories (which also get the most impressions). Image and Carousel ads convert slightly lower.

**Performance by ad type**

| Ad Type | Impressions | Clicks | CTR | Purchase Rate | Conversion Rate | Engagement Rate |
|---------|:-----------:|:------:|:---:|:-------------:|:---------------:|:---------------:|
| Carousel | 48K | 6K | 11.7% | 0.59% | 5.1% | 13.4% |
| Image | 51K | 6K | 11.7% | 0.57% | 4.9% | 13.5% |
| Stories | 72K | 8K | 11.8% | 0.65% | 5.2% | 13.6% |
| Video | 46K | 5K | 11.9% | 0.62% | 5.2% | 13.7% |

---

## ✅ Recommendations

1. **Fix the purchase funnel:** improve landing pages, offers and retargeting campaigns to lift the purchase rate.
2. **Target the core audience:** females aged 18–30 in the highest-engagement countries.
3. **Prioritise formats:** Video > Stories > Carousel / Image.
4. **Schedule smartly:** deliver ads in afternoon and evening slots.
5. **Optimise budget:** shift spend towards the best-performing geographies and ad formats.

---

## 🔮 Future Improvements

- Add cost-based KPIs: **CPM, CPC and ROAS**
- Compare **Facebook vs Instagram** directly with a dedicated platform page
- Add **target vs actual audience** analysis (targeted gender and age vs engaging users)
- Add **campaign pacing** views using start date, end date and duration
- Set up scheduled data refresh and publish to the Power BI Service

---

## 🛠️ Tools & Skills Used

- **Power BI Desktop**: data modelling, DAX measures, interactive visuals
- **Star schema modelling**: fact and dimension tables, relationships, Date table
- **DAX**: KPI measures, dynamic metric selection (parameter)
- **Business analysis**: BRD, domain knowledge documentation, insight generation

---

## 📁 Repository Structure

```
├── FACEBOOK___INSTAGRAM_ANALYSIS.pbix   # Power BI dashboard file
├── Business_Requirements_Document.pdf   # Objectives, scope, KPIs, chart requirements
├── Domain_Knowledge_Document.pdf        # Dataset, tables, fields, relationships
├── Dashboard_Insights.pdf               # Findings and recommendations
├── images/                              # Dashboard screenshots (optional)
└── README.md
```

---

## ▶️ How to Open the Dashboard

1. Clone or download this repository:
   ```bash
   git clone https://github.com/athira20122002/meta-ad-performance-analysis.git
   ```
2. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows).
3. Open `FACEBOOK___INSTAGRAM_ANALYSIS.pbix`.
4. Use the metric parameter and slicers to explore the report.
   ------
👩‍💻 Author
Athira M
   ------------


---

