Data Analysis and Strategies for E-commerce
===========================================

Leveraging SQL and Power BI for Business Analysis
-------------------------------------------------


Welcome to the vibrant world of Brazilian **e-commerce**, where Olist stands out as an innovative and dynamic entity.

This case study delves into the intricate workings of Olist, a Brazilian marketplace that bridges small businesses across Brazil with a vast network of online sales channels. Utilizing real, anonymized data, this study offers a comprehensive look into **key facets of e-commerce operations.**

Our journey will explore various critical elements of **business analytics**, shedding light on the following areas:

*   **Revenue Analysis**: Understanding the sources and trends of Olist’s revenue.
*   **Average Order Value (AOV)**: Examining the average spending per order and its implications for sales strategies.
*   **Customer Lifetime Value (CLV)**: Evaluating the long-term value of customer relationships and its impact on business growth.
*   **Customer Segmentation:** Applying techniques like RFM (Recency, Frequency, Monetary) analysis to categorize customers based on their purchasing behaviors.
*   **Churn Rate Analysis:** Identifying the rate at which customers stop purchasing and strategizing on retention.
*   **Correlation Studies:** Investigating relationships between various business aspects such as shipping costs, product characteristics, and customer reviews.

This educational journey aims not just to present data but to transform it into actionable strategies.

Company
=======

**Olist is a Brazilian marketplace** that acts as a powerful link between small businesses spread throughout Brazil and a vast network of online sales channels. With a single contract, **these sellers can sell their products through the Olist Store, benefiting from simplified logistic** management thanks to Olist’s logistic partners.

More information is available on Olist’s website: [www.olist.com](http://www.olist.com/).

This case study delves into a public Brazilian dataset, made freely available under a [Creative Commons license](https://creativecommons.org/licenses/by-nc-sa/4.0/) on the [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) platform.

The real data is anonymized and concerns orders made at the Olist Store. **The dataset includes details on 100,000 orders placed between 2016 and 2018.** This information allows for the analysis of orders from multiple perspectives.

My study focuses on some key business questions that Olist poses to optimize and continuously improve its operations and customer satisfaction.

Goal
====

During this analysis, I will answer **the fundamental business question**:

_How can Olist optimize its operational and marketing strategy to improve profitability, increase customer satisfaction, and reduce delivery times, based on the analysis of historical sales data, customer reviews, and logistic metrics?_

Database Structure
==================

The **Olist database is composed of different datasets**:

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://i.imgur.com/HRhd2Y0.png">
  <source media="(prefers-color-scheme: light)" srcset="https://i.imgur.com/HRhd2Y0.png">
  <img alt="Shows datasets from Olist" src="https://i.imgur.com/HRhd2Y0.png">
</picture>


Source: [Kaggle Olist Store](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)


## Database Glossary

**Each entry in the glossary describes in detail the fields present in each of the datasets** that make up the database. This knowledge is **essential to ensure correct interpretation of the data** during analysis.

I have also renamed each database in a simpler way, by removing the words “olist” and “dataset” from the name of each dataset.

**1\. olist\_order\_customer\_dataset**

Renamed: order\_customer

\> customerd\_id => key to the orders dataset. Each order has a unique customer\_id

\> customers\_unique\_id => unique identifier of a customer

\> customers\_zip\_code\_prefix => first five digits of customer zip code

\> customer\_city => customer city name

\> customer\_state => customer state

**2\. olist\_order\_dataset**

Renamed: order

\> order\_id => unique identifier of the order

\> customer\_id => key to the customer dataset. Each order has a unique customer\_id

\> order\_status => reference to the order status (delivered, shipped, etc)

\> order\_purchase\_timestamp => shows the purchase timestamp

\> order\_approved\_at => shows the payments approval timestamp

\> order\_delivered\_carrier\_state => shows the order posting timestamp. When it was handled to the logistic partner

\> order\_delivered\_customer\_date => shows the actual order delivery date to the customer

\> order\_estimated\_delivery\_date => shows the estimated delivery date that was informed to customer at the purchase moment

**3\. olist\_order\_reviews\_dataset**

Renamed: order\_reviews

\> review\_id => unique review identifier

\> order\_id => unique order identifier

\> review\_score => note ranging from 1 to 5 given by the customer on a satisfaction survey

\> review\_comment\_title => comment title from the review left by the customer, in Portuguese

\> review\_comment\_message => comment message from the review left by the customer, in Portuguese

\> review\_creation\_date => shows the date in which the satisfaction survey was sent to the customer

\> review\_answer\_timestamp => shows satisfaction survey answer timestamp

**4\. olist\_order\_payments\_dataset**

Renamed: order\_payments

\> order\_id => unique identifier of an order

\> payment\_sequential => a customer may pay an order with more than one payment method. if he does so, a sequence will be created to accomodate all payments

\> payment\_type => method of payment chosen by the customer

\> payment\_installments => number of installments chosen by the customer

\> payment\_value => transaction value. This field tells us the total cost paid by any customer along with the shipping charges. That means the payment value is the summation of price and freight value.

**5\. olist\_order\_items\_dataset**

Renamed: order\_items

\> order\_id => order unique identifier

\> order\_item\_id => sequential number identifying number of items included in the same order

\> product\_id => product unique identifier

\> seller\_id => seller unique identifier

\> shipping\_limit\_date => shows the seller shipping limit date for handling the order over to the logistics partner

\> price => item price

\> freight\_value => item freight value (if an order has more than one item the freight value is splitted between items)

**6\. olist\_products\_dataset**

Renamed: products

\> product\_id => unique product identifier

\> product\_category\_name => root category of product, in Portuguese

\> product\_name\_lenght => number of characters extracted from the product name

\> product\_description\_lenght => number of characters extracted from the product product\_description\_lenght

\> product\_photos\_qty => number of product published product\_photos\_qty

\> product\_weight\_g => product weight measured in grams

\> product\_length\_cm => product length measured in centimeters

\> product\_height\_cm => product height measured in centimeters

\> product\_width\_cm => product width measured in centimeters

**7\. olist\_sellers\_dataset**

Renamed: sellers

\> seller\_id => seller unique identifier

\> seller\_zip\_code\_prefix => first 5 digits of seller zip codes

\> seller\_city => seller city name

\> seller\_state => seller state

**8\. olist\_geolocation\_dataset**

Renamed: geolocation

\> geolocation\_zip\_code\_prefix => first 5 digits of zip code

\> geolocation\_lat => latitude

\> geolocation\_lng => longitude

\> geolocation\_city => city name

\> geolocation\_state => state

**9\. product\_category\_name\_translation**

Renamed: category\_name\_translation

\> product\_category\_name => category name in Portuguese

\> product\_category\_name\_english => category name in english

_The English category name has been directly integrated into the “products” dataset, allowing us to eliminate this dataset from our analysis._

## ER Schema

To **understand the schema of the datasets** in the database, I use the INFORMATION_SCHEMA.COLUMNS view according to this model:


```SQL

SELECT 
  column_name,
  data_type
FROM `<your-gcp-project-id>`.<your-bigquery-dataset-name>.INFORMATION_SCHEMA.COLUMNS
WHERE table_name="<your-bigquery-table-name>"

```

```SQL

--Database Schema

SELECT
  column_name,
  data_type
FROM `my-case-studies-270287.brazilian_marketplace.INFORMATION_SCHEMA.COLUMNS`
WHERE table_name = "customers";

```

This process is applied for each of the datasets (tables) present in the database.

I use a tool like [QuickDBD](http://quickdbd/) to **obtain the ER (Entity-Relationship) schema of the database** along with its primary keys.

This schema is **used to graphically represent the datasets and the relationships between them.**

Here is the complete procedure used to obtain the ER schema on QuickDBD:

1.  Retrieve the schema of BigQuery tables. Write the query following this schema:

```SQL

SELEZIONA
 nome_colonna, tipo_dati 
FROM
 ` < il tuo - progetto - gcp - id > `. < il tuo - nome - bigquery - set di dati > .INFORMATION_SCHEMA.COLUMNS 
WHERE
 nome_tabella = "<nome-tabella-bigquery>"

```

2\. Run the query above

3\. Click “SAVE RESULTS” and then click “Copy to Clipboard”

4\. Paste back to QDB, click “EDIT” and then “Untabify”

5\. Repeat Step 2 for the rest of your tables to be included.

By doing this, I obtain an **overview of the connections** between the various datasets and their primary keys:

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://esterpinna.it/wp-content/uploads/2024/02/F2_database-model.jpg">
  <source media="(prefers-color-scheme: light)" srcset="https://esterpinna.it/wp-content/uploads/2024/02/F2_database-model.jpg">
  <img alt="ER schema" src="https://esterpinna.it/wp-content/uploads/2024/02/F2_database-model.jpg">
</picture>

Created with [QuickDBD](http://quickdbd/)

Our database also includes the “product\_category\_name\_translation” dataset, which provides English translations for various product categories.

To make accessing this information more efficient and reduce database complexity, I have chosen to integrate the English translations directly into the “products” table.

This step eliminates the need for a separate table for translations, thus reducing redundancy and simplifying future database queries.

```SQL

--Aggiungi una nuova colonna per il nome della categoria inglese alla tabella dei prodotti 
ALTER  TABLE brazilian_marketplace.products 
ADD  COLUMN product_category_name_eng STRING; 


--Aggiorna il nome della categoria inglese nella tabella dei prodotti utilizzando la tabella di traduzione 
UPDATE brazilian_marketplace.products AS pc 
SET pc.product_category_name_eng = pct.product_category_name_english 
FROM brazilian_marketplace.product_category_name_translation AS pct 
WHERE pc.product_category_name = pct.product_category_name;

```

Data Cleaning
=============

Check for Missing Values
========================

**We look for _null_ values**:

```SQL

--Check for Missing Values
SELECT *
FROM `my-case-studies-270287.brazilian_marketplace.products`
WHERE product_category_name_eng IS NULL;

```

And so on for each column of the table, for all tables.

In the absence of further indications, most _null_ values cannot be fixed.

However, there are some missing translations (_null_ values) for some product categories:

For the category “portateis\_cozinha\_e\_preparadores\_de\_alimentos,” I assign the corresponding English category name “kitchen\_and\_food\_preparation\_portable\_devices”;

For the category “pc\_gamer,” I assign the corresponding English category name “gaming\_pc.”

The following queries are used to assign the English category name:

```SQL

--Display rows where the English category name is null but the original category name is not null
SELECT product_category_name_eng, product_category_name
FROM `my-case-studies-270287.brazilian_marketplace.products`
WHERE product_category_name_eng IS NULL
AND product_category_name IS NOT NULL;


--Update the English category name where missing, based on specific Portuguese category names
UPDATE `my-case-studies-270287.brazilian_marketplace.products`
SET product_category_name_eng =
  CASE
    WHEN product_category_name = "portateis_cozinha_e_preparadores_de_alimentos"
      THEN "kitchen_and_food_preparation_portable_devices"
    WHEN product_category_name = "pc_gamer"
      THEN "gaming_pc"
    ELSE product_category_name_eng
  END
WHERE product_category_name_eng IS NULL;

```

_Null_ English category names remain where they are also missing in the original language. We cannot replace these _null_ values, as we have no further indications.

Checking for Duplicate Data
===========================

In each table of the database, it is essential to **verify the presence of duplicate rows**. However, in this specific case, such verification is superfluous because each table has a column containing unique data, ensuring the uniqueness of each row.

Data Analysis
=============

Profitability Analysis
======================

Total Revenue
=============

**To accurately determine Olist’s revenue, it is crucial to identify which orders actually contribute to the final revenue.** This process involves analyzing the status of each order to ensure that only those that are completed and delivered are included in the total revenue calculation.

```SQL

--Type of distinct order statuses
SELECT DISTINCT order_status
FROM `my-case-studies-270287.brazilian_marketplace.orders`;

```

There are 8 different order statuses.

**Only the orders with the status “delivered” contribute to the revenue**.

```SQL

\--Total revenue  
SELECT ROUND(SUM(op.payment\_value),0) AS total\_revenue  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
  ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered";

```

We have a total revenue of €15,422,462. But over what time period?

To **determine the time period under review**, I proceed with the calculation of the date of the first and last purchase and then the time span under review, in terms of days, weeks, months, years:

```SQL

\--Time Span in Days  
SELECT  
  MIN(order\_purchase\_timestamp) AS started\_time,  
  MAX(order\_purchase\_timestamp) AS ended\_time,  
  DATETIME\_DIFF(DATETIME(MAX(order\_purchase\_timestamp)), DATETIME(MIN(order\_purchase\_timestamp)), DAY) AS difference  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\`;

```

We have **data for the period from September 4, 2016, to October 17, 2018**, a time span of **773 days.**

```SQL

\--Time Span in Weeks  
SELECT  
  MIN(order\_purchase\_timestamp) AS started\_time,  
  MAX(order\_purchase\_timestamp) AS ended\_time,  
  DATETIME\_DIFF(DATETIME(MAX(order\_purchase\_timestamp)), DATETIME(MIN(order\_purchase\_timestamp)), WEEK) AS difference  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\`;

```

**It’s 110 weeks.**

```SQL

\--Time Span in Months  
SELECT  
  MIN(order\_purchase\_timestamp) AS started\_time,  
  MAX(order\_purchase\_timestamp) AS ended\_time,  
  DATETIME\_DIFF(DATETIME(MAX(order\_purchase\_timestamp)), DATETIME(MIN(order\_purchase\_timestamp)), MONTHS) AS difference  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\`;

```

**It’s 25 months.**

```SQL

\--Time Span in Years  
SELECT  
  MIN(order\_purchase\_timestamp) AS started\_time,  
  MAX(order\_purchase\_timestamp) AS ended\_time,  
  DATETIME\_DIFF(DATETIME(MAX(order\_purchase\_timestamp)), DATETIME(MIN(order\_purchase\_timestamp)), YEAR) AS difference  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\`;

```

**It’s 2 years.**

Therefore, the period is from September 4, 2016, to October 17, 2018, totaling 773 days (about 2 years and 43 days).

Analysis of Periodic Average Revenue
====================================

I can now calculate the average daily, weekly, monthly, and annual revenue:

The **average daily revenue** is calculated as follows:

\--Average revenue per day  
SELECT ROUND(15422462 / 773, 0) AS avg\_revenue\_per\_day;

The average daily revenue is €19,951.

The **average weekly revenue** is calculated as follows:

\--Average revenue per week  
SELECT ROUND(15422462 / 110, 0) AS avg\_revenue\_per\_week;

The average weekly revenue is €140,204.

The **average monthly revenue** is calculated as follows:

\--Average revenue per month  
SELECT ROUND(15422462 / 25, 0) AS avg\_revenue\_per\_month;

The average monthly revenue is €616,898.

The **average annual revenue** is calculated as follows:

\--Average revenue per year  
SELECT ROUND(15422462 / 2, 0) AS avg\_revenue\_per\_year;

The average annual revenue is €7,711,231.

**These data are useful** when monitoring the annual, monthly, weekly, and daily trends t**o understand whether the performance is above or below average,** and thus make timely adjustments.

Let’s now see how revenues have varied over the considered time period:

Revenue Trends Over Time
========================

Let’s examine the annual and quarterly revenue trends.

For the **revenue annual trend**:

\--Changing over years  
SELECT  
  EXTRACT(YEAR FROM DATETIME(o.order\_purchase\_timestamp)) AS year,  
  ROUND(SUM(op.payment\_value),0) AS annual\_total\_revenue  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
  ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered"  
GROUP BY year  
ORDER BY year;

This shows a consistent increase in revenue over the years.

For the **revenue quarterly trend**:

\--Changing over quarters  
SELECT  
  EXTRACT(YEAR FROM DATETIME(o.order\_purchase\_timestamp)) AS year,  
  EXTRACT(QUARTER FROM DATETIME(o.order\_purchase\_timestamp)) AS quarter,  
  ROUND(SUM(op.payment\_value), 0) AS quarter\_total\_revenue  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
  ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered"  
GROUP BY year, quarter  
ORDER BY year, quarter;

Here, we observe a consistent growth across quarters within the period.

Seasonality of Sales
====================

A thorough analysis of the seasonality of sales is not possible for the years 2016 and 2018 due to **incomplete data**, as some months are missing:

\--Changing over months  
SELECT  
  EXTRACT(YEAR FROM DATETIME(o.order\_purchase\_timestamp)) AS year,  
  EXTRACT(QUARTER FROM DATETIME(o.order\_purchase\_timestamp)) AS quarter,  
  EXTRACT(MONTH FROM DATETIME(o.order\_purchase\_timestamp)) AS month,  
  ROUND(SUM(op.payment\_value),0) AS month\_total\_revenue  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
  ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered"  
GROUP BY year, quarter, month  
ORDER BY year, quarter, month;

However, for the year with complete data — 2017 — **we notice a revenue peak** in November, likely linked to **Black Friday** promotional sales.

This trend is more clearly visualized in the Power BI chart:

Additionally, we can derive an **average monthly revenue**:

\--Changing over months  
SELECT  
  EXTRACT(MONTH FROM DATETIME(o.order\_purchase\_timestamp)) AS month,  
  ROUND(AVG(op.payment\_value),0) AS month\_avg\_revenue  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
  ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered"  
GROUP BY month  
ORDER BY month;

This analysis highlights that **some months** — April, September, and October **— have the highest revenues.**

This trend is particularly evident in the graph below, which illustrates the monthly revenue distribution.

\***Note**: The graph is taken directly from BigQuery’s “preview chart” function.

Top Popular Products and Categories
===================================

Let’s analyze **the 10 most popular products in terms of quantity sold**.

We only have the product IDs available, which, while not immediately meaningful to us, are certainly useful for the company to match each ID to its specific product.

Additionally, we observe **the 5 most popular product categories, both in terms of the number of products sold and the revenues generated.**

This analysis will help us understand **which products and categories drive sales and contribute most significantly to the company’s revenue.**

Top Popular Products in Terms of Items Sold:
============================================

\--Counting the Top 10 Best-Selling Products  
SELECT oi.product\_id, COUNT(oi.product\_id) AS n\_of\_products\_sold, p.product\_category\_name, p.product\_category\_name\_eng  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_items\` AS oi  
  ON o.order\_id = oi.order\_id  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.products\` AS p  
  ON oi.product\_id = p.product\_id  
WHERE o.order\_status = "delivered"  
GROUP BY oi.product\_id, p.product\_category\_name, p.product\_category\_name\_eng  
ORDER BY n\_of\_products\_sold DESC  
LIMIT 10;

**The most sold product** is identified by the ID aca2eb7d00ea1a7b8ebd4e68314663af, belonging to the furniture\_decor category.

Top Popular Categories in Terms of Items Sold:
==============================================

\--Counting the Top 5 Best-Selling Categories  
WITH total\_products\_sold AS (  
    SELECT COUNT(\*) AS total\_sold  
    FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
    INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_items\` AS oi  
      ON o.order\_id = oi.order\_id  
    WHERE o.order\_status = "delivered"  
)  
  
  
SELECT p.product\_category\_name\_eng,  
  COUNT(o.order\_id) AS n\_of\_products\_per\_category\_sold,  
  ROUND((COUNT(o.order\_id)/  
                          (SELECT total\_sold  
                          FROM total\_products\_sold))\*100, 2) AS percent\_sold\_per\_category  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_items\` AS oi  
  ON o.order\_id = oi.order\_id  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.products\` AS p  
  ON oi.product\_id = p.product\_id  
WHERE o.order\_status = "delivered"  
GROUP BY p.product\_category\_name\_eng  
ORDER BY n\_of\_products\_per\_category\_sold DESC  
LIMIT 5;

**The most popular categories**, in order of revenue generated, are

bed\_bath\_table,

health\_beauty,

sports\_leisure.

Top Popular Categories in Terms of Items Sold and Revenues Generated
====================================================================

\--Comparing Number of Items Sold and Sales Volumes of the Most Popular Categories  
WITH total\_revenue\_gained AS (  
  SELECT SUM(op.payment\_value) AS total\_revenue  
  FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
  INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
    ON o.order\_id = op.order\_id  
  WHERE o.order\_status = "delivered"  
),  
  
  
total\_products\_sold AS (  
  SELECT COUNT(\*) AS total\_sold  
  FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
  INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_items\` AS oi  
    ON o.order\_id = oi.order\_id  
  WHERE o.order\_status = "delivered"  
)  
  
  
  
  
SELECT p.product\_category\_name\_eng,  
  COUNT(p.product\_category\_name\_eng) AS n\_of\_products\_per\_category\_sold,  
  ROUND(SUM(op.payment\_value),0) AS revenue\_per\_category\_sold,  
  ROUND((COUNT(o.order\_id)/  
                          (SELECT total\_sold  
                          FROM total\_products\_sold))\*100, 2) AS percent\_sold\_per\_category,  
  ROUND((SUM(op.payment\_value)/  
                             (SELECT total\_revenue  
                             FROM total\_revenue\_gained))\*100, 2) AS percent\_revenue\_per\_category  
FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_items\` AS oi  
  ON o.order\_id = oi.order\_id  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.products\` AS p  
  ON oi.product\_id = p.product\_id  
INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
  ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered"  
GROUP BY p.product\_category\_name\_eng  
ORDER BY n\_of\_products\_per\_category\_sold DESC  
LIMIT 5;

From the analysis results, we can observe:

**Most Popular Category in Terms of Number of Items Sold**

*   The category “bed\_bath\_table” emerges as the most popular in terms of items sold, with 11,650 items. This accounts for over 10% of the total sales, indicating that more than one out of every ten items sold belongs to this category.

**Most Popular Category in Terms of Revenue**

*   Although “bed\_bath\_table” also leads in revenue percentage (10.98%), the “health\_beauty” category nearly matches it in overall revenue (10.51%) despite a lower quantity of items sold. This suggests that items in the “health\_beauty” category might have a higher average price compared to “bed\_bath\_table”.
*   A similar observation is noted for the “computers\_accessories” category. Although it accounts for 10.05% of revenue, it represents only 7.17% of sales, indicating that, like “health\_beauty”, it may have higher-priced items on average

**Knowing which products and categories are most sold can be extremely useful for optimizing inventory and shaping marketing strategies**. For instance, Olist could focus on stocking higher quantities of popular categories while also considering pricing strategies that reflect the demand and popularity of these categories.

This approach could help in maximizing revenue and ensuring customer satisfaction by readily having in-demand products available.

AOV — Calculation of the Average Order Value
============================================

\--AOV = revenue\_tot / num\_ordini  
WITH tot\_revenue AS (  
  SELECT ROUND(SUM(op.payment\_value),0) AS total\_revenue  
  FROM \`my-case-studies-270287.brazilian\_marketplace.orders\` AS o  
  INNER JOIN \`my-case-studies-270287.brazilian\_marketplace.order\_payments\` AS op  
    ON o.order\_id = op.order\_id  
  WHERE o.order\_status = "delivered"  
  ),  
  
  
tot\_orders AS (  
  SELECT COUNT(DISTINCT order\_id) AS n\_of\_orders  
  FROM \`my-case-studies-270287.brazilian\_marketplace.orders\`  
)  
  
  
SELECT  
  ROUND((SELECT total\_revenue  
  FROM tot\_revenue)/  
                (SELECT n\_of\_orders  
                FROM tot\_orders),0) AS AOV;

**The Average Order Value** is €155.

CLV — Assessing Customer Lifetime Value Over Time
=================================================

To calculate the Customer Lifetime Value (CLV), I’ve chosen three methods:

*   **Average Monthly CLV for the Entire Customer Base.**

This approach provides an aggregated and periodic view of the CLV. It is **useful for monitoring trends over time**, assessing the effectiveness of marketing campaigns or strategic changes, and for making periodic comparisons (for example, monthly or yearly).

*   **CLV for Each Individual Customer.**

This approach is useful for understanding the value of each customer individually and can be **used to segment customers**, identify high-value customers, and customize marketing or sales strategies based on the value of each customer.

*   **CLV as an Average of All Customers.**

This method is useful for a general assessment of the value of the customer portfolio. It can be **used for strategic planning and as a benchmark** to evaluate the effectiveness of marketing and sales initiatives. It helps to determine the reasonable level of investment in customer acquisition and retention based on the average value that a customer brings over their lifetime.

For example, the Customer Acquisition Cost (CAC) should always be lower than the CLV.

**To calculate the customer lifetime value, I will first need to calculate other metrics**.

Here is a list:

**Average Purchase Value** = total revenue/total number of purchases

**Average Purchase Frequency Rate** = total number of purchases/total number customers

**Average Customer Value** = Average Purchase Value\*Average Purchase Frequency Rate

**Average Customer Lifespan** = sum of customer lifespans/total number of customers

At this point, we can calculate the Customer Lifetime Value as follows:

**Customer Lifetime Value** \= Average Customer Value\*Average Customer Lifespan

Average Monthly CLV for the Entire Customer Base
================================================

(step by step)

\--Customer Lifetime Value as a Monthly Average  
  
  
/\* Step 1: Calculate Total Revenue and Total Number of Purchases  
Calculate the total revenue and the total number of purchases on a monthly basis. \*/  
WITH monthly\_revenue\_purchases AS (  
  SELECT  
    DATE\_TRUNC(o.order\_purchase\_timestamp, MONTH) AS month,  
    SUM(op.payment\_value) AS total\_revenue,  
    COUNT(o.order\_id) AS total\_purchases  
  FROM \`brazilian\_marketplace.orders\` AS o  
  INNER JOIN \`brazilian\_marketplace.order\_payments\` AS op  
    ON o.order\_id = op.order\_id  
  WHERE o.order\_status = "delivered"  
  GROUP BY month  
),  
  
  
/\* Step 2: Calculate the Average Purchase Value (APV)  
Calculate the APV as the total revenue divided by the total number of purchases. \*/  
average\_purchase\_value AS (  
  SELECT  
    month,  
    total\_revenue/total\_purchases AS average\_purchase\_value  
    FROM monthly\_revenue\_purchases  
),  
  
  
/\* Step 3: Calculate Total Number of Customers  
Determine the total number of unique customers each month. \*/  
monthly\_customers AS (  
  SELECT  
    DATE\_TRUNC(o.order\_purchase\_timestamp, MONTH) AS month,  
    COUNT(DISTINCT c.customer\_unique\_id) AS total\_customers  
  FROM \`brazilian\_marketplace.orders\` AS o  
  INNER JOIN \`brazilian\_marketplace.customers\` AS c  
    ON o.customer\_id = c.customer\_id  
  WHERE o.order\_status = "delivered"  
  GROUP BY month  
),  
  
  
/\* Step 4: Calculate the Average Purchase Frequency Rate (APFR)  
Calculate the APFR as the total number of purchases divided by the total number of customers. \*/  
purchase\_frequency AS (  
  SELECT  
    mrp.month,  
    mrp.total\_purchases/mc.total\_customers AS average\_purchase\_frequency\_rate  
  FROM monthly\_revenue\_purchases AS mrp  
  INNER JOIN monthly\_customers AS mc  
    ON mrp.month =  mc.month  
),  
  
  
/\* Step 5: Calculate the Average Customer Value (ACV)  
Multiply the APV by the APFR to obtain the ACV. \*/  
average\_customer\_value AS (  
  SELECT  
    av.month,  
    av.average\_purchase\_value\*pf.average\_purchase\_frequency\_rate AS average\_customer\_value  
  FROM average\_purchase\_value AS av  
  INNER JOIN purchase\_frequency AS pf  
    ON av.month = pf.month  
),  
  
  
/\* Step 6: Calculate the Average Customer Lifespan (ACL)  
For the ACL, you'll need the total duration of each customer's relationship and its average. \*/  
customer\_lifespans AS (  
customer\_lifespans AS (  
  SELECT  
    c.customer\_unique\_id,  
    DATE\_DIFF(MAX(DATETIME(o.order\_purchase\_timestamp)), MIN(DATETIME(o.order\_purchase\_timestamp)), MONTH) AS customer\_lifespan  
  FROM \`brazilian\_marketplace.orders\` AS o  
  INNER JOIN \`brazilian\_marketplace.customers\` AS c  
    ON o.customer\_id = c.customer\_id  
  WHERE o.order\_status = "delivered"  
  GROUP BY c.customer\_unique\_id  
),  
  
  
average\_customer\_lifespan AS (  
  SELECT  
    AVG(customer\_lifespan) AS average\_customer\_lifespan  
  FROM  
    customer\_lifespans  
)  
  
  
/\* Step 7: Calculate the Customer Lifetime Value (CLV)  
Finally, multiply the ACV by the ACL to obtain the CLV. \*/  
SELECT  
  acv.month,  
  acv.average\_customer\_value \* acl.average\_customer\_lifespan AS customer\_lifetime\_value  
FROM  
  average\_customer\_value AS acv  
CROSS JOIN  
  average\_customer\_lifespan AS acl  
ORDER BY month;

We then obtain the **average monthly CLV.**

CLV for Each Individual Customer
================================

\--Customer Lifetime Value for Each Individual Customer  
WITH customer\_orders AS (  
    SELECT  
        c.customer\_unique\_id,  
        COUNT(o.order\_id) AS total\_orders,  
        ROUND(AVG(p.payment\_value),2) AS avg\_order\_value  
    FROM  
        \`brazilian\_marketplace.customers\`AS c  
    JOIN  
        \`brazilian\_marketplace.orders\` AS o  
      ON c.customer\_id = o.customer\_id  
    JOIN  
        \`brazilian\_marketplace.order\_payments\` AS p ON o.order\_id = p.order\_id  
    WHERE  
        o.order\_status = 'delivered'  
    GROUP BY  
        c.customer\_unique\_id  
),  
customer\_lifetime AS (  
    SELECT  
        customer\_unique\_id,  
        DATE\_DIFF(MAX(DATETIME(o.order\_purchase\_timestamp)), MIN(DATETIME(o.order\_purchase\_timestamp)), MONTH) AS customer\_lifetime\_months  
    FROM  
        \`brazilian\_marketplace.orders\` AS o  
    JOIN  
        \`brazilian\_marketplace.customers\` AS c  
      ON o.customer\_id = c.customer\_id  
    GROUP BY  
        customer\_unique\_id  
)  
SELECT  
  co.customer\_unique\_id,  
  co.avg\_order\_value,  
  co.total\_orders,  
  cl.customer\_lifetime\_months,  
  ROUND((co.avg\_order\_value \* co.total\_orders \* cl.customer\_lifetime\_months),2) AS clv  
FROM  
  customer\_orders AS co  
JOIN  
  customer\_lifetime AS cl  
  ON co.customer\_unique\_id = cl.customer\_unique\_id  
ORDER BY clv DESC;

\***Note**: The image above shows only a preview of the result, as the rows are 93,357, which is the number of unique customers. Also, I have sorted the table by the CLV column in descending order; otherwise, the preview would have shown only those customers with a CLV of zero.

We obtain **the CLV for each customer.**

CLV as an Average of All Customers
==================================

\--Average Customer Lifetime Value  
WITH customer\_orders AS (  
    SELECT  
        c.customer\_unique\_id,  
        COUNT(o.order\_id) AS total\_orders,  
        AVG(p.payment\_value) AS avg\_order\_value  
    FROM  
        \`brazilian\_marketplace.customers\`AS c  
    JOIN  
        \`brazilian\_marketplace.orders\` AS o  
      ON c.customer\_id = o.customer\_id  
    JOIN  
        \`brazilian\_marketplace.order\_payments\` AS p ON o.order\_id = p.order\_id  
    WHERE  
        o.order\_status = 'delivered'  
    GROUP BY  
        c.customer\_unique\_id  
),  
customer\_lifetime AS (  
    SELECT  
        customer\_unique\_id,  
        DATE\_DIFF(MAX(DATETIME(o.order\_purchase\_timestamp)), MIN(DATETIME(o.order\_purchase\_timestamp)), MONTH) AS customer\_lifetime\_months  
    FROM  
        \`brazilian\_marketplace.orders\` AS o  
    JOIN  
        \`brazilian\_marketplace.customers\` AS c  
      ON o.customer\_id = c.customer\_id  
    GROUP BY  
        customer\_unique\_id  
),  
customer\_lifetime\_value\_per\_customer AS (  
    SELECT  
        co.customer\_unique\_id,  
        co.avg\_order\_value,  
        co.total\_orders,  
        cl.customer\_lifetime\_months,  
        co.avg\_order\_value \* co.total\_orders \* cl.customer\_lifetime\_months AS clv  
    FROM  
        customer\_orders AS co  
    JOIN  
        customer\_lifetime AS cl  
      ON co.customer\_unique\_id = cl.customer\_unique\_id  
)  
SELECT  
  AVG(clv) AS avg\_clv  
FROM customer\_lifetime\_value\_per\_customer;

From this result, we get the following:

**Average Customer Lifetime Value (CLV):** amounts to €29.21, representing the average expenditure each customer is expected to make over the course of their interaction with the company. This data is crucial for defining customer acquisition strategies, as it suggests that the cost to acquire a single customer (Customer Acquisition Cost, CAC) should ideally be less than this figure.

**Average receipt:** on average, each order made by customers is valued at about €157.65. That is the average receipt, which is comparable to the AOV we saw earlier; the slight difference between the two values is due to the method of calculating.

**Average number of orders:** on average, each customer has made about 1.08 orders. This number close to 1 suggests that most customers have made only one purchase.

**Average Lifespan**: average duration of the customer relationship is about 0.09 months, or approximately 2.7 days. This very low value indicates that most customer relationships do not extend beyond the initial month.

Segmentazione dei Clienti con RFM
=================================

I have decided to **segment customers based on the potential value they could bring to the company.**

To understand their potential value, I will evaluate three characteristics:

**Recency(R)**: the **time since a customer last made a purchase**. Customers who have made a purchase more recently are considered more valuable, as they are more likely to make a purchase again soon.

**Frequency(F)**: the **number of purchases** a customer has made **over a period**. Customers who make frequent purchases are considered more valuable, as they are more likely to continue making purchases in the future.

**Monetary(M)** value: **the total amount of money a customer has spent**. Customers who spend more money are considered more valuable, as they will likely generate more revenue for the business.

I am therefore segmenting customers based on what is called “**RFM segmentation**” (i.e., based on various combinations of the factors just listed).

I calculate the **Recency** for each customer:

\--recency  
\--How many days have passed since the last purchase for each customer?  
WITH report\_end AS (  
  SELECT MAX(order\_purchase\_timestamp) AS ended\_time  
FROM \`brazilian\_marketplace.orders\`  
)  
SELECT  
  c.customer\_unique\_id,  
  DATE\_DIFF((SELECT ended\_time FROM report\_end), MAX(o.order\_purchase\_timestamp), DAY) as recency,  
FROM \`brazilian\_marketplace.customers\` AS c  
INNER JOIN \`brazilian\_marketplace.orders\` AS o  
 ON c.customer\_id = o.customer\_id  
WHERE o.order\_status = "delivered"  
GROUP BY c.customer\_unique\_id;

The Recency column shows the days passed since the last purchase.

I calculate the **Frequency** for each customer:

\--frequency  
\--How often has the customer made a purchase?  
SELECT  
  c.customer\_unique\_id,  
  COUNT(o.order\_id) as frequency,  
FROM \`brazilian\_marketplace.customers\` AS c  
INNER JOIN \`brazilian\_marketplace.orders\` AS o  
 ON c.customer\_id = o.customer\_id  
WHERE o.order\_status = "delivered"  
GROUP BY c.customer\_unique\_id;

The Frequency column shows the number of orders for each individual customer.

I calculate the **Monetary** Value for each customer:

\--monetary  
\--What is the value contributed by each individual customer?  
SELECT  
  c.customer\_unique\_id,  
  SUM(op.payment\_value) as monetary  
FROM \`brazilian\_marketplace.customers\` AS c  
INNER JOIN \`brazilian\_marketplace.orders\` AS o  
 ON c.customer\_id = o.customer\_id  
INNER JOIN \`brazilian\_marketplace.order\_payments\` AS op  
 ON o.order\_id = op.order\_id  
WHERE o.order\_status = "delivered"  
GROUP BY c.customer\_unique\_id;

The Monetary column shows the total monetary contribution from each customer (i.e., the revenue generated by each customer).

**We can also combine the three queries into a single table** using a CTE, so we can call it up more quickly during analysis:

\--Combine the three queries -- recency, frequency, monetary -- into a single table  
WITH report\_end AS (  
  SELECT  
MAX(order\_purchase\_timestamp) AS ended\_time  
  FROM \`brazilian\_marketplace.orders\`  
),  
  
  
rfm\_customers AS (  
  SELECT  
    c.customer\_unique\_id,  
    DATE\_DIFF((SELECT ended\_time FROM report\_end), MAX(o.order\_purchase\_timestamp), DAY) as recency, --Recency calculation  
    COUNT(o.order\_id) AS frequency, --Frequency calculation  
    ROUND(SUM(op.payment\_value)) AS monetary --Monetary calculation  
  FROM \`brazilian\_marketplace.customers\` AS c  
  INNER JOIN \`brazilian\_marketplace.orders\` AS o  
    ON c.customer\_id = o.customer\_id  
  INNER JOIN \`brazilian\_marketplace.order\_payments\` AS op  
    ON o.order\_id = op.order\_id  
  WHERE o.order\_status = "delivered"  
  GROUP BY c.customer\_unique\_id  
)  
  
  
  
  
\-- We will now only refers to this CTE  
SELECT \* FROM rfm\_customers  
  

**To segment our customers, we will use quintile division** (i.e., dividing into 5 groups) **for each of the three RFM values** (Recency, Frequency, Monetary).

This process creates a grid of **125** (5x5x5) **possible combinations**.

**Each of these combinations represents a different customer segment.**

Among these combinations, we might identify customers who have contributed the highest monetary value (the highest Monetary value), those who made their last purchase a long time ago (the lowest Recency value), or those who make purchases most frequently (the highest Frequency value).

We can also identify customers with combinations of factors, such as those who have contributed the highest monetary value and have the highest frequency — which would be our best customers — and those, for example, who have a low purchase frequency but have spent a lot in a few purchases.

**The combinations we consider will depend on our business strategies.**

**Suppose we want to identify customer groups** that bring the highest value to the business, the most loyal ones, those “at risk,” and the most promising ones:

*   **High Value**: Customers with **the highest monetary value and purchase frequency.**
*   **Loyal**: Customers with **the highest purchase frequency**.
*   **At Risk**: Customers with **the most outdated last purchase**.
*   **Promising**: Customers with **the most recent purchase**.

To identify these groups, I will first divide the customers into quintiles for each of the 3 factors (Recency, Frequency, Monetary) using the NTILE() function.

\--I divide my customers into 5 groups based on the three RFM factors  
quintiles\_customers AS (  
  SELECT  
  customer\_unique\_id,  
  NTILE(5) OVER (ORDER BY recency) AS recency\_quintile,  
  NTILE(5) OVER (ORDER BY frequency) AS frequency\_quintile,  
  NTILE(5) OVER (ORDER BY monetary) AS monetary\_quintile  
FROM  
  rfm\_customers  
)  
  
  
  
\--We will now only refers to this CTE  
SELECT \* FROM quintiles\_customers

Thus, each unique customer will have a value from 1 to 5 for each of the 3 RFM factors depending on the quintile they belong to for that single factor.

**We can now find customers with the sought-after combinations of characteristics:**

*   High Value: Customers in the top quartile for monetary value and frequency and any quartile for Recency.

That is: monetary\_quintile >= 4 AND frequency\_quintile >= 4

*   Loyal: Customers in the top quartile for frequency and any quartile for monetary value and Recency.

That is: frequency\_quintile >= 4

*   At Risk: Customers in the bottom quartile for Recency and any quartile for frequency and monetary value.

That is: recency\_quintile <= 1

*   Promising: Customers in the top quartile for Recency and any quartile for frequency and monetary value.

That is: recency\_quintile >= 4

\--Customer Segmentation  
SELECT  
  customer\_unique\_id,  
  CASE  
    WHEN monetary\_quintile >= 4 AND frequency\_quintile >= 4 THEN "High Value"  
    WHEN frequency\_quintile >= 4 THEN "Loyal"  
    WHEN recency\_quintile <= 1 THEN "At Risk"  
    WHEN recency\_quintile >= 4 THEN "Promising"  
  END AS segment  
FROM quintiles\_customers;

**Each customer is assigned to a group according to the given segmentation.**

Of course, we will also have _null_ values for all those combinations that do not meet any of the 4 identified combinations. They can be useful in the future when we decide to add other customer segments to test a strategy.

**Among the segments we are not analyzing but may decide to target in the future** with a dedicated strategy are:

*   New or Occasional Customers: These customers might have a low frequency (lower quintiles for F) but have made purchases recently (higher quintiles for R). They can have either a high or low monetary value.
*   Dormant Customers: Customers with low Recency and Frequency values but who have had a high monetary value in the past. These customers have made purchases in the past but have not made any recent purchases.
*   Sporadic Low-Value Customers: Customers with low frequency and low monetary value, but with a recent purchase. They could be new customers or customers who make purchases only occasionally.
*   Low-Value Loyal Customers: Customers with high frequency (purchase often) but low monetary value. These customers are regular but spend less per purchase.
*   High-Value One-Time Customers: Customers with a low frequency but who have spent a lot in a few purchases. They could be customers who make large-sized purchases but not frequently.
*   Lost Customers: Customers with low values in both Recency and Frequency, and possibly in Monetary as well. They have made purchases in the past but are no longer active.

**Now, let’s return to the 4 identified groups (“High Value”, “Loyal”, “At Risk”, “Promising”) to see how to visualize them graphically.**

Churn Rate — Customer Turnover Rate
===================================

The churn rate is the percentage of customers who stop purchasing from a store — in our case, the Olist marketplace — during a specified period.

First, we need to understand **what time frame to look at**: are we interested in analyzing the monthly churn rate? Yearly?

\--Calculation of the average customer lifespan within my customer base to determine the time period for analyzing the churn rate  
WITH lifespan\_per\_customer AS (  
  SELECT  
  c.customer\_unique\_id,  
  DATE\_DIFF(MAX(DATETIME(o.order\_purchase\_timestamp)), MIN(DATETIME(o.order\_purchase\_timestamp)), DAY) AS customer\_lifespan  
  FROM \`brazilian\_marketplace.customers\` AS c  
INNER JOIN \`brazilian\_marketplace.orders\` AS o  
  USING (customer\_id)  
WHERE o.order\_status = "delivered"  
GROUP BY c.customer\_unique\_id  
ORDER BY customer\_lifespan DESC  
)  
  
  
SELECT ROUND(AVG(customer\_lifespan),2) AS avg\_customer\_lifespan  
FROM lifespan\_per\_customer;

**In this case, we have a database that covers a period of a year and a few months,** so it wouldn’t make sense to analyze the annual churn rate.

We know the average customer lifespan is 2.7 days, so it would make sense to analyze the churn rate on a weekly or monthly basis.

**We will look at the monthly churn rate of customers.**

\--Comparing the number of active customers each month with the previous month  
WITH monthly\_customers AS (  
SELECT  
  DATETIME\_TRUNC(o.order\_purchase\_timestamp, MONTH) AS month,  
  COUNT(DISTINCT c.customer\_unique\_id) AS month\_customers  
FROM \`brazilian\_marketplace.customers\` AS c  
INNER JOIN \`brazilian\_marketplace.orders\` AS o  
  USING(customer\_id)  
WHERE order\_status = "delivered"  
GROUP BY month  
ORDER BY month ASC  
),  
  
  
previous\_monthly\_customers AS (  
  SELECT  
    month,  
    month\_customers,  
    LAG(month\_customers, 1) OVER(ORDER BY month) AS previous\_month\_customers  
  FROM monthly\_customers  
  ORDER BY month ASC  
),  
  
  
comparison\_monthly\_customers AS (  
  SELECT  
    month,  
    month\_customers,  
    previous\_month\_customers,  
    month\_customers - previous\_month\_customers AS monthly\_variation,  
  FROM previous\_monthly\_customers  
  ORDER BY month ASC  
)  
  
  
\--Calculating the monthly variation rate in the number of customers  
 SELECT  
    month,  
    month\_customers,  
    previous\_month\_customers,  
    monthly\_variation,  
    ROUND((monthly\_variation/previous\_month\_customers)\*100, 2) AS monthly\_variation\_rate  
FROM comparison\_monthly\_customers  
ORDER BY month ASC

**NOTE**: In this case, in the monthly\_variation\_rate column, we have negative numbers when the variation represents a churn rate, and a positive number when the variation represents a growth rate.

Let’s transform the last query into a CTE, name it “variation\_rate\_per\_month” so that we can use it to calculate the average rate of variation.

. . .  
variation\_rate\_per\_month AS(  
   SELECT  
    month,  
    month\_customers,  
    previous\_month\_customers,  
    monthly\_variation,  
    ROUND((monthly\_variation/previous\_month\_customers)\*100, 2) AS monthly\_variation\_rate  
FROM comparison\_monthly\_customers  
ORDER BY month ASC  
)  
  
  
SELECT ROUND(AVG(monthly\_variation\_rate),2) AS avg\_variation\_rate  
FROM variation\_rate\_per\_month;

We notice that an average variation rate of **+4,455.01% is not a churn rate but rather an extremely surprising growth rate**, perhaps because the available data covers the very first months of Olist’s activity.

Correlation between Shipping Cost and Product Characteristics
=============================================================

For this analysis, I used the Power BI visualization tool.

I analyzed the **correlation between the average freight value and some product characteristics, such as weight and volume.**

Correlation between Average Shipping Cost and Product Weight
============================================================

From the graph, we can see that there is a **positive correlation between the weight of the product and the freight value**: as the weight of the product increases, so does the freight value. However, this relationship does not seem to be strongly linear, suggesting that **factors other than weight influence freight value.**

We also observe that most of the data points are concentrated in the lower-left corner, indicating that **most products have a low weight**.

There are also some **outliers**, particularly some points that indicate a very high shipping value compared to weight. This could be due to special cases where shipping costs are abnormally high, perhaps due to international shipping, remote areas, or premium shipping services.

We also see that while the weights of the products seem to cover a very wide range, the shipping costs seem to stay below a certain threshold.

Correlation between Average Shipping Cost and Product Volume
============================================================

I add a column in PowerBI to calculate the volume:

There does **not seem to be a strong linear correlation between the volume of the product and the average freight value**. While some points indicate that the shipping value increases with volume, there are many exceptions, and the data is quite scattered.

As in the previous graph, most of the data is concentrated in the lower-left part of the graph, suggesting that for **many products, both volume and freight value are relatively low**.

As before, there are also some **outliers**, which seem to suggest that the freight value depends on factors other than just volume.

This suggests that other factors not represented in this graph influence the freight value.

Correlation between Average Shipping Cost, Volume, and Weight of the Product
============================================================================

Let’s see the **correlation between weight, volume, an freight value**:

Most of the data is still concentrated in the lower-left part of the graph, indicating that **both lower volumes and weights tend to have lower freight values.**

There are some **outliers** with high freight values scattered along the x-axis, suggesting that factors other than weight and volume can influence the freight value.

**To better understand the relationship, it would be useful to examine other factors**, such as shipping distance.

Review Score and Influencing Factors
====================================

Average Review Score
====================

\--Calculating the average review score  
SELECT ROUND(AVG(review\_score),2) AS avg\_reviews\_score  
FROM \`brazilian\_marketplace.order\_reviews\`;

The **average score** is 4.09, which is very good considering that the maximum score is 5.

Factors Influencing Review Score
================================

Review Score by Product Category
--------------------------------

\--Product Category  
SELECT  
    p.product\_category\_name,  
    ROUND(AVG(r.review\_score),2) AS avg\_review\_score  
FROM  
    \`brazilian\_marketplace.order\_reviews\` AS r  
JOIN  
    \`brazilian\_marketplace.orders\` AS o ON r.order\_id = o.order\_id  
JOIN  
    \`brazilian\_marketplace.order\_items\` AS i ON o.order\_id = i.order\_id  
JOIN  
    \`brazilian\_marketplace.products\` AS p ON i.product\_id = p.product\_id  
GROUP BY  
    p.product\_category\_name  
ORDER BY  
    avg\_review\_score DESC;

We can see the **categories with the worst reviews**.

Let’s now see if **delivery delay can influence the review score**.

**Are the delivery timeframes of the product respected?**

Delivery Delays and Impact on Reviews
-------------------------------------

Firstly, we see that over 93% of products are delivered within the deadline:

\--Time Delivery  
WITH product\_delivery AS (  
  SELECT  
  order\_estimated\_delivery\_date,  
  order\_delivered\_customer\_date,  
    CASE  
      WHEN DATE\_DIFF(order\_delivered\_customer\_date, order\_estimated\_delivery\_date, DAY) > 0 THEN "Delay"  
      ELSE "In Time"  
  END delivery  
FROM \`brazilian\_marketplace.orders\`  
WHERE order\_delivered\_customer\_date IS NOT NULL  
  AND order\_status = "delivered"  
)  
SELECT  
  delivery,  
  COUNT(delivery) AS order\_number\_status\_delivery,  
  ROUND((COUNT(delivery) / (SELECT COUNT(\*) FROM product\_delivery) \* 100), 2) AS order\_percent\_status\_delivery  
FROM product\_delivery  
GROUP BY delivery;

We also note that **the more the days of delay increase, the lower the review score** becomes:

\--Is the review score related to the days of delay in delivery?  
SELECT  
  orew.review\_score,  
  AVG(DATE\_DIFF(order\_delivered\_customer\_date, order\_estimated\_delivery\_date, DAY)) AS avg\_days\_of\_delay  
FROM \`brazilian\_marketplace.order\_reviews\` AS orew  
INNER JOIN \`brazilian\_marketplace.orders\` AS o  
  ON orew.order\_id = o.order\_id  
WHERE order\_delivered\_customer\_date IS NOT NULL  
  AND order\_status = "delivered"  
  AND DATE\_DIFF(order\_delivered\_customer\_date, order\_estimated\_delivery\_date, DAY) > 0  
GROUP BY orew.review\_score;

Therefore, it is important for Olist to strive to reduce delivery delays in order to increase the overall review score.

Strategic Recommendations
=========================

Inventory Management Suggestions
================================

**Stock Optimization**

Olist could consider **increasing stock for categories with higher sales volumes** (such as bed\_bath\_table or health\_beauty).

**For less frequently sold categories, inventory could be reduced** to minimize storage costs and invest more in the more popular categories.

Pricing Strategy
================

**Price Increase for Popular Products**

Olist could **consider raising prices for more popular categories**, especially if the demand appears to be inelastic and resistant to small price increases.

**Discounts and Promotions**

**For less popular categories**, Olist could use **discounts or promotions** to stimulate demand.

Sales and Promotional Campaign Planning
=======================================

**Targeted Campaigns**

Olist could further **encourage the purchase of popular products with targeted marketing** **campaigns**.

**Bundles and Cross-Promotions**

Olist could explore the possibility of **creating bundle offers or cross-promotions that combine popular products with less sold ones** to increase exposure of the latter.

Customization of Marketing and Sales Strategies
===============================================

Develop personalized marketing campaigns for different segments identified by the RFM analysis:

**High Value Customers**:

Offer personalized deals to **encourage purchases**, perhaps in their favorite categories; activate a “**VIP program**” with dedicated discounts, such as free shipping, early access to sales events.

Communication: They are the right customers who can help in optimizing services/products offered; ask for their feedback through a survey.

**Loyalty Customers:**

Implement a **loyalty program** that rewards customers based on purchase frequency (for example, with a points program or discounts); implement a **referral program** (“invite a friend” in exchange for rewards in the form of discounts).

Communication: Newsletters or product suggestions based on their purchase history.

**Risk Customers:**

**Send a discount to be used shortly** to make an immediate purchase.

Communication: Send them newsletters or activate re-engagement marketing campaigns, highlighting the novelties since their last purchase.

**Promising Customers:**

**Discounts and welcome offers on their next purchase**.

Communication: A welcome email sequence educating the customer about the features and benefits of the marketplace. Suggest the purchase of other related products to their first purchase.

Reducing Churn Rate
===================

It is necessary to **further investigate the churn rate over time**, particularly among new customers or those with low purchase frequency.

Continuous Improvement Based on Customer Feedback
=================================================

**Use customer reviews to identify areas for improvement** in products or services.

Conclusion
==========

In conclusion, **this case study reveals how** **Olist**, through accurate data analysis, **can implement strategies for steady growth while maintaining focus on customer satisfaction and loyalty**.

These insights open new avenues for optimizing operations and for a stronger positioning in the Brazilian e-commerce market.
