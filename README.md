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

***

### Q2: Traffic Conversion Rates

<img width="652" alt="Q2" src="https://user-images.githubusercontent.com/69760533/233285906-ed2cd46d-eb35-4e2d-9888-c304c4f72b12.png">


- **Request:** 
drilling down `gsearch` and `nonbrand`and before `'2012-04-14'` to find out CVR for this traffic source. If CVR >=4%, then increase bigs to drive volume, otherwise bids should be reduced.
- **hints:**
*CVR = number of orders/number of sessions*
- **Results:** 
Coversion rate is 2.87%, which is less than 4%, So company is overspending on gsearch nonbrand compaign through '2012-04-14'.

```SQL
SELECT utm_source,utm_campaign,
        count(DISTINCT o.order_id) as num_orders,
        count(DISTINCT w.website_session_id) as num_webs,
        count(DISTINCT o.order_id)/count(DISTINCT w.website_session_id)*100 as CVR -- CVR is 2.87% < 4% and reduce bids.
FROM website_sessions w
LEFT JOIN orders o
ON o.website_session_id=w.website_session_id
WHERE w.utm_source = 'gsearch' and w.utm_campaign = 'nonbrand' and w.created_at < '2012-04-14' -- Don`t forget date
GROUP BY 1,2;
```

<img width="843" alt="Q2-RESULTS" src="https://user-images.githubusercontent.com/69760533/233286106-10ae8d20-b577-4960-b37f-baf16bff6ff4.png">

- **Next Steps:**
the following action is to reduce bids and then monitor the impact of bigs reductions for campaign.

<img width="654" alt="Q2-NEXT" src="https://user-images.githubusercontent.com/69760533/233330351-1cbd6e90-1cd4-45f7-b77c-ee7a95979445.png">

***
### **Bid Optimization & Trend Analysis**

Analyzing for bid optimization is about understanding the value of various segments of paid traffic, so that you can optimize your marketing budget.

- **Target:** To figure out the right amount of bid for different segments of traffic based on potential revenue.

- **How?:** 
    - Using conversion rate and revenue per click
analyses to figure out how much you should
spend per click to acquire customers
    - Understanding how your website and products
perform for various subsegments of traffic to optimize within channels
    - Analyzing the impact that bid changes have on
your ranking in the auctions, and the volume of
customers driven to your site

***

### **Q3: Traffic Source Trending**

<img width="633" alt="Q3" src="https://user-images.githubusercontent.com/69760533/233364043-b98cc55a-440a-4c03-bea9-fe72d1935232.png">


- **Request:** After reducing bids on 2012-04-15, to find out the the `trend session volume` and impact on session for `gsearch`,`nonbrand`campaign `by weeks` and `before 2012-05-10`. *As order volume and CVR is highly related to sessions, both of figures will appear on the results.*

- **Results:** the number of sessions and orders and even CVR has dropped after reducing bids so the campaign highly regard to bid changes.

```SQL
select min(DATE(w.created_at)) week_start_date,
        WEEK(w.created_at) num_weeksofyear,
        count(DISTINCT w.website_session_id) num_sessions,
        count(DISTINCT o.order_id) num_orders,
        count(DISTINCT o.order_id)/count(DISTINCT w.website_session_id)*100 CVR
from website_sessions w
LEFT JOIN orders o
on o.website_session_id=w.website_session_id
WHERE utm_source = 'gsearch' and utm_campaign ='nonbrand' and w.created_at < '2012-05-10' -- looking at `trend` volume means to also show data before bids down for comparing
GROUP BY 2;
```
<img width="874" alt="Q3-RESULTS" src="https://user-images.githubusercontent.com/69760533/233364098-6d5c999e-3a4a-4949-9011-ff5e818618f4.png">

- **Next Steps:**
Continuing to monitor volume levels and think about how we could make the campaigns more efficient so that we can increase volume again.

<img width="652" alt="Q3-NEXT" src="https://user-images.githubusercontent.com/69760533/233378790-1cdb4f9d-08aa-458e-b8a1-79fbf99cc36d.png">

***

### **Q4: Traffic Source Bid Optimization**

<img width="652" alt="Q4" src="https://user-images.githubusercontent.com/69760533/233405294-3b473953-c148-49cc-9c8d-be36343f3b01.png">

- **Request:** what is the CVR from session to order by device type? `Desktop`, `Mobile` and date `< 2012-05-11`

- **Results:** Desktop bids were driving 3.73% which was almost reaching target CVR 4% of sessions. However, CVR for Mobile is very low, which is only 0.97%. So we should reduce mobile and transfer the paid traffic to desktop channel instead.

```SQL
select w.device_type,
        count(DISTINCT w.website_session_id) num_sessions,
        count(DISTINCT o.order_id) num_orders,
        count(DISTINCT o.order_id)/count(DISTINCT w.website_session_id)*100 CVR
from website_sessions w
LEFT JOIN orders o
on o.website_session_id=w.website_session_id
WHERE utm_source = 'gsearch' and utm_campaign ='nonbrand' and w.created_at < '2012-05-11'
GROUP BY 1;
```

<img width="652" alt="Q4-RESULTS" src="https://user-images.githubusercontent.com/69760533/233405398-822e64db-b6f3-46e5-bfd5-3e0baa59d176.png">

- **Next Steps:**
Looking for ways to optimise campaign.

<img width="652" alt="Q4-NEXT" src="https://user-images.githubusercontent.com/69760533/233479400-dcdb2770-a8a1-45f5-b9d4-1910303dfe6b.png">

***

### **Q5: Traffic Source Segment Trending**

<img width="652" alt="Q5" src="https://user-images.githubusercontent.com/69760533/233479802-6b27da71-5b2a-42c7-893c-12c29f89f0f9.png">

- **Request:** after bid up on desktop on `2012-05-19`, what is weekly trends for all devices between `2012-04-15` and `2012-06-09`

- **Results:** Desktop volume has significantly increased whereas mobile volume has slightly dropped after bidding up desktop channel on 2012-05-19. Marketing director made the correct desision to foucs on desktop, which is able to optimize spend efficiently.

```SQL
select min(date(w.created_at)) start_week,
        -- week(w.created_at)
        count(DISTINCT CASE WHEN  w.device_type = 'desktop' THEN w.website_session_id ELSE NULL END) desktop_sessions,
        count(DISTINCT case WHEN w.device_type = 'mobile' then w.website_session_id ELSE NULL end) mobile_sessions
from website_sessions w
LEFT JOIN orders o
on o.website_session_id=w.website_session_id
WHERE utm_source = 'gsearch' and utm_campaign ='nonbrand' and w.created_at between '2012-04-15' and '2012-06-09'
GROUP BY week(w.created_at)
```

<img width="540" alt="Q5-RESULTS" src="https://user-images.githubusercontent.com/69760533/233500326-2ae634cf-7758-4fc7-b44f-bad655acbac7.png">

<img width="642" alt="q5-next" src="https://user-images.githubusercontent.com/69760533/233505035-394f6647-0456-4ff7-ae5d-d2cb9fa87a16.png">


***


## **Analyzing Website Performances**

Website content analysis is about understanding which pages are seen the most by your users, to identify where to focus on improving your business.

- **Target:**
    - Finding the most-viewed pages that customers view on your site
    - Identifying the most common entry pages to your websites (home page)
    - Understandinging how those pages perform for the business activities.

- **How?**
    - Analyzing top landing page
    - Analyzing bounce rates and landing pages test
    - building conversion funnels and testing conversion paths.

***

## **Q6: Identifying Top Website Pages**

<img width="642" alt="Q6" src="https://user-images.githubusercontent.com/69760533/233518954-5547b955-61de-44d7-a899-fe2a12399787.png">

- **Request:** Identify most viewd website pages ranked by session volume

- **Results:** `/home` page got most views

```SQL
SELECT pageview_url,
        count(DISTINCT wv.website_session_id) page_view
FROM website_pageviews wv
LEFT JOIN website_sessions ws
ON wv.website_session_id=ws.website_session_id
WHERE DATE(ws.created_at) < '2012-06-09'
GROUP BY 1
ORDER BY 2 DESC;
```
<img width="321" alt="Q6-RESULTS" src="https://user-images.githubusercontent.com/69760533/233518983-0b3ba1c8-3daf-48ca-8020-cbeb8128e8ef.png">

- **Next Steps:** finding out top entry pages and analyzing the performance of each of top pages to look for improvement opportunities.

<img width="647" alt="Q6-NEXT" src="https://user-images.githubusercontent.com/69760533/233519043-375e6657-10c9-4522-ba20-597207c7e162.png">

***

## **Q7: Identifying Top Entry Pages**

![Alt text](Assets/Q7-Identifying%20Top%20Entry%20Pages/Q7.png)

- **Request:** finding out top entry pages

- **Results:** home page is top entry page.

```SQL

-- Can use TEMPORARY TEABLE, SUBQUERY Instead
WITH T1 AS
(
SELECT 
        website_session_id WSID,
        MIN(website_pageview_id) MIN_WPID
FROM website_pageviews
WHERE date(created_at) <'2012-06-12'
GROUP BY 1
)

SELECT WP.pageview_url ,
        count(DISTINCT(T1.MIN_WPID))
FROM T1
LEFT JOIN website_pageviews WP
ON T1.MIN_WPID=WP.website_pageview_id
GROUP BY 1
ORDER BY 2 DESC 
```

![Alt text](Assets/Q7-Identifying%20Top%20Entry%20Pages/Q7-RESULTS.png)


![Alt text](Assets/Q7-Identifying%20Top%20Entry%20Pages/Q7-NEXT.png)