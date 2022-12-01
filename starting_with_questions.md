Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**
```
SELECT country, city, SUM(total_transaction_revenue) as total_city_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT null 
GROUP BY country, city
ORDER BY total_city_revenue DESC;
```

-- As countries
```
SELECT country, SUM(total_transaction_revenue) as total_country_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT null 
GROUP BY country
ORDER BY total_country_revenue DESC;
```

-- As cities
```
SELECT city, SUM(total_transaction_revenue) as total_city_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT null AND city != 'not available in demo dataset'
GROUP BY city
ORDER BY total_city_revenue DESC;
```

Answer: Other than the aggregated non-descript cities of the United states ($6092.56), San Francisco, USA has the highest level of transaction revenues at $1564.




**Question 2: What is the average number of products ordered from visitors in each city and country?**

```
WITH newtable AS (
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
``` 


Answer: The avg number of products ordered from the highest ordering countries, cities are Riyadh, Saudi Arabia and Brno, Czechia at 319. 





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```
SELECT country, v2_product_category, COUNT(distinct(v2_product_category)) as product_category_count
FROM all_sessions
WHERE v2_product_category != 'not set' and country != '(not set)'
GROUP BY country, v2_product_category
ORDER BY 1,2 DESC;
```
```
SELECT city, v2_product_category, COUNT(distinct(v2_product_category)) as product_category_count
FROM all_sessions
WHERE v2_product_category != 'not set' and city != '(not set)'
GROUP BY city, v2_product_category
ORDER BY 1,2 DESC;
```
Answer: It seems that across most cities and countries, the most popular category of products is apparel.





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

```
SELECT COUNT(v2_product_name), v2_product_name, country 
FROM all_sessions
WHERE transactions is not null
GROUP BY 2,3
ORDER BY count DESC;
```
```
SELECT COUNT(v2_product_name), v2_product_name, city
FROM all_sessions
WHERE transactions is not null and city != 'not available in demo dataset' and city != '(not set)'
GROUP BY 2,3
ORDER BY count DESC;
```

Answer: Similarly to question #3, a pattern emerges in that the Nest security camera is the most sold product





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

-- As countries
```
SELECT country, SUM(total_transaction_revenue) as total_country_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT null 
GROUP BY country
ORDER BY total_country_revenue DESC;
```
-- As cities

```
SELECT city, SUM(total_transaction_revenue) as total_city_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT null AND city != 'not available in demo dataset'
GROUP BY city
ORDER BY total_city_revenue DESC;
```

Answer:
Using the same queries in question #1, we can see that the country responsible for generating the most revenue is the USA, and the single city would be San Francisco.






