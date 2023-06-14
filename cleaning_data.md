What issues will you address by cleaning the data?

Revenue and sales of product from different product category and brand base on different region



Queries:
Below, provide the SQL queries you used to clean your data.
1. Changing the price by divided by 1,000,0000

```SQL
UPDATE all_sessions
SET productPrice= CAST(productprice/1000000.0 AS DECIMAL(10, 2))
;

UPDATE all_sessions
SET totaltransactionrevenue= CAST(totaltransactionrevenue/1000000.0 AS DECIMAL(18, 2))
;

UPDATE analytics
SET unitprice= CAST(unitprice/1000000.0 AS DECIMAL(10, 2))
;

UPDATE analytics
SET revenue= CAST(revenue/1000000.0 AS DECIMAL(18, 2))
;
````
2. Format date and time

```SQL
ALTER TABLE all_sessions
ALTER COLUMN time
TYPE VARCHAR
USING LPAD(time::VARCHAR, 6, '0');

UPDATE public.all_sessions
SET time = SUBSTR(time, 1, 2) || ':'||  SUBSTR(time, 3, 2) || ':'||  SUBSTR(time, 5, 2);

UPDATE public.all_sessions
SET time = TIME '00:00:00' + 
           INTERVAL '1 hour' * CAST(SUBSTR(time, 1, 2) AS INTEGER) +
           INTERVAL '1 minute' * CAST(SUBSTR(time, 4, 2) AS INTEGER) +
           INTERVAL '1 second' * CAST(SUBSTR(time, 7, 2) AS INTEGER)
;
```

3. Clean up city and country

```SQL
UPDATE all_sessions
SET country = CASE
        WHEN country LIKE '%not set%'
		THEN 'n/a'
        ELSE CAST(country AS VARCHAR)
		END
;
UPDATE all_sessions
	SET city= CASE
        	WHEN city IN ('not available in demo dataset', '(not set)') 
				THEN CAST(country AS VARCHAR)
        	ELSE CAST(city AS VARCHAR)
		END
;
```
4. Organized product base on brand

```SQL
UPDATE all_sessions
SET brand= CASE
		WHEN v2productname LIKE '%Google%' OR v2productcategory LIKE '%Google%'
			THEN 'Google'
		WHEN v2productname LIKE '%Android%' OR v2productcategory LIKE '%Android%'
			THEN 'Android'
		WHEN v2productname LIKE '%Waze%' OR v2productcategory LIKE '%Waze%'
			THEN 'Waze'
		WHEN v2productname LIKE '%Nest%' OR v2productcategory LIKE '%Nest%'
			THEN 'Nest'
		WHEN v2productname LIKE '%YouTube%' OR v2productcategory LIKE '%YouTube%'
			THEN 'YouTube'
		ELSE 'Generic'
	END 
;
```
5. Create a new table using all_sessions to contain product info

```SQL
CREATE TABLE session_products AS
    SELECT productsku
            , v2productname AS productname
            , v2productcategory AS category
            , brand
            , productquantity
            , productprice
    FROM all_sessions
,
```

6. Categorize product in all sessions

```SQL
UPDATE session_products
SET category = CASE
        WHEN productname LIKE '%Men%'
	            OR productname LIKE '%Women%' 
	            OR productname LIKE '%Toddler%'
                Or productname LIKE '%Onesie%'
				OR productname LIKE '%Tee%'
				OR productname LIKE '%Cap%'
				OR productname LIKE '%Hat%'
	            OR category LIKE '%Apparel%'
                OR productname LIKE '%hirt%'
                OR productname LIKE '%Hood%'
		    THEN 'Apparel'
        ELSE CAST(category AS VARCHAR)
END 
;
UPDATE session_products
SET category = CASE
        WHEN productname LIKE '%Bottle%'
                OR productname LIKE '%Mug%'
                OR productname LIKE '%Tumbler%'
                OR category LIKE '%Drinkware%'
            THEN 'Drinkware'
        WHEN productname LIKE '%Lip Balm%'
                OR productname LIKE '%Mat%'
				OR productname LIKE '%Sanitizer%'
                OR productname LIKE '%Selfie%'
                OR category LIKE '%Lifestyle%'
                OR category LIKE '%Sports & Fitness%'
            THEN 'Lifestyle'
        WHEN productname LIKE '%Bag%'
                OR productname LIKE '%Rucksack%'
				OR productname LIKE '%Backpack'
				Or productname LIKE '%Pouch'
                OR productname LIKE '%Tote'
                OR category LIKE '%Bag%'
            THEN 'Bag'
        WHEN productname LIKE '%Nest%'
            OR category LIKE '%Nest%'
            THEN 'Nest'
    ELSE CAST(category AS VARCHAR)
END
;
UPDATE session_products
SET category = CASE
        WHEN productname LIKE '%Decal%'
	            OR productname LIKE '%Sticker%' 
	            OR productname LIKE '%Windup%'
                OR productname LIKE '%Tag%'
                OR productname LIKE '%Mount%'
		        OR productname LIKE '%Umbrella%'
		        OR productname LIKE '%Holder%'
                OR productname LIKE '%Device Stand%'
                OR productname LIKE '%Sunglasses%'
                OR category LIKE '%Accessories%'
		    THEN 'Accessories'
    ELSE CAST(category AS VARCHAR)
END
;
UPDATE session_products
SET category = CASE
        WHEN productname LIKE '%Book%'
	            OR productname LIKE '%Pen%' 
                OR productname LIKE '%book%' 
	            OR productname LIKE '%Journal%'
	            OR category LIKE '%Office%'
		    THEN 'Office'
    ELSE CAST(category AS VARCHAR)
END
;
UPDATE session_products
SET category = CASE
        WHEN productname LIKE '%Light%'
                OR productname LIKE '%Device Stand%'
                OR productname LIKE '%Bank%'
				OR productname LIKE '%light%'
				OR productname LIKE '%Bluetooth%'
                OR productname LIKE '%Charger%'
	            OR category LIKE '%ELectronic%'
		    THEN 'Electronics'
    ELSE CAST(category AS VARCHAR)
END
;
UPDATE session_products
SET category = CASE
        WHEN category LIKE '%Home%'
                OR category LIKE '%not set%'
		    THEN 'MISC'
    ELSE CAST(category AS VARCHAR)
    END
;
```

7. Create Revenue Table from analytics

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

8. Create sales_analytics table to remove duplicate fullvisitorID
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