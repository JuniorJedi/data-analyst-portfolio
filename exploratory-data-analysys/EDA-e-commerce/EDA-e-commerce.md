Exploratory Data Analysis for E-commerce
===========================================

Welcome to the vibrant world of Brazilian **e-commerce**, where Olist stands out as an innovative and dynamic entity.

This case study delves into the intricate workings of Olist, a Brazilian marketplace that bridges small businesses across Brazil with a vast network of online sales channels. 


## Company

Welcome to the vibrant world of Brazilian e-commerce, where Olist stands out as an innovative and dynamic entity.

This case study delves into the intricate workings of Olist, a Brazilian marketplace that bridges small businesses across Brazil with a vast network of online sales channels.

Olist is a Brazilian marketplace that acts as a powerful link between small businesses spread throughout Brazil and a vast network of online sales channels. With a single contract, these sellers can sell their products through the Olist Store, benefiting from simplified logistic management thanks to Olist's logistic partners.
More information is available on Olist's website: www.olist.com.

This case study delves into a public Brazilian dataset, made freely available under a Creative Commons license on the Kaggle platform.
The real data is anonymized and concerns orders made at the Olist Store. The dataset includes details on 100,000 orders placed between 2016 and 2018. This information allows for the analysis of orders from multiple perspectives.

** Here I will address exploratory data analysis (EDA). **
For the complete case study, including recommendations and proposed strategies, please see [Data Analysis and Strategies for E-commerce](https://github.com/JuniorJedi/data-analyst-portfolio/blob/d774c49c9797cd7d846ed56b3db8d936c30a42af/data%20analysis%20and%20strategies%20for%20e-commerce/README.md)

## Database Structure

The **Olist database is composed of different datasets**:

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://i.imgur.com/HRhd2Y0.png">
  <source media="(prefers-color-scheme: light)" srcset="https://i.imgur.com/HRhd2Y0.png">
  <img alt="Shows datasets from Olist" src="[https://user-images.githubusercontent.com/25423296/163456779-a8556205-d0a5-45e2-ac17-42d089e3c3f8.png](https://i.imgur.com/HRhd2Y0.png)">
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
  <source media="(prefers-color-scheme: dark)" srcset="https://drive.google.com/file/d/175erkphnycP1Du3rkACNbSTE8j3WOtS-/view?usp=drive_link">
  <source media="(prefers-color-scheme: light)" srcset="https://drive.google.com/file/d/175erkphnycP1Du3rkACNbSTE8j3WOtS-/view?usp=drive_link">
  <img alt="Shows datasets from Olist" src="[https://user-images.githubusercontent.com/25423296/163456779-a8556205-d0a5-45e2-ac17-42d089e3c3f8.png]([https://i.imgur.com/HRhd2Y0.png](https://drive.google.com/file/d/175erkphnycP1Du3rkACNbSTE8j3WOtS-/view?usp=drive_link))">
</picture>

Created with [QuickDBD](http://quickdbd/)

Our database also includes the “product\_category\_name\_translation” dataset, which provides English translations for various product categories.

To make accessing this information more efficient and reduce database complexity, I have chosen to integrate the English translations directly into the “products” table.

This step eliminates the need for a separate table for translations, thus reducing redundancy and simplifying future database queries.

