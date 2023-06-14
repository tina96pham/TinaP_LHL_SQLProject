# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
Analyze the ecommerce dataset to gain insight for improvement in revenue by addressing:
1. What is the statistic on customer who make purchases?
2. What is the top selling products?
3. How product get sell?
4. Region with most sales 

## Process
### Clean data
1. The initial cost from the database is ambiguous. 
    --> Cost in the database etablished by divided by 1,000,0000.
2. Date and time are not format properly when import to pgAdmin. 
    -->Transform data to HH:MM:SS
3. City and country were not enter properly. 
    --> Country with NULL input  are set to 'n/a'. 
    --> Cast city with no proper entry has with their corespondent country.
4. A new column are made and product are organized according to brand that offered product.
    --> Filtering brand name from product name and product category. 
    -->If, not match then group with 'Generic
5. Product category are not appropriate for analysis with no business rule or standard for the database. 
    --> Cleaning performed to cut down to 12 category for analysis by filtering keywords from product name and product category.

### Analysis
1. Calculate percentage of visitor that made a purchase by calculate the sum of transactions make by all unique visitor and divided by the number of distinct customer where they spend time on site.
2. A CTE created with the join between the analytics and the all_sessions table to analyze the trend in revenue between different channelsgrouping, city, country and brand.
### QA
Create sales_analytic table with information from unique fullvisitorID from all_sessions and analytics to diagnose the affect of duplicate entry. Although the result with the number are off due to duplicate data in the bigger datasource, the trend observed in the data will provide the assertion evaluation

## Results
1. 0.72% visitor made a purchase on site
2. Google has the top selling product
3. Organic search is the top channel for sales generate the highest revenue for Google
4. Cities in The United States generate the most revenue.
## Challenges 
One of the big challenge for this project is the ambiguous business rule for the data set. As the result, this generate the lack of understanding in the context of values in the data set which lead to the limitation in etablishing relationship between different entities.

## Future Goals
Additional time for the project would allow a more understanding of the data which can lead to a more refine cleaning with better connection between the entities. More time would be given to understand the disrepancy in data in analytic table  and all session table especcially in revenue and totaltransaction revenue, which will provide more insight for better analysis. There are various area in the data set with 'NULL' value which can be tackle with comprehensive knowledge on the dataset.
Question for future analysis: ' How brand awareness contribute to revenue?'. This will provide insights and trend in revenue which influence the decision making in cutting down products offer that did poorly which can help restructure and upkeep with the database and data integrity.
