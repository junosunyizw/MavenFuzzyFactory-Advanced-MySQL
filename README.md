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


- **Next Steps:** Analyze landing page performance, for the homepage sepcifically. and think about whether or not the homepage is the best intitial experience for all customers

![Alt text](Assets/Q7-Identifying%20Top%20Entry%20Pages/Q7-NEXT.png)

***

## **Landing Page Performance & Testing**

Landing page analysis and testing is about understanding the performance of your key landing pages and then testing to improve your results

- **Target:** 
    - Identifying your top opportunities for landing pages high volume pages with higher than expected bounce rates or low conversion rates
    - bounce means no more additional page view after viewing first page vice versa

- **How?:** 
    - analyze landing page performance and compare multiple pages.
    - finding the first pageview for relevant sessions, asocitate that pageview with the url seen, then analyze whether that session had additional pageviews

***

## **Q8: Calculating Bounce Rates**

![Alt text](Assets/Q8-Calculating%20Bounce%20Rates/Q8.png)

 - **Request:** Finding sessions for landing pages before `2012-06-01` setting pageview_url as `/home`and then identify the bounce sessions.Finally, total sessions, number of bounces sessions for landing page and bounce rate should be calculated.


- **Results:** Bounce rate is 60%, which is very high for the paid search.

```SQL
WITH first_pageview AS -- 1.find sessions for landing page
(
SELECT wp.website_session_id,
        pageview_url,
        min(wp.website_pageview_id) min_pageview_id
FROM website_pageviews wp
JOIN website_sessions ws
ON wp.website_session_id=ws.website_session_id
WHERE wp.created_at <= '2012-06-14' and pageview_url = '/home' -- conditions
GROUP BY 1,2
),
bounce_view as -- 2.identify the bounce sessions
(
SELECT fp.pageview_url,
        fp.website_session_id,
        count(wp.website_session_id) bounce_views

FROM first_pageview fp
LEFT JOIN website_pageviews wp
ON fp.website_session_id=wp.website_session_id
GROUP BY 1,2
HAVING count(wp.website_session_id) = 1
)
select count(fp.website_session_id) total_sessions, -- 3.calculate bounce sessions and bounce rate
        count(bv.website_session_id) bounce_session,
        count(bv.website_session_id)/count(fp.website_session_id) bounce_rates
from first_pageview fp
LEFT JOIN bounce_view bv
on fp.website_session_id=bv.website_session_id;
```
![Alt text](Assets/Q8-Calculating%20Bounce%20Rates/Q8-RESULTS.png)

- **Next Steps:** WM thinks a new page will improve performance. So keeping eyes on bounce rates and setting up A/B test against the homepage. 

![Alt text](Assets/Q8-Calculating%20Bounce%20Rates/Q8_NEXT.png)

***

## **Q9: Analyzing Landing Page Tests**

![Alt text](Assets/Q9-Analyzing%20Landing%20Page%20Tests/Q9.png)

- **Request:** Running A/B teset for `/home` and `/lander-1` for gsearch compaign and finding out their bounce rate. Remember to limit the timeframe to ensure fair comparsion. So the first thing is to find out when is the first date that `/lander-` was created. and then find landing sessions from `2012-06-19` to `2012-07-28`. besides, remeber to limit utm_source as `gsearch` and utm_campagin as `nonbrand` as requested.


- **Results:** the new /lander-1 `traffic has improved and bounce rate has reduced as well. So fewer customers has bounced on the landing page.

```SQL
-- Step 1: Find when /lander-1 was created and displayed. BY getting minmium of `create_at`and `pageview_id` 

SELECT MIN(created_at),
        min(website_pageview_id),
        pageview_url
FROM website_pageviews
where pageview_url = '/lander-1';-- result is `2012-06-19`


-- Step 2: getting results from last query. limit the landing pages from `2012-06-19` to `2012-07-28` and utm_source as `gsearch` and utm_coampaign as `nonbrand` according to WM requested
WITH cte_landingpage_views AS
(
SELECT min(wp.website_pageview_id) min_landingpage, 
        wp.website_session_id,
        wp.pageview_url -- landing pages
FROM website_pageviews wp
JOIN website_sessions ws
ON wp.website_session_id=ws.website_session_id
WHERE wp.pageview_url IN ('/home','/lander-1') 
        and wp.created_at BETWEEN '2012-06-19' AND '2012-07-28'
        and ws.utm_source = 'gsearch'
        and ws.utm_campaign = 'nonbrand' -- setting up fair timeframe and objectives for A/B TEST
GROUP BY 2,3
),
-- Step 3: identity bounce views
cte_bouncepage_view AS
(
SELECT wp.website_session_id,
        count(wp.website_pageview_id) bounce_views
FROM cte_landingpage_views clv
LEFT JOIN website_pageviews wp 
ON clv.website_session_id=wp.website_session_id -- join session id with first landing pages
GROUP BY 1
HAVING count(wp.website_pageview_id) = 1 -- limit view times, 1 means bounce session.
)

-- Step 4: Calculate number of sessions and bounce sessions by landing pages and bounce rates for comparsion

SELECT pageview_url landing_pages,
        count(DISTINCT clv.website_session_id) total_sessions,-- number of session by landing pages
        count(DISTINCT cbv.website_session_id) bounce_sessions,-- number of bounce sessions by landing page
        count(DISTINCT cbv.website_session_id)/count(DISTINCT clv.website_session_id) bounce_rates --  bounce rate
from cte_landingpage_views clv
LEFT JOIN cte_bouncepage_view cbv
ON clv.website_session_id=cbv.website_session_id -- use left join to preserve all sessions with 1 page view
GROUP BY 1;
```
![Alt text](Assets/Q9-Analyzing%20Landing%20Page%20Tests/Q9-RESULTS.png)

- **Next Steps:** ensure new compaign is directed to land-1 page and then monitoring bounce rate.

![Alt text](Assets/Q9-Analyzing%20Landing%20Page%20Tests/Q9-NEXT.png)

***

## **Q10: Landing Page Trend Analysis**

![Alt text](Assets/Q10-Landing%20Page%20Trend%20Analysis/Q10.png)

- **Request:** Pulling data for paid `gsearch`,`nonbrand`campagin traffic on date between `2012-06-01` and `2012-08-31` and Summary total&bounce sessions, bounce rate by weekly.

- **Results:** bounce rate dropped from around 60% to around 50% after rounted landing page from /home to /lander-1. so there is improvement and /lander-1 is working better.


```SQL

-- 1.identify the landing page sessions
WITH cte_landingpage_views AS
(
SELECT ws.website_session_id,
        min(wp.website_pageview_id) landingpage_view_id,
        count(ws.website_session_id) landingpage_sessions

FROM website_sessions ws
JOIN website_pageviews wp
ON ws.website_session_id=wp.website_session_id
WHERE ws.utm_source = 'gsearch'
        AND ws.utm_campaign = 'nonbrand'
        AND ws.created_at BETWEEN '2012-06-01' AND '2012-08-31'
GROUP BY 1
),
-- 2.summary required field
cte_summary AS
(
SELECT wp.pageview_url,
        wp.created_at,
        clv.*
FROM cte_landingpage_views clv
JOIN website_pageviews wp
ON clv.landingpage_view_id=wp.website_pageview_id
)
-- 3. Summary by week (bounce rate, bounce sessions, total sessions, home sessions, lander1 sessions)

SELECT YEARWEEK(created_at) Week_on_year,
        MIN(DATE(created_at)) Week_start,
        COUNT(DISTINCT website_session_id) total_sessions,
        COUNT(DISTINCT CASE WHEN landingpage_sessions = 1 then website_session_id END) bounce_sessions,
        ROUND(count(DISTINCT CASE WHEN landingpage_sessions = 1 then website_session_id END)/
        COUNT(DISTINCT website_session_id),2) bounce_rates,
        COUNT(DISTINCT CASE WHEN pageview_url = '/home'then website_session_id else null end) home_sessions,
        COUNT(DISTINCT CASE WHEN pageview_url = '/lander-1'then website_session_id else null end) lander1_sessions
FROM cte_summary
GROUP BY 1;
```

![Alt text](Assets/Q10-Landing%20Page%20Trend%20Analysis/Q10-RESULTS.png)

- **Next Steps:** looking into the conversion rate from pages. conversion funnels will be anaylzed and tested.

![Alt text](Assets/Q10-Landing%20Page%20Trend%20Analysis/Q10-NEXT.png)

***

## Analyzing and Testing Conversion Funnels

Conversion funnel analysis is about understanding and optimizing each step of your user’s experience on their journey toward purchasing your products.

- **Target:** Homepage -> Product page -> add to cart page -> Sales

- **How?:**
        - Identifying the most common paths customers take before purchasing your products.
        - Identifying how many of your users continue on to each next step in your conversion flow,and how many users abandon at each step.
        - Optimize critical pain points where users are abandoning so that you can convert more users and sell more products.

***

## **Q11: Building Conversion Funnel**

![Alt text](Assets/Q11-Building%20Conversion%20Funnel/Q11.png)

- **Request:** buiding conversion funnel all the way down from `/products`,`/the-original-mr-fuzzy`,`/cart`,`/shipping`,`/billing` to `/thank-you-for-your-order` under paid `gsearch` and `nonbrand` compagin traffic limit the time between `2012-08-05` to `2012-09-05`

- **Results:** identify the pain points of landingpage to prodcutpage, mrfuzzypage to cartpage and billingpage to thankyoupage. all clickthrough rate are less than 50% which much less than other pages clickthrough. Most of customers dropped off from billing page.


```SQL

WITH cte_pages_level AS
(
SELECT ws.website_session_id,
        wp.pageview_url,
        wp.created_at,
        CASE WHEN wp.pageview_url = '/products' THEN 1 ELSE 0 END AS products_page,
        CASE WHEN wp.pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE 0 END AS mrfuzzy_page,
        CASE WHEN wp.pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
        CASE WHEN wp.pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
        CASE WHEN wp.pageview_url = '/billing' THEN 1 ELSE 0 END AS billing_page,
        CASE WHEN wp.pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thankyou_page
FROM website_sessions ws
JOIN website_pageviews wp
ON ws.website_session_id=wp.website_session_id
WHERE ws.utm_source = 'gsearch'
        AND ws.utm_campaign = 'nonbrand'
        AND ws.created_at BETWEEN '2012-08-05' AND '2012-09-05'
),
cte_pages_flag AS
(
SELECT website_session_id,
        MAX(products_page) productspage_flag,
        MAX(mrfuzzy_page) mrfuzzypage_flag,
        MAX(cart_page) cartpage_flag,
        MAX(shipping_page) shippingpage_flag,
        MAX(billing_page) billingpage_flag,
        MAX(thankyou_page) thankyoupage_flag
FROM cte_pages_level
GROUP BY 1
)
-- to page
-- SELECT count(DISTINCT website_session_id) total_sessions,
        -- count(DISTINCT CASE WHEN productspage_flag = 1 then website_session_id ELSE NULL END) to_productspage,
        -- count(DISTINCT CASE WHEN mrfuzzypage_flag = 1 then website_session_id ELSE NULL END) to_mrfuzzypage,
        -- count(DISTINCT CASE WHEN cartpage_flag = 1 then website_session_id ELSE NULL END) to_carpage,
        -- count(DISTINCT CASE WHEN shippingpage_flag = 1 then website_session_id ELSE NULL END) to_shippingpage,
        -- count(DISTINCT CASE WHEN billingpage_flag = 1 then website_session_id ELSE NULL END) to_billingpage,
        -- count(DISTINCT CASE WHEN thankyoupage_flag = 1 then website_session_id ELSE NULL END) to_thankyoupage
-- FROM cte_pages_flag;
-- clickthrough page rate (conversion rate%)
SELECT count(DISTINCT website_session_id) total_sessions,
        count(DISTINCT CASE WHEN productspage_flag = 1 then website_session_id ELSE NULL END)/count(DISTINCT website_session_id) landingpage_to_product_rate,
        count(DISTINCT CASE WHEN mrfuzzypage_flag = 1 then website_session_id ELSE NULL END)/count(DISTINCT CASE WHEN productspage_flag = 1 then website_session_id ELSE NULL END) productpage_to_mrfuzzypage_rate,
        count(DISTINCT CASE WHEN cartpage_flag = 1 then website_session_id ELSE NULL END)/count(DISTINCT CASE WHEN mrfuzzypage_flag = 1 then website_session_id ELSE NULL END) mrfuzzypage_to_cartpage_rate,
        count(DISTINCT CASE WHEN shippingpage_flag = 1 then website_session_id ELSE NULL END)/count(DISTINCT CASE WHEN cartpage_flag = 1 then website_session_id ELSE NULL END) carpage_to_shippingpage_rate,
        count(DISTINCT CASE WHEN billingpage_flag = 1 then website_session_id ELSE NULL END)/count(DISTINCT CASE WHEN shippingpage_flag = 1 then website_session_id ELSE NULL END) shippingpage_to_billingpage_rate,
        count(DISTINCT CASE WHEN thankyoupage_flag = 1 then website_session_id ELSE NULL END)/count(DISTINCT CASE WHEN billingpage_flag = 1 then website_session_id ELSE NULL END) billingpage_to_thankyoupage_rate
FROM cte_pages_flag;
```

![Alt text](Assets/Q11-Building%20Conversion%20Funnel/Q11-RESULTS.png)

- **Next Steps:** WM need to find out why user dropped off from these pages. and how to improve the billing page to make customers more comfortable to process the payment through billing page.

![Alt text](Assets/Q11-Building%20Conversion%20Funnel/Q11-NEXT.png)

***


## **Q12: Conversion Funnels Testing for `/billing` and `/billing-2`(A/B Testing)**

![Alt text](Assets/Q12-Conversion%20Funnels%20Testing%20/Q12.png)

- **Request:** WM develped billing page as `/billing-2` and will test the conversion rate for billing to order pages through all traffic.

- **Results:**

```SQL
-- 1. identify the time of billing-2 being online. in order to create fair comparison for this test.
SELECT min(website_pageview_id),-- min pageview_id is 53550
        pageview_url
FROM website_pageviews
WHERE pageview_url = '/billing-2';

-- 2.summary of relvant page sessions and orders

SELECT pageview_url,
        count(DISTINCT pageview_sessions.website_session_id) total_sessions,
        count(o.order_id) total_orders,
        count(o.order_id)/count(DISTINCT pageview_sessions.website_session_id) conversion_rate

FROM
        (SELECT ws.website_session_id,
                pageview_url
        FROM website_sessions ws
        LEFT JOIN website_pageviews wp
        ON ws.website_session_id=wp.website_session_id
        WHERE wp.website_pageview_id >= 53550
                AND ws.created_at < '2012-11-10'
                AND pageview_url IN ('/billing','/billing-2')) AS pageview_sessions
LEFT JOIN orders o
ON pageview_sessions.website_session_id=o.website_session_id
GROUP BY 1;

```

![Alt text](Assets/Q12-Conversion%20Funnels%20Testing%20/Q12-RESULTS.png)

- **Next Steps:** 

![Alt text](Assets/Q12-Conversion%20Funnels%20Testing%20/Q12-NEXT.png)

***

## Mid Course Project

***

### The Situation

Maven Fuzzy Factory has been live for ~8 months, and your CEO is due to present company performance metrics to the board next week. You’ll be the one tasked with preparing relevant metrics to show the company’s promising growth.

### The Objective

- Extract and analyze website traffic and performance data from the Maven Fuzzy Factory database to quantify the company’s growth, and to tell the story of how you have been able to generate that growth.

- As an Analyst, the first part of your job is extracting and analyzing the data, and the next part of your job is effectively communicating the story to your stakeholders.

### **Project Questions**

### *Q1:Gsearch seems to be the biggest driver of our business. Could you pull monthly trends for gsearch sessions and orders so that we can showcase the growth there?*

- **Request:** `Monthly trends` for `gserach` `sessions`, `orders` and `sessions to orders rate` before `2012-11-27`


- **Results:**

```SQL
SELECT 
        EXTRACT(YEAR_MONTH FROM WS.created_at) yrmonth,
        COUNT(DISTINCT ws.website_session_id) sessions,
        count(DISTINCT o.order_id) orders,
        ROUND(count(DISTINCT o.order_id)/COUNT(DISTINCT ws.website_session_id)*100,2) CVR
        
FROM website_sessions ws
LEFT JOIN orders o
ON ws.website_session_id=o.website_session_id

WHERE utm_source = 'gsearch' AND ws.created_at < '2012-11-27'
GROUP BY 1
```

***

### *Q2:Next, it would be great to see a similar monthly trend for Gsearch, but this time splitting out nonbrand and brand campaigns separately. I am wondering if brand is picking up at all. If so, this is a good story to tell.*

- **Request:** Comparing `brand` and `non-brand` of Monthly trends for gserach sessions, orders and sessions to orders rate before 2012-11-27

- **Results:**

```sql
-- My solutions by using CTE and then combine required column in a table. Althougt the answer looks longer than pivoting method, however it looks more clear to get the right answers.
WITH brand as(

        SELECT 
                EXTRACT(YEAR_MONTH from ws.created_at) yrmonth,
                COUNT(DISTINCT ws.website_session_id) sessions,
                count(DISTINCT o.order_id) orders,
                ROUND(count(DISTINCT o.order_id)/COUNT(DISTINCT ws.website_session_id)*100,2) CVR
        FROM website_sessions ws
        LEFT JOIN orders o
        on ws.website_session_id=o.website_session_id
        WHERE utm_source = 'gsearch' and utm_campaign = 'brand' and ws.created_at < '2012-11-27'
        GROUP BY 1
),
nonbrand as(

                SELECT 
                EXTRACT(YEAR_MONTH from ws.created_at) yrmonth,
                COUNT(DISTINCT ws.website_session_id) sessions,
                count(DISTINCT o.order_id) orders,
                ROUND(count(DISTINCT o.order_id)/COUNT(DISTINCT ws.website_session_id)*100,2) CVR
        FROM website_sessions ws
        LEFT JOIN orders o
        on ws.website_session_id=o.website_session_id
        WHERE utm_source = 'gsearch' and utm_campaign = 'nonbrand' and ws.created_at < '2012-11-27'
        GROUP BY 1
)
select b.yrmonth yrmonth,b.sessions,b.orders,b.cvr,nb.sessions,nb.orders,nb.cvr
from brand b
join nonbrand nb
on b.yrmonth=nb.yrmonth;

-- Pivoting Method
SELECT 
  EXTRACT(YEAR_MONTH FROM s.created_at) AS yrmonth,
  COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN s.website_session_id ELSE NULL END) AS nbsessions,
  COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END) AS nborders,
  ROUND(COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END)/
    COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN s.website_session_id ELSE NULL END)*100,2) AS nbcvr,
  COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN s.website_session_id ELSE NULL END) AS bsessions,
  COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN o.order_id ELSE NULL END) AS borders,
  ROUND(COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN o.order_id ELSE NULL END)/
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN s.website_session_id ELSE NULL END)*100,2) AS bcvr
FROM website_sessions s
LEFT JOIN orders o
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27' AND s.utm_source = 'gsearch' AND s.utm_campaign IN ('nonbrand', 'brand')
GROUP BY 1;

```


***
### *Q3:While we’re on Gsearch, could you dive into nonbrand, and pull monthly sessions and orders split by device type? I want to flex our analytical muscles a little and show the board we really know our traffic sources.*
***

### *Q4:I’m worried that one of our more pessimistic board members may be concerned about the large % of traffic from Gsearch. Can you pull monthly trends for Gsearch, alongside monthly trends for each of our other channels?*
***

### *Q5: I’d like to tell the story of our website performance improvements over the course of the first 8 months. Could you pull session to order conversion rates, by month?*
***

### *Q6: For the gsearch lander test, please estimate the revenue that test earned us (Hint: Look at the increase in CVR from the test (Jun 19 – Jul 28), and use nonbrand sessions and revenue since then to calculate incremental value)*
***

### *Q7:For the landing page test you analyzed previously, it would be great to show a full conversion funnel from each of the two pages to orders. You can use the same time period you analyzed last time (Jun 19 – Jul 28).*
***

### *Q8:I’d love for you to quantify the impact of our billing test, as well. Please analyze the lift generated from the test (Sep 10 – Nov 10), in terms of revenue per billing page session, and then pull the number of billing page sessions for the past month to understand monthly impact.*
***
