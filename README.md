#  🛒 E-Commerce Customer Journey Map 

## 📖 Project Overview
This project was developed to analyze user behavior on an e-commerce website and understand conversion rates. The project utilizes BigQuery for querying Google Analytics 4 (GA4) data and Tableau for visualization.

## 🎯 Objectives
- 📊 Understand user behavior (sessions, product views, add to cart, etc.).
- 🔄 Analyze the steps of the conversion funnel.
- 🌐 Compare user traffic sources and devices.
- 💡 Optimize purchase conversion rates.

---

## 🛠 Tools and Technologies Used
- ** 🔍 BigQuery**: Data querying and processing.
- ** 📈 Tableau**: Data visualization.
- ** 💻 SQL**: Querying and analyzing data.

---

## 📋 Data Query
The SQL query used in this project is as follows:

<details>
<summary>👉 Click to view the query</summary>
  
```sql
WITH query AS (
  SELECT
    FORMAT_TIMESTAMP('%Y/%m/%d %H:%M:%S.', TIMESTAMP_MICROS(event_timestamp)) || 
    LPAD(CAST(EXTRACT(MILLISECOND FROM TIMESTAMP_MICROS(event_timestamp)) AS STRING), 4, '0') AS event_date,
    event_name,
    traffic_source.source AS ssource,
    traffic_source.medium AS medium,
    traffic_source.name AS campaign,
    device.language AS device_language,
    device.category AS device_category,
    device.operating_system AS device_os,
    geo.country as country,
    (
      SELECT
        value.int_value
      FROM
        UNNEST(event_params)
      WHERE
        key = 'ga_session_id'
    ) AS user_session_id,
    (
      SELECT
        value.string_value
      FROM 
        UNNEST(event_params)
      WHERE
        key = 'page_location'
    ) AS Session_landing_page
  FROM
    `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
  WHERE
    event_name IN ('session_start',
    'view_item',
    'add_to_cart',
    'begin_checkout',
    'add_shipping_info',
    'add_payment_info',
    'purchase')
),
query2 AS (
  SELECT
    event_date,
    user_session_id,
    ssource,
    medium,
    campaign,
    event_name,
    country,
    regexp_extract((Session_landing_page), r'(?:https:\/\/)?[^\/]+\/(.*)') as landing_page_location,
    device_language,
    device_category,
    device_os, 
    COUNT(DISTINCT(CASE WHEN event_name LIKE '%add_to_cart%' THEN 1 END)) AS add_to_cart_count,
    COUNT(DISTINCT(CASE WHEN event_name LIKE '%begin_checkout%' THEN 1 END)) AS begin_checkout_count,
    COUNT(DISTINCT(CASE WHEN event_name LIKE '%purchase%' THEN 1 END)) AS purchase_count,
    COUNT(DISTINCT(CASE WHEN event_name LIKE '%view_item%' THEN 1 END)) AS view_item_count,
    COUNT(DISTINCT(CASE WHEN event_name LIKE '%add_shipping_info%' THEN 1 END)) AS add_shipping_info_count,
    COUNT(DISTINCT(CASE WHEN event_name LIKE '%add_payment_info%' THEN 1 END)) AS add_payment_info_count,
    COUNT(DISTINCT user_session_id) AS user_sessions_count
  FROM
    query
  GROUP BY
    event_date, user_session_id, ssource, medium, campaign, event_name, country, landing_page_location, device_language, device_category, device_os
)
SELECT
  event_date,
  user_session_id,
  ssource,
  medium,
  campaign,
  landing_page_location,
  device_language,
  device_category,
  device_os,
  country,
  event_name,
  user_sessions_count,
  view_item_count,
  begin_checkout_count,
  add_shipping_info_count,
  add_payment_info_count,
  add_to_cart_count,
  purchase_count,
FROM
  query2;
```
</details>

## 🔍 BigQuery
The SQL query used in this project is accessible via the link below:

🔗 **BigQuery Query Link**: [Click here to open the query](https://console.cloud.google.com/bigquery?sq=441610422913:e95c731a0a9c49c2acbad62add9bce00)

## 📊 Analysis Overview

### This query was designed to analyze the following data:
- **Events**: Session start, product view, add to cart, payment info entry, and purchase.
- **User Attributes**: Devices, operating systems, countries, and traffic sources.

---

## 📂 Dashboard Features

The following visualizations were created in Tableau:
1.🖥 **Device Categories**: Shows which devices users are using.
2.📈 **Conversion Rates Over Time**: Tracks user sessions and conversion rates over time.
3.🪜 **Funnel Chart**: Analyzes user steps in the conversion funnel.
4.🌍 **Sessions by Country**: Displays user sessions geographically on a map.
5.🔗 **Sources and Conversion Rates**: Compares conversion rates across traffic sources.
6.📑 **Landing Page Performance**: Analyzes which landing pages users are coming from.

---

## 🖼 Dashboard Preview
 ![E-Commerce Dashboard](https://github.com/Necodk/Customers-Journey-Map-and-Funnel-Chart-Analysis/blob/main/Dashboard%20.png)

🔗 **Interactive Tableau Dashboard Link**: [View the dashboard here](https://public.tableau.com/shared/2627GNN2Y?:display_count=n&:origin=viz_share_link)


---

## 🛠 How to Use

### Extract Data from BigQuery:
1. Run the above SQL query in BigQuery.
2. Export the resulting data to Tableau.

### Open and Visualize the Dashboard:
1. Open the `E-Commerce-Dashboard.twbx` file in Tableau.
2. Update the data to reproduce the analyses.

---

## 📈 Results
- **Total Sessions**: 354,857
- **Total Purchases**: 4,745
- **Conversion Rate (CR)**: 1.34%

## 🎨 Visual Summary
This dashboard helps business analysts and decision-makers understand user behavior, identify areas for improvement in the conversion funnel, and optimize campaigns to boost performance. The interactive Tableau dashboard makes it easy to dive deeper into the insights.

