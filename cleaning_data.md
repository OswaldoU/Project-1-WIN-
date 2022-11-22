What issues will you address by cleaning the data?
1)Distinguishing between relevant and irrelevant data (ex. Sales_by_sku and 0 values) 
2)Removing Incomplete Data
3)Changing Data Types
4)Removing Irrelevant Data
5)Changing Inappropriate Data Values 


Queries:
Below, provide the SQL queries you used to clean your data.


DELETE FROM sales_report
WHERE total_ordered = 0 
AND stock_level = 0;

DELETE FROM products
WHERE ordered_quantity = 0 
AND stock_level = 0;


DELETE FROM products
WHERE sentiment_score ISNULL
AND sentiment_ magnitude ISNULL;

DELETE FROM analytics
WHERE user_id ISNULL
AND units_sold ISNULL
AND time_on_site ISNULL
AND revenue ISNULL;


ALTER TABLE analytics
ALTER COLUMN unit_price TYPE NUMERIC
USING unit_price::numeric;


ALTER TABLE analytics
	DROP COLUMN bounces;


ALTER TABLE analytics
	DROP COLUMN user_id;


UPDATE analytics
SET unit_price = unit_price * 0.000001;

DELETE FROM all_sessions 
WHERE total_transaction_revenue ISNULL 
AND transactions ISNULL
AND session_quality_dim ISNULL
AND product_revenue ISNULL
AND item_quantity ISNULL 
AND item_revenue ISNULL
AND transaction_revenue ISNULL
AND transaction_id ISNULL;

ALTER TABLE all_sessions
DROP COLUMN search_keyword;

ALTER TABLE all_sessions
DROP COLUMN product_refund_amount;

ALTER TABLE all_sessions
DROP COLUMN item_revenue;

ALTER TABLE all_sessions
DROP COLUMN item_quantity;


