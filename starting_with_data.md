## Question 1: What is the average time spent on the site by purchasing customers and non purchasing customers? 

SQL Queries:
```
SELECT CASE
		WHEN units_sold >= 1 THEN 'purchasers'
		ELSE 'non-purchasers'
		END as type_of_traffic, 
		avg(time_on_site) as time
FROM analytics
GROUP BY 1;
``` 

Answer:  Using this query, to no surprise, customers who end up purchasing products after visiting the site spend nearly twice as much time (20min 20s) then those who don't purchase (11min 30s).



## Question 2: Considering how potential customers access our site, what method of accessing our website is attributed with the most sales? 


SQL Queries:
```
SELECT channel_grouping, count(units_sold) as total_units_sold
FROM analytics
WHERE units_sold >= 1
Group by channel_grouping;
```

Answer: Using this query, we can see that the customers who wre referred to the site ended up account for the largest share of paying customers. 



## Question 3:  Looking at the customers referred to our site, how many of them have been converted to purhasing customers? 

SQL Queries:
```
SELECT channel_grouping, COUNT(*) * 100 / (SELECT COUNT(*) FROM analytics WHERE channel_grouping = 'Referral')
FROM analytics
WHERE channel_grouping = 'Referral' AND units_sold IS NOT NULL
GROUP BY 1;
```
Answer: After using this query, we can see that referred customers are converted to paying customers at high clip (~4.8%).



