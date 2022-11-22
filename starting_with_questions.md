Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries: SELECT country, city, SUM(CAST('total_transaction_revenue'/1000000 as real)) as total_transaction_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL 
AND city != 'not available in demo dataset'
GROUP BY country, city 
ORDER BY total_transaction_revenue DESC;



Answer: San Francisco, USA has the highest level of transaction revenues at $1564.




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries: WITH newtable AS (
SELECT country, CASE WHEN city = 'not available in demo dataset' THEN country
																ELSE city 
																END as 
city, total_ordered
	FROM all_sessions al 
	JOIN sales_by_sku sbs 
	ON al.product_sku = sbs.product_sku)
SELECT country, city, CAST (AVG(total_ordered) as real)
FROM newtable 
GROUP BY country, city 
ORDER BY avg DESC;



Answer: The avg number of products ordered from the highest ordering countries, cities are Riyadh, Saudi Arabia and Brno, Czechia at 319. 





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







