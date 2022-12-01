### What are your risk areas? Identify and describe them.

One risk area would be that of incomplete data both found in individual rows and columns all together. Similarly, repeated data values or columns can be equally dangerous as they may inflate the strength or relevance of insights drawn from the data.  


## QA Process:
### Describe your QA process and include the SQL queries used to execute it.

droping/deleting columns with nearly all if not all null, poses no benefit to business
```
ALTER TABLE all_sessions
DROP COLUMN search_keyword;

ALTER TABLE all_sessions
DROP COLUMN product_refund_amount;

ALTER TABLE all_sessions
DROP COLUMN item_revenue;

ALTER TABLE all_sessions
DROP COLUMN item_quantity;
```

When calculating values that involve applying functions(-,+,etc.) between columns, write seperate queries for each individual value to validate that original query achieves intended value. 

EX. FOR: 
```
SELECT channel_grouping, COUNT(*) * 100 / (SELECT COUNT(*) FROM analytics WHERE channel_grouping = 'Referral')
FROM analytics
WHERE channel_grouping = 'Referral' AND units_sold IS NOT NULL
GROUP BY 1;
``` 

Validate with: 
```					  
SELECT COUNT(units_sold) 
FROM analytics
WHERE channel_grouping = 'Referral'
	
						  
						  
SELECT channel_grouping, COUNT(units_sold)
FROM analytics
WHERE channel_grouping = 'Referral' and units_sold >= 1 
Group BY channel_grouping
```

Count total, distinct, and duplicate values within a column before and after dropping duplicate values to make sure that queries run were successful. 