What issues will you address by cleaning the data?

1)Distinguishing between relevant and irrelevant data (ex. Sales_by_sku and 0 values) 

2)Removing Incomplete Data

3)Changing Data Types

4)Removing Irrelevant Data

5)Changing Inappropriate Data Values 


Queries:
Below, provide the SQL queries you used to clean your data.

-- Create all sessions table 
``` 
CREATE TABLE public.all_sessions
(
    "full_visitor_id " numeric(20),
    channel_grouping character varying(30),
    "time" integer,
    country character varying(30),
    city character varying(60),
    total_transaction_revenue numeric(20, 2),
    transactions integer,
    time_on_site interval,
    page_views integer,
    session_quality_dim integer,
    date date,
    visit_id integer,
    page character varying(10),
    product_refund_amount numeric(20, 2),
    product_quantity integer,
    product_price numeric(20, 2),
    product_revenue numeric(20, 2),
    product_sku character varying(30),
    v2_product_name character varying(100),
    v2_product_category character varying(100),
    product_variant character varying(100),
    currency_code character varying(5),
    item_quantity integer,
    item_revenue numeric(20, 2),
    transaction_revenue numeric(20, 2),
    transaction_id character varying(60),
    page_title character varying(1000),
    search_keyword character varying(60),
    page_path_level_1 character varying(60),
    ecommerce_action_type integer,
    ecommerce_action_step integer,
    ecommerce_action_option character varying(30)
);
```
-- import data from all sesssions csv to all_sessions table 
```
COPY all_sessions
FROM 'C:\temp\all_sessions.csv' 
DELIMITER ',' 
CSV HEADER;
```
-- check if data successfully imported into table
```
Select * 
From all_sessions;
```
-- Delete rows with too many null values as these rows have too much incomplete data to properly analyze
```
DELETE FROM all_sessions 
WHERE total_transaction_revenue ISNULL 
AND transactions ISNULL
AND session_quality_dim ISNULL
AND product_revenue ISNULL
AND item_quantity ISNULL 
AND item_revenue ISNULL
AND transaction_revenue ISNULL
AND transaction_id ISNULL;
```
-- Similarly, drop/delete columns with nearly all if not all null, poses no benefit to business
```
ALTER TABLE all_sessions
DROP COLUMN search_keyword;
```
```
ALTER TABLE all_sessions
DROP COLUMN product_refund_amount;
```
```
ALTER TABLE all_sessions
DROP COLUMN item_revenue;
```
```
ALTER TABLE all_sessions
DROP COLUMN item_quantity;
```

-- Convert/ Cast prices by dividing by 1000000 in order to get appropriate values

--product revenue
```
SELECT CAST(product_revenue/1000000 as real) AS product_revenue_revised
FROM all_sessions;

UPDATE all_sessions
SET product_revenue = product_revenue/1000000;
```
--product_price
```
SELECT CAST(product_price/1000000 as real) AS product_price_revised
FROM all_sessions;

UPDATE all_sessions
SET product_price = product_price/1000000;
```

--total_transaction_revenue
```
SELECT CAST(total_transaction_revenue/1000000 as real) AS total_transaction_revenue_revised
FROM all_sessions;

UPDATE all_sessions
SET total_transaction_revenue = total_transaction_revenue/1000000;
```

--transaction_revenue
```
SELECT CAST(transaction_revenue/1000000 as real) AS transaction_revenue_revised
FROM all_sessions;

UPDATE all_sessions
SET transaction_revenue = transaction_revenue/1000000;
```

-- Changing all null currrency codes to USD 
```
SELECT CASE WHEN currency_code is null THEN 'USD'
			ELSE currency_code 
			END
			AS currency_code 
FROM all_sessions;
```

-- visit id is recorded in unix timestamp, will leave as is for the moment as this might be used by the business as an internal identifier


--Create analytics table 
```
CREATE TABLE public.analytics
(
    visit_number integer,
    "visit_id " numeric(20),
    visit_start_time numeric(20),
    date date,
    full_visitor_id numeric(20),
    user_id integer,
    channel_grouping character varying(30),
    social_engagement_type character varying(50),
    units_sold integer,
    page_views integer,
    time_on_site interval,
    bounces integer,
    revenue numeric(20, 2),
    unit_price numeric(20, 2)
);
```
-- Import data from csv file into analytics table
```
COPY analytics
FROM 'C:\temp\analytics.csv' 
DELIMITER ',' 
CSV HEADER;
```
-- Verify that data has been imported successfully
```
SELECT * 
FROM analytics;
```
-- Delete rows with too many null values to properly analyze
```
DELETE FROM analytics
WHERE user_id ISNULL
AND units_sold ISNULL
AND time_on_site ISNULL
AND revenue ISNULL;
```
-- Delete user_id column as it only contains null values 
```
ALTER TABLE analytics
DROP COLUMN user_id;
```
-- Repeat the prior for bounces column for same reason
```
ALTER TABLE analytics
DROP COLUMN bounces;
```

-- Much like the all_sessions table, the revenue and unit price columns will need to be adjusted to show appropiate values

-- On revenue 
```
SELECT CAST(revenue/1000000 as real) AS revenue_revised
FROM analytics;

UPDATE analytics
SET revenue = revenue/1000000;
```
-- On unit_price
```
SELECT CAST(unit_price/1000000 as real) AS unit_price_revised
FROM analytics;

UPDATE analytics
SET unit_price = unit_price/1000000;
```
-- In this table visit_id = visit_start_time, since we know that these are unix timestamps, we'll leave visit_id as an identifier and change visit_start_time to a more legible form of timestamp 
```
UPDATE analytics
SET visit_start_time = to_timestamp(visit_start_time);

UPDATE analytics
SET visit_start_time = to_char(visit_start_time);
```

-- Create products table
```
CREATE TABLE public.products
(
    "SKU" character varying(30),
    name character varying(100),
    ordered_quantity integer,
    stock_level integer,
    restocking_lead_time integer,
    sentiment_score numeric(5, 2),
    sentiment_magnitude numeric(5, 2),
    PRIMARY KEY ("SKU")
);
```
-- Import data from csv into products table
```
COPY products
FROM 'C:\temp\products.csv' 
DELIMITER ',' 
CSV HEADER;
```

-- Verify that data has been successfully imported into table
```
SELECT * 
FROM products;
```
-- Remove all rows with ordered quantity and stock = 0, as these skus maybe discontinued or never offered in the first place
```
DELETE FROM products
WHERE ordered_quantity = 0 
AND stock_level = 0;
```

-- Create sales_by_sku table
```
CREATE TABLE public.sales_by_sku
(
    product_sku character varying(30),
    total_ordered integer,
    PRIMARY KEY (product_sku)
);
```
-- Import data from csv file into sales_by_sku table
```
COPY sales_by_sku
FROM 'C:\temp\sales_by_sku.csv' 
DELIMITER ',' 
CSV HEADER;
```
-- Verify that data has been successfully imported
```
SELECT * 
FROM sales_by_sku;
```
-- Thought about removing the rows with sku’s that have 0 units ordered however this information may prove to be beneficial for the business to see why these sku’s have not sold

-- Create sales_report table 
```
CREATE TABLE public.sales_report
(
    product_sku character varying(30),
    total_ordered integer,
    name character varying(100),
    stock_level integer,
    restocking_lead_time integer,
    sentiment_score numeric(4, 2),
    sentiment_magnitude numeric(4, 2),
    ratio numeric(5, 3),
    PRIMARY KEY (product_sku)
);
```
-- Import data from csv file into sales_report table
```
COPY sales_report
FROM 'C:\temp\sales_report.csv' 
DELIMITER ',' 
CSV HEADER;
```
-- Verify that data has been successfully imported
```
SELECT *
FROM sales_report;
```
-- remove all rows with total_ordered and stock = 0 (as its a sales report, this columns wouldn’t provide much value to the business) 
```
DELETE FROM sales_report
WHERE total_ordered = 0 
AND stock_level = 0;
```