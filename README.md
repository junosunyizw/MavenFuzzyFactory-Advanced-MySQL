# Udemy Project of MavenFuzzyFactory: Advance Sql for Data Analysis and Business Intelligence - MYSQL

## Introduction

<details> 
<summary>
Click to expand Introduction
	
</summary>

**THE SITUATION** 

You’ve just been hired as an eCommerce Database Analyst for Maven Fuzzy Factory, an online retailer which has just launched their first product.

**THE BRIEF**

As a member of the startup team, you will work with the CEO, the Head of Marketing, and the Website Manager to help steer the business. 

You will analyze and optimize marketing channels, measure and test website conversion performance, and use data to understand the impact of new product launches.

**THE OBJECTIVE**

Use SQL to:
- Access and explore the Maven Fuzzy Factory database
- Become the data expert for the company, and the go-to person for mission critical analyses
- Analyze and optimize the business’ marketing channels, website, and product portfolio

</details> 

***


## Table of Contents

<details> 
<summary>
Click to expand Table of Contents
	
</summary>

- Entity Relationship Diagram(ERD)
- Analyzing Traffic Sources
- Analyzing Website Performances
- Mid-Course Project
- Analysis for Channel Portfolio Management
- Analyzing Business Patterns and Seasonality
- Product Analysis
- User Analysis
- Final Project

</details> 

***

## Entity Relationship Diagram(ERD)
<details> 
<summary>
Click to expand ERD
	
</summary>

<img width="838" alt="ERD" src="https://user-images.githubusercontent.com/69760533/232990175-14806d00-ea98-46e9-84f5-f89c1ad9d5dd.png">

***

⬇️`order_item_refunds` - 

<img width="908" alt="order_item_refunds" src="https://user-images.githubusercontent.com/69760533/232990286-fbd0ba03-fd9f-42c3-ac6a-3bf2000ec42e.png">

***

⬇️`order_items` -

<img width="1201" alt="order_items" src="https://user-images.githubusercontent.com/69760533/232990353-59711233-6b95-4248-8b52-7fd107820313.png">

***

⬇️`orders` - 

<img width="1384" alt="orders" src="https://user-images.githubusercontent.com/69760533/232990361-9310e7df-5833-4b4b-891f-465e5ec8cd19.png">

***

⬇️`products` -

<img width="611" alt="products" src="https://user-images.githubusercontent.com/69760533/232990381-9cf0e902-0c4b-4eae-9492-c493f1280caf.png">

***

⬇️`website_pageviews` - 

<img width="673" alt="website_pageviews" src="https://user-images.githubusercontent.com/69760533/232990397-13b6c6de-71c0-4754-a3d4-d7d276e0d050.png">

***

⬇️`website_sessions` - 

<img width="1700" alt="website_sessions" src="https://user-images.githubusercontent.com/69760533/232990415-3f384165-355b-4d1c-8f16-90d129584571.png">

***

</details> 

***

## Analyzing Traffic Sources

### Traffic Source Analysis

- To understanding where your customers are coming from and which channels driving the highest quality traffic.
- Sources including Email, Social Media, Search engine and direct traffic.
- To look at the conversion rate (CVR), Which is the percentage of sessions that convert to sales activity.

### Why CVR analysis is important to business activity
- To understand how qualified that traffic is and how valuable each of those traffic sources is to us.
- To shifting budget towards sources of traffic driving the strongest conversion rates.
- Comparing user behavior patterns across traffic sources to inform creative and messaging strategy.
- Identifying opportunities to elimiate wastes spend or scale high-converting traffic

### Targets
- Analyzing traffic sources

***

### Q1: Finding Top Traffic Sources

<img width="652" alt="Q1" src="https://user-images.githubusercontent.com/69760533/233090487-058b9847-2364-4036-82af-b7f9f635de0d.png">


- **Request:** 
CEO wants to review website sessions coming from. By Breaking down UTM Sources, compaign and referring domain and before 2012-04-12
- **Results:** 
`utm_source`,`utm_campaign`,`http_referer`,`count(DISTINCT website_session_id)` and `created_at < '2012-04-12'`

```SQL
SELECT utm_source,utm_campaign,http_referer,
    count(DISTINCT website_session_id) num_sessions
FROM website_sessions
WHERE created_at < '2012-04-12' -- count sessions only before '2012-04-12'
GROUP BY 1,2,3 
ORDER BY 4 DESC;
```

<img width="716" alt="Q1-RESULTS" src="https://user-images.githubusercontent.com/69760533/233090687-1da1ca4a-9e3f-4431-adb4-e6ff9ec9e29f.png">

- **Next Steps:**
After getting results, CEO wants to drill deeper into gsearch nonbrand campaign traffic to explore potential optimization opportunities

<img width="652" alt="Q1-DRILLDOWN" src="https://user-images.githubusercontent.com/69760533/233090729-e9cf84bf-4587-47bf-9f2e-af187d616301.png">

### Q2: Traffic Conversion Rates



- **Request:** 
drilling down `gsearch` and `nonbrand`and before `'2012-04-12'` to find out CVR for this traffic source. If CVR >=4%, then increase bigs to drive volume, otherwise bids should be reduced.
- **Results:** 
Coversion rate is 2.92%, which is less than 4%, So company is overspending on gsearch nonbrand compaign through '2012-04-12'. the following action is to reduce bids and then monitor the impact of bigs reductions for campaign.

```SQL
SELECT utm_source,utm_campaign,
        count(DISTINCT o.order_id) as num_orders,
        count(DISTINCT w.website_session_id) as num_webs,
        count(DISTINCT o.order_id)/count(DISTINCT w.website_session_id)*100 as CVR -- CVR is 2.96% < 4% and reduce bids.
FROM website_sessions w
LEFT JOIN orders o
ON o.website_session_id=w.website_session_id
WHERE w.utm_source = 'gsearch' and w.utm_campaign = 'nonbrand' and w.created_at < '2012-04-12' -- Don`t forget date
GROUP BY 1,2
```

