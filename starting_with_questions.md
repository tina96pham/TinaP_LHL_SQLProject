Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```SQL
SELECT  city
		, country
		, SUM(totalTransactionRevenue) AS totaltransactionrevenue
FROM allsession_clean
GROUP BY city, country
ORDER BY totaltransactionrevenue DESC
LIMIT 10
;
```

Answer:
<img src= Imagedirectory/sq1.png>




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT city
		, country
		, AVG(productquantity) AS Averageorderedquantity
FROM (SELECT *
	 	FROM all_sessions
	 WHERE productquantity IS NOT NULL) AS Valid_Data
GROUP BY city, country
ORDER BY Averageorderedquantity DESC
;
```


Answer:
<img src= Imagedirectory/sq2.png>




**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT
    al.country as country
    , al.city as city
    , p.category as productcategory,
    , SUM(an.units_sold) as totalUnitsSold
FROM all_sessions al
JOIN analytics an 
    ON al.visitId = an.visitId
JOIN session_products  p 
    ON al.productSKU = p.productSKU
GROUP BY country, city, category
HAVING SUM(an.units_sold) IS NOT NULL
ORDER BY totalUnitsSold DESC
LIMIT 10
;
```


Answer:
<img src= Imagedirectory/sq3.png>




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

```SQL
SELECT DISTINCT(p.brand)
		, al.city
		, al.country
		, SUM(an.units_sold) as totalUnitsSold
		, RANK() OVER (Order BY SUM(an.units_sold) DESC) as ranking
FROM all_sessions al
JOIN analytics an 
    ON al.visitId = an.visitId
JOIN session_products  p 
    ON al.productSKU = p.productSKU
WHERE transactions is NOT NULL
GROUP BY  DISTINCT(p.brand), city, country
ORDER BY totalUnitsSold DESC
LIMIT 10
;
```

Answer:
<img src= Imagedirectory/sq4.png>




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```SQL
SELECT SUM(an.revenue) AS revenue
		, al.currencycode
		, al.city
		, al.country
FROM all_sessions al
JOIN analytics an 
	USING (fullvisitorid)
GROUP BY city , country, al.currencycode
Having SUM(an.revenue) IS NOT NULL
ORDER BY revenue DESC
LIMIT 10
;
```


Answer:
<img src= Imagedirectory/sq5.png>






