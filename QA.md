What are your risk areas? Identify and describe them.

With a more deeper dive into the data, the more constraint in the business rule displays. 
1. There are numerous data entry rows for the same fullvistorID where each contain some information. Consequently, when query for the results and perform QA, the results are off due to duplicate and inconsistent data entry.
2. Data lost within the pool of NULL Values

The issue can be addressed by breaking down of the larger entities into sub-entities. Each of the sub-entities carried unique insights with a unique identifier and  smaller number of alias. 
QA Process:
Describe your QA process and include the SQL queries used to execute it.
1. Create sales_analytic from all_sessions and analytics with distinct fullvisitorID  and valid revenue. Table contained aliases that use to diagnose area of risk from data source and non-duplicate data.

i. Create Revenue Table from analytics

```SQL
CREATE TABLE revenue AS
	SELECT Distinct (fullvisitorid) AS fullvisitorID
		, SUM(units_sold) AS Total_units_sold
		, SUM (revenue) AS Total_revenue
	FROM Analytics
	WHERE revenue IS NOT NULL
	GROUP BY fullvisitorid
	ORDER BY Total_revenue DESC
```

ii. Create sales_analytics table to remove duplicate fullvisitorID
```SQL
CREATE TABLE sale_analytics AS
	SELECT DISTINCT(fullvisitorID)
		, country
		, city
		, type
		, channelgrouping
		, total_revenue 
		, total_units_sold
	FROM revenue 
	JOIN all_sessions
	USING (fullvisitorID)
;
```

