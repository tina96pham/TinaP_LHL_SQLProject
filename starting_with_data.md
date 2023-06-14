Question 1: Percentage of visitor that make a purchased

SQL Queries:

```SQL
WITH cte_transactions AS
	(SELECT DISTINCT (fullvisitorID)
			, transactions
		FROm all_sessions
		WHERE timeonsite>0 )
SELECT 
		SUM(transactions)/(Count(*))*100 AS PCT_purchase
FROm cte_transactions
;
```
Answer: 0.72%



Question 2: Total revenue from each refering sites 

SQL Queries:

```SQL
SELECT 
		DISTINCT(al.channelgrouping) AS refering_site
		, COUNT(DISTINCT al.fullvisitorID) AS unique_visitor_count
		, SUM(an.revenue) AS Total_revenue
FROM all_sessions AS al
JOIN analytics AS an
    ON al.fullvisitorID=an.fullvisitorID
GROUP BY al.channelgrouping
ORDER BY unique_visitor_count DESC
``` 
Answer:
<src img= Imagedirectory/Sd2.png>



Question 3: Brand with total unit sold and channelgrouping

SQL Queries:
```SQL
SELECT 
		al.brand AS brand
		, al.channelgrouping AS sales_channel
		, COUNT(DISTINCT al.fullvisitorID) AS unique_visitor_count
		, SUM(an.revenue) AS Total_revenue
FROM all_sessions AS al
JOIN analytics AS an
    ON al.fullvisitorID=an.fullvisitorID
GROUP BY al.brand, al.channelgrouping
HAVING SUM(an.revenue) IS NOT NULL
ORDER BY Total_revenue DESC
;
```

Answer: Google through Organic Search with highest total revenue of $12,685



Question 4: City and Country with the most revenue

SQL Queries:

```SQL
SELECT  
		city
		, country
		, SUM(total_revenue) AS totalRevenue
FROM sale_analytics
GROUP BY city, country
ORDER BY totalRevenue DESC
LIMIT 10
```

Answer: USA



Question 5: 

SQL Queries:

Answer:
