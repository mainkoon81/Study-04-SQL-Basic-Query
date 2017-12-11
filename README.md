# Query-SQL
<img src="https://user-images.githubusercontent.com/31917400/33782274-f5759f26-dc4f-11e7-95c5-9bbebc1fbaa7.png" width="600" height="300" />
An entity relationship diagram (ERD) is a key element to understanding how we can pull data from multiple tables. In ERD, PK - a primary key - is a unique column in a particular table. FK – a foreign key – is the column that is (not unique and many of it exist in its table) the primary key (unique) of another table. FK can actually appear in many rows in a table.

---------------------------------------------------------------------------------------------------------------------------------------
## 1. Basic
### SELECT + columns  : 
 - is where you tell the query what columns you want back.
 - the arithmetic operators:  +, -, *, / (f or creating the “derived columns”, a combination of existing columns, then….. AS new_name)… what if…we are thrown an error because of a division by zero? We will go through CASE statements..

### FROM  + table  :
 - is where you tell the query what table you are querying from.

### ORDER BY + columns (desc/asc):
 - is sorting our data by any row
 - > Ex) Write a query that returns the top 5 rows from 'orders' ordered according to newest to oldest, but with the largest 'total_amt_usd' for each date listed first for each date. (tips: You will notice each of these dates shows up as unique because of the **time element**. You might have noticed that the comparisons on **dates** in this way doesn't make a lot of sense because no two dates are the same with this type of granularity!)
``` 
SELECT *
FROM orders
ORDER BY occurred_at DESC, total_amt_usd DESC
LIMIT 5;
```
### LIMIT  : 
 - ... just like checking few rows..like …. head()…tail() ?? 

### WHERE  + conditions  : 
 - subsetting (filtering) out the data based on conditions that must be met.
 - the common operators: <,>,<=,>=,!=,= (for numeric, non-numeric)
 - the LOGICAL operators
   - **AND / BETWEEN** combining operations where all combined conditions must be true.
   - **OR** combining operations where at least one of the combined conditions must be true.
   - **LIKE** for cases when you might not know exactly what you are looking for…so use wildcard - %
   - **IN** for more than one condition.
   - **NOT** used with IN or LIKE to select all of the rows NOT LIKE or NOT IN a certain condition.

### WHERE + column + LIKE + ‘%str%’ : 
 - useful for searching ‘text’ and which is frequently used with '%'(wildcard) 
 - > Imagine yourself as a digital marketing manager. You want to identify web traffic that came via Google pages. Problem is the refer URLs from Google all have the same domain but often have a bunch of extra stuff tacked on. In order to write a filter that will capture all web traffic from Google regardless of the stuffs that’s tacked onto the end, use: LIKE + ”%Google%”  
 - > Ex) Use the accounts table to find all companies whose names start with 'C'.
 - > Ex) Use the accounts table to find…. All companies whose names end with 's'. 
 - > Ex) Use the accounts table to find all companies whose names contain the string 'one' somewhere in the name.
```
SELECT name
FROM accounts
WHERE name LIKE 'C%';
> WHERE name LIKE '%s';
> WHERE name LIKE '%one%';
```
### WHERE + column + IN + ( value, value, value,,,,) : 
 - check many column values for which we want to pull data, but all within the same single query. (tips: NEED to use " for when apostrophe within the text you are attempting to pull, for example Macy's in our work space would be: 'Macy"s')
 - > imagine yourself as a sales manager. You want to see how several rows (jumbled) are performing. You need to filter data based on several, multiple IDs. For example, just want to see Walmart, Apple, Samsung, etc. at the same time.
 - > Ex) Use the accounts table to find the account name, primary_poc, and sales_rep_id for Walmart, Target, and Nordstrom. 
```
SELECT name, primary_poc, sales_rep_id
FROM accounts
WHERE name IN ('Walmart', 'Target', 'Nordstrom');
```
### WHERE + (column + NOT + LIKE (%) or NOT + IN(‘—‘, ‘—‘) ):   
 - it’s an inverse..
 - > imagine yourself as a sales manager. You are considering promoting 2 of top sales reps (sales representative) into management (so their accounts would be taken over by others). You need to figure out how to divvy up all of their accounts among the other sales reps. In deciding who to assign these accounts to, you need to consider which accounts the other sales reps are currently working. So you need to look at all the accounts that are not listed in the query that shows the 2 new managers’ previous accounts. 
 - > Ex) Use the accounts table, find all the companies whose names do not start with 'C' but end with 's'.
 - > Ex) Use the web_events table, to find all information regarding individuals who were contacted via organic or adwords (they are channel) and started their account at any point in 2016 sorted from newest to oldest.
 - > Ex) Use the accounts table, find all the company names that start with a 'C' or 'W', and the primary contact contains 'ana' or 'Ana', but it doesn't contain 'eana'.
``` 
SELECT name
FROM accounts
WHERE (name NOT LIKE ‘C%’) AND (name LIKE ‘%s’);

SELECT *
FROM web_events
WHERE (channel IN ('organic', 'adwords')) AND (occurred_at BETWEEN '2016-01-01' AND '2017-01-01')
ORDER BY occurred_at desc;

SELECT *
FROM accounts
WHERE (name LIKE 'C%' OR name LIKE 'W%') 
AND (primary_poc LIKE '%ana%' OR primary_poc LIKE '%Ana%') 
AND (primary_poc NOT LIKE '%eana%');
```
---------------------------------------------------------------------------------------------------------------------------------------
## 2. Join (inner)
<img src="https://user-images.githubusercontent.com/31917400/33805787-25cf61ae-ddb6-11e7-9a22-982109fd7ba8.png" width="150" height="100" />

> Producing results for which the join condition is matched in both tables.

_Why Would We Want to Split Data Into Separate Tables in the database? 
 - 1)Some columns (they are identifiers though) carry different types of objects. Keeping separate makes things easier to organize. 
 - 2)Allowing queries faster to execute by reducing the amount of data. 
_How to pull data from multiple tables?
 - We need to specify every table a column comes from in the SELECT statement….
 - **FROM** introduces the first “table”. 
 - **JOIN** introduces the second “table”.
 - **ON** tells us how you would like to merge the tables in the FROM and JOIN statements together (relationship, identifier?).
```
SELECT orders.*, accounts.*
> SELECT orders.standard_qty, orders.gloss_qty, orders.poster_qty, accounts.website, accounts.primary_poc
FROM orders
JOIN accounts
ON orders.account_id = accounts.id;
```
 - > Join-Q1. Provide a table for all web_events associated with **account name** of Walmart. There should be three columns - **primary_poc, time of the event, and the channel for each event.** Additionally, you might choose to add a fourth column to assure only Walmart events were chosen.
```
SELECT a.primary_poc, w.occurred_at, w.channel, a.name
FROM web_events w
JOIN accounts a
ON w.account_id = a.id
WHERE a.name = 'Walmart'; 
> WHERE a.name IN (‘Walmart’);
```
 - > Join-Q2. Provide a table that provides the region for each sales_rep along with their associated accounts. Your final table should include three columns: the **region name**, the **sales rep name**, and the **account name**. Sort the accounts alphabetically (A-Z) according to account name.
```
SELECT r.name region, s.name rep, a.name account
> SELECT region.name AS region, sales_reps.name AS rep, accounts.name AS account
FROM sales_reps s
JOIN region r
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
ORDER BY a.name;
```
 - > Join-Q3. Provide the name for each region for every order, as well as the account name and the unit price they paid (total_amt_usd/total) for the order. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. A few accounts have 0 for total, so I divided by (total + 0.01) to assure not dividing by zero.
```
SELECT r.name region, a.name account, o.total_amt_usd/(o.total + 0.01) unit_price
> SELECT region.name region, accounts.name account, orders.total_amt_usd/(orders.total+0.01) unit_price
FROM region r
JOIN sales_reps s
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id;
```
 _inner join : As seen above, only returns rows that appear in both tables. In the venn diagram - overlapping sets.
 _outer join – (left/right/outer) join :  returns the inner join result set, as well as any unmatched rows from either of the two tables being joined (from older brother table). The use cases for a full outer join are very rare. #FROM is older brother, #LEFT JOIN is younger brother. 
```
SELECT c.countryid, c.countryName, s.stateName
FROM Country c
LEFT JOIN State s
ON c.countryid = s.countryid;
```
 - > Join-Q4. Provide a table that provides the region for each sales_rep along with their associated accounts. This time only for the Midwest region. Your final table should include three columns: the **region name**, the **sales rep name**, and the **account name**. Sort the accounts alphabetically (A-Z) according to account name. 
``` 
SELECT region.name region, sales_reps.name rep, accounts.name account
FROM region
JOIN sales_reps
ON region.id = sales_reps.region_id 
JOIN accounts
ON sales_reps.id = accounts.sales_rep_id  
AND region.name = ‘Midwest’
> WHERE region.name = ‘Midwest’
ORDER BY accounts.name
```
 - > Join-Q5. Provide a table that provides the region for each sales_rep along with their associated accounts. This time only for accounts where the sales rep has a last name starting with ‘K’ and in the Midwest region. Your final table should include three columns: the **region name**, the **sales rep name**, and the **account name**. Sort the accounts alphabetically (A-Z) according to account name.
``` 
SELECT region.name region, sales_reps.name rep, accounts.name account
FROM region 
JOIN sales_reps
ON region.id = sales_reps.region_id
JOIN accounts
ON sales_reps.id = accounts.sales_rep_id 
WHERE region.name = 'Midwest' AND sales_reps.name LIKE '% K%'
ORDER BY accounts.name
```
 - > Join-Q6. Provide the name for each region for every order, as well as the account name and the unit price they paid (total_amt_usd/total) for the order. However, you should only provide the results if the standard order quantity exceeds 100 and the poster order quantity exceeds 50. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. Sort for the smallest unit price first. In order to avoid a division by zero error, adding .01 to the denominator here is helpful (total_amt_usd/(total+0.01).
``` 
SELECT region.name region, accounts.name account, orders.total_amt_usd/(orders.total+0.01) unit_price
FROM region 
JOIN sales_reps
ON region.id = sales_reps.region_id
JOIN accounts
ON sales_reps.id = accounts.sales_rep_id 
JOIN orders
ON accounts.id = orders.account_id
WHERE orders.standard_qty > 100 AND orders.poster_qty > 50
ORDER BY unit_price
```
 - > Join-Q7. What are the different channels used by account id 1001? Your final table should have only 2 columns: **account name** and the **different channels**. You can try **SELECT DISTINCT** to narrow down the results to only the unique values.
``` 
SELECT DISTINCT accounts.name account, web_events.channel
FROM accounts
JOIN web_events
ON accounts.id = web_events.account_id
WHERE web_events.account_id = ‘1001’
``` 
 - > Join-Q8. Find all the orders that occurred in 2015. Your final table should have 4 columns: **occurred_at**, **account name**, **order total**, and **order total_amt_usd**.
``` 
SELECT o.occurred_at, a.name, o.total, o.total_amt_usd
FROM accounts a
JOIN orders o
ON a.id = o.account_id
WHERE o.occurred_at BETWEEN ‘01-01-2015’ AND ‘12-31-2015’
ORDER BY o.occurred_at DESC 
```
### Left joins, Right joins 
<img src="https://user-images.githubusercontent.com/31917400/33805855-0ec5196c-ddb7-11e7-9172-f68766d7c62c.png" width="150" height="100" />
<img src="https://user-images.githubusercontent.com/31917400/33805856-10c3126e-ddb7-11e7-98a2-75621ef160d4.png" width="150" height="100" />

> Left joins include unmatched rows from the left table, which is indicated in the “FROM” clause.
```
SELECT column_name(s)
FROM Table_A
LEFT JOIN Table_B 
ON Table_A.column_name = Table_B.column_name;
```
> Right joins are similar to left joins, but include unmatched data from the right table..the one that’s indicated in the JOIN clause.
```
SELECT column_name(s)
FROM Table_A
RIGHT JOIN Table_B 
ON Table_A.column_name = Table_B.column_name;
```
### Full Outter joins, 
<img src="https://user-images.githubusercontent.com/31917400/33805860-15828ce4-ddb7-11e7-90ab-c41f647bd20b.png" width="150" height="100" />
<img src="https://user-images.githubusercontent.com/31917400/33805863-185f62fc-ddb7-11e7-98c1-3bc076cd564c.png" width="150" height="100" />

> In some cases, you might want to include unmatched rows from both tables being joined. You can do this with a full outer join.
 - A common application of this is when joining two tables on a timestamp. Let’s say you’ve got one table containing the number of item 1 sold each day, and another containing the number of item 2 sold. If a certain date (say, January 1, 2018) exists in the left table but not the right, while another date (say, January 2, 2018) exists in the right table but not the left:
   - a left join would drop the row with January 2, 2018 from the result set
   - a right join would drop January 1, 2018 from the result set
The only way to make sure both January 1, 2018 and January 2, 2018 make it into the results is to do a full outer join. A full outer join returns unmatched records in each table with null values for the columns that came from the opposite table.
```
SELECT column_name(s)
FROM Table_A
FULL OUTER JOIN Table_B ON Table_A.column_name = Table_B.column_name;
```
> If you wanted to return unmatched rows only, which is useful for some cases of data assessment, you can isolate them by adding the following line to the end of the query:
```
WHERE Table_A.column_name IS NULL OR Table_B.column_name IS NULL
```


---------------------------------------------------------------------------------------------------------------------------------------
## 3. Aggregation
_How to get some statistics in SQL?
 - count() : How many rows in the column
 - sum() : add all values together in the column
 - min() : the lowest value in the column 
 - max() : the highest value in the column
 - avg() : the average in the column
#### Aggregators operate down *columns, not across rows.
```
SELECT count(accounts.primary_poc)
FROM accounts

SELECT count(*)
FROM accounts
WHERE primary_poc IS NULL
```
#### count() does not consider rows that have NULL values; therefore, this can be useful for quickly identifying which rows have missing data. 
#### aggregators only aggregate vertically - the values of a column. If you want to perform a calculation across rows, you would do this with simple arithmetic (+,-,*,/). Notice, this solution did not use an aggregate. We used '+' instead. 
 - > Agg-Q1. Find the total amount for each individual order that was spent on standard and gloss paper in the orders table. This should give a dollar amount for each order in the table.
```
SELECT standard_amt_usd + gloss_amt_usd AS total_standard_gloss
FROM orders
```
#### Notice, this solution used both an aggregate and our mathematical operators.
 - > Agg-Q2. Though the price/standard_qty paper varies from one order to the next. I would like this ratio across all of the sales made in the orders table.
``` 
SELECT sum(standard_amt_usd) / sum(standard_qty) AS standard_price_per_unit
FROM orders
```
#### Notice that MIN() and MAX() are aggregators that again ignore NULL values. MIN() will return the lowest number, earliest date, or non-numerical value as early in the alphabet as possible. As you might suspect, MAX() does the opposite. 
#### AVG() aggregate function ignores the NULL values in both the numerator and the denominator. If you want to count NULLs as zero, you will need to use SUM() and COUNT().
 - > Agg-Q3. When was the earliest order ever placed? Try performing the same query as in the previous without using an aggregation function.
```
SELECT min(occurred_at)
FROM orders

>SELECT occurred_at
>FROM orders
>ORDER BY occurred_at
>LIMIT 1
```
 - > Agg-Q4. What is the MEDIAN total_usd spent on all orders?
``` 
SELECT *
FROM (SELECT total_amt_usd
      FROM orders
      ORDER BY total_amt_usd
      LIMIT 3457) AS Table1
ORDER BY total_amt_usd DESC
LIMIT 2;

# Since there are 6912 orders - we want the average of the (half=3456) and (half+1=3457) order amounts when ordered. This gives us 2483.16 and 2482.55. This gives the median of 2482.855. SQL didn't even calculate the median for us. The above used a SUBQUERY.
```
_**GROUP BY** clause : to aggregate data **within subsets** of the data. It creates segments that will aggregate independent from one another. It allows us to take sum of the data **limited to some column** rather than across the entire dataset.
```
SELECT sum(standard_qty), sum(gloss_qty), sum(poster_qty) 
FROM orders
#Let’s say..here we want to create a separate set of sums for each ‘account_id’ so if we add ‘account_id’ then…

SELECT account_id, sum(standard_qty), sum(gloss_qty), sum(poster_qty) 
FROM orders
#it returns an error. WHY? Coz..we included the ‘account_id’ column! But this column is not being collapsed like the columns that are being aggregated. We have to be explicit about if we make it into a grouping. 

SELECT account_id, sum(standard_qty), sum(gloss_qty), sum(poster_qty) 
FROM orders
GROUP BY account_id
ORDER BY account_id
#GROUP BY is always go between WHERE and ORDER BY 
```
 - > Agg-Q5. What was the smallest order placed by each account in terms of total usd. Provide only two columns - the **account name** and the **total usd**. Order from smallest dollar amounts to largest.
```
SELECT accounts.name account, min(orders.total_amt_usd) total_usd
FROM accounts
JOIN orders
ON accounts.id = orders.account_id
GROUP BY accounts.name
ORDER BY total_usd 
```
 - > Agg-Q6. Find the number of sales reps in each region. Your final table should have two columns - the **region** and the **number of sales_reps**. Order from fewest reps to most reps.
``` 
SELECT region.name, count(sales_reps.name) n_reps
FROM region
JOIN sales_reps
ON sales_reps.region_id = region.id
GROUP BY region.name
ORDER  BY n_reps
```
#### You can GROUP BY multiple columns at once. This is often useful to aggregate across a number of different segments. The order of columns listed in the ORDER BY clause does make a difference. But the order of column names in your GROUP BY clause doesn’t matter. A reminder here that any column that is not within an aggregation must show up in your GROUP BY statement. If you forget, you will likely get an error.
 - > Agg-Q7. Determine the number of times a particular channel was used in the web_events table for each region. Your final table should have three columns - the **region name**, the **channel**, and the **number of occurrences**. Order your table with the highest number of occurrences first.
``` 
SELECT region.name, web_events.channel, count(web_events.channel) count
FROM region
JOIN sales_reps
ON region.id = sales_reps.region_id
JOIN accounts
ON sales_reps.id = accounts.sales_rep_id
JOIN web_events
ON accounts.id = web_events.account_id
GROUP BY region.name, web_events.channel
ORDER BY count DESC
```
_**SELECT DISTINCT** clause: returning only the unique values of a particular column.
 - > Agg-Q8. Use DISTINCT to test if there are any accounts associated with more than one region. The below two queries have the same number of resulting rows (351), so we know that every account is associated with only one region. If each account was associated with more than one region, the second query should have returned more rows than the first query.
```
SELECT DISTINCT id, name
FROM accounts

SELECT accounts.id AS "account_id", accounts.name AS "account", region.id AS "region_id", region.name AS "region"
FROM accounts
JOIN sales_reps
ON sales_reps.id = accounts.sales_rep_id
JOIN region
ON sales_reps.region_id = region.id
```
 - > Agg-Q9. Have any sales reps worked on more than one account?
``` 
SELECT DISTINCT id, name
FROM sales_reps

SELECT s.id, s.name, COUNT(*) num_accounts
FROM accounts a
JOIN sales_reps s
ON s.id = a.sales_rep_id
GROUP BY s.id, s.name
ORDER BY num_accounts;
```
_**HAVING** clause : to filter a query that has been aggregated….WHERE subsets the returned data based on the logical condition. HAVING is like WHERE, but HAVING works on **logical statements** involving **aggregations**. WHERE appears after FROM, JOIN, ON, but before GROUP BY. HAVING appears after **GROUP BY**, but before ORDER BY clause. (It is only useful when GROUP BY multiple columns?) Essentially, any time you want to perform a WHERE on an element of your query that was created by an aggregate, you need to use HAVING instead of WHERE.  
 - > Agg-Q10. How many of the sales reps have more than 5 accounts that they manage?
``` 
SELECT sales_reps.id, count(*) num_account
FROM accounts
JOIN sales_reps
ON sales_reps.id = accounts.sales_rep_id
GROUP BY sales_reps.id
HAVING count(*) > 5
ORDER BY num_account

#using SUBQUERY, we plug our query in FROM( ) clause. 
>SELECT COUNT(*) AS num_reps
>FROM
(SELECT s.id, count(*) num_accounts
FROM accounts a
JOIN sales_reps s
ON s.id = a.sales_rep_id
GROUP BY s.id
HAVING COUNT(*) > 5
ORDER BY num_accounts) AS table_1
```
 - > Agg-Q11. How many accounts spent more than 30,000 usd total across all orders?
```
SELECT accounts.id, sum(orders.total_amt_usd) total_spent
FROM orders
JOIN accounts
ON orders.account_id = accounts.id
GROUP BY accounts.id 
HAVING sum(orders.total_amt_usd) > 30000
ORDER BY total_spent
```
 - > Agg-Q12. Which accounts used facebook as a channel to contact customers more than 6 times? And which account used facebook most as a channel?
```
SELECT a.id, a.name, w.channel, COUNT(*) use_of_channel
FROM accounts a
JOIN web_events w
ON a.id = w.account_id
GROUP BY a.id, a.name, w.channel
HAVING COUNT(*) > 6 AND w.channel = 'facebook'
ORDER BY use_of_channel;
```
_DATE-column: GROUPing BY a date column is not usually very useful in SQL, as these columns tend to have transaction data down to a second. Keeping date information at such a granular data is both a blessing and a curse, as it gives really precise information (a blessing), but it makes grouping information together directly difficult (a curse). Here we see that dates are stored in year, month, day, hour, minute, second, which helps us in truncating. DATE_TRUNC( ) / DATE_PART( )
 - **DATE_TRUNC( )**: to truncate your date to a particular part of your date-time column. Common trunctions are : year - month - day
 - **DATE_PART( )**: to pull a specific portion of a date, but notice pulling ‘month’ or ‘dow’(dayofweek) means that you are no longer keeping the years in order. Rather you are grouping for certain components regardless of which year they belonged in.
 - > Agg-Q13. Which year did Parch & Posey have the greatest sales in terms of total dollars? Are all years evenly represented by the dataset?
``` 
SELECT DATE_PART('year', occurred_at) ord_year,  SUM(total_amt_usd) total_spent
FROM orders
GROUP BY 1
ORDER BY 2 DESC

#For 2013 and 2017 there is only one month of sales for each of these years (12 for 2013 and 1 for 2017). Therefore, neither of these are evenly represented. Sales have been increasing year over year, with 2016 being the largest sales to date. At this rate, we might expect 2017 to have the largest sales.
```
 - > Agg-Q14. Which month did Parch & Posey have the greatest sales in terms of total number of orders? Are all months evenly represented by the dataset?
``` 
SELECT date_part(‘month’, occurred_at) ord_mon, count(*) total_ord
FROM orders
WHERE occurred_at BETWEEN ‘2014-01-01’ AND ‘2016-12-31’
GROUP BY 1
ORDER BY 2 DESC

# December still has the most sales, but interestingly, November has the second most sales (but not the most dollar sales. To make a fair comparison from one month to another 2017 and 2013 data were removed.
```
 - > Agg-Q15. In which month of which year did Walmart spend the most on gloss paper in terms of dollars?
``` 
SELECT date_trunc(‘month’, orders.occurred_at) ord_y_m, sum(orders.gloss_amt_usd) total_gloss
FROM accounts
JOIN orders
ON accounts.id = orders.account_id
WHERE accounts.name = ’Walmart’
GROUP BY 1
ORDER BY 2 DESC

# Here, selected columns are all aggregations so WHERE clause can work. May 2016 was when Walmart spent the most on gloss paper.  
```
### CASE statement : 
 - *CASE must include the following components: WHEN, THEN, and END. 
 - *ELSE is an optional component to catch cases that didn’t meet any of the other previous CASE conditions.
 - *Just like WHERE, CASE makes any conditional statement using any conditional operator (common or logical) between WHEN and THEN. 
 - *You can include multiple WHEN statements, as well as an ELSE statement again, to deal with any unaddressed conditions.
```
SELECT account_id, CASE WHEN standard_qty = 0 OR standard_qty IS NULL THEN 0 ELSE standard_amt_usd/standard_qty END AS unit_price
FROM orders
```
 - Now the first part of the statement will catch any of those division by zero values that were causing the error, and the other components will compute the division as necessary.
 - Getting the same information using a WHERE clause means only being able to get one set of data from the CASE at a time. As it were, using the WHERE clause only allows to count one condition at a time.
``` 
SELECT CASE WHEN total > 500 THEN ‘Over 500’ ELSE ‘500 or under’ END AS total_grp, count(*) 
FROM orders
GROUP BY 1
```
 - > Agg-Q16. We would like to understand 3 different branches of customers based on the amount associated with their purchases. The top branch includes anyone with a Lifetime Value (total sales of all orders) greater than 200,000 usd. The second branch is between 200,000 and 100,000 usd. The lowest branch is anyone under 100,000 usd. Provide a table that includes the level associated with each account. You should provide the account name, the total sales of all orders for the customer, and the level. Order with the top spending customers listed first.
``` 
SELECT a.name, SUM(total_amt_usd) total_spent, 
     CASE WHEN SUM(total_amt_usd) > 200000 THEN 'top'
     WHEN  SUM(total_amt_usd) > 100000 THEN 'middle'
     ELSE 'low' END AS customer_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id 
GROUP BY a.name
ORDER BY 2 DESC;
```
 - > Agg-Q17. We would now like to perform a similar calculation to the first, but we want to obtain the total amount spent by customers only in 2016 and 2017. Keep the same levels as in the previous question. Order with the top spending customers listed first.
``` 
SELECT a.name, SUM(total_amt_usd) total_spent, 
     CASE WHEN SUM(total_amt_usd) > 200000 THEN 'top'
     WHEN  SUM(total_amt_usd) > 100000 THEN 'middle'
     ELSE 'low' END AS customer_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id
WHERE occurred_at > '2015-12-31' 
GROUP BY 1
ORDER BY 2 DESC;
```
 - > Agg-Q18. We would like to identify top performing sales reps, which are sales reps associated with more than 200 orders. Create a table with the sales rep name, the total number of orders, and a column with top or not depending on if they have more than 200 orders. Place the top sales people first in your final table.
``` 
SELECT s.name, COUNT(*) num_ords,
     CASE WHEN COUNT(*) > 200 THEN 'top'
     ELSE 'not' END AS sales_rep_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id 
JOIN sales_reps s
ON s.id = a.sales_rep_id
GROUP BY s.name
ORDER BY 2 DESC;
```
 - > Agg-Q19. The previous didn't account for the middle, nor the dollar amount associated with the sales. Management decides they want to see these characteristics represented as well. We would like to identify top performing sales reps, which are sales reps associated with more than 200 orders or more than 750000 in total sales. The middle group has any rep with more than 150 orders or 500000 in sales. Create a table with the sales rep name, the total number of orders, total sales across all orders, and a column with top, middle, or low depending on this criteria. Place the top sales people based on dollar amount of sales first in your final table. You might see a few upset sales people by this criteria!
``` 
SELECT s.name, COUNT(*), SUM(o.total_amt_usd) total_spent, 
     CASE WHEN COUNT(*) > 200 OR SUM(o.total_amt_usd) > 750000 THEN 'top'
     WHEN COUNT(*) > 150 OR SUM(o.total_amt_usd) > 500000 THEN 'middle'
     ELSE 'low' END AS sales_rep_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id 
JOIN sales_reps s
ON s.id = a.sales_rep_id
GROUP BY s.name
ORDER BY 3 DESC;
```
---------------------------------------------------------------------------------------------------------------------------------------
## 4. SubQueries
_How to query from the results of another query?
 - *Subqueries
 - *Table Expressions
 - *Persistent Derived Tables
 
_Both subqueries and table expressions are methods for being able to write a query that creates a table, and then write a query that interacts with this newly created table. This is because sometimes the question you are trying to answer doesn't have an answer when working directly with existing tables in database. For example, Q.Find the average number of events for each day for each channel ? 
   - #The quiry A: it provides us the number of events for each day and each channel. 
   - #The quiry B: it averages these values together.
   - #The first query goes in the FROM clause. Must use an alias for the table you nest within the outer query. 
```
SELECT channel, AVG(events) AS average_events
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day, channel, COUNT(*) as events
      FROM web_events 
      GROUP BY 1,2) sub
GROUP BY channel
ORDER BY 2 DESC;
```   
### WITH statement : 
 - *is often called a **Common Table Expression** or **CTE**. Though these expressions serve the exact same purpose as subqueries, they are more common in practice, as they tend to be cleaner for a future reader to follow the logic. We can run it as a completely separate query and then right back into the database as its own table. 

Let's try this again using a WITH statement.
 - This is the part we put in the WITH statement. Notice, we are aliasing the table as events below. Now, we can use this newly created events table as if it is any other table in our database:
```
WITH table_1 AS (
          SELECT DATE_TRUNC('day',occurred_at) AS day, channel, COUNT(*) as events
          FROM web_events 
          GROUP BY 1,2)

SELECT channel, AVG(events) AS average_events
FROM table_1
GROUP BY channel
ORDER BY 2 DESC;
```
#### Subquery in a conditional statement :  
 - Subquery also can be used in WHERE, JOIN clauses or WHEN portion in CASE statement. If you are only returning a single **value**(for example, **the day** of the first order ever occurred), you might use that value in a logical statement like WHERE, HAVING, or even SELECT - the value could be nested within a CASE statement. Note that you should not include an alias when you write a subquery in a conditional statement. This is because the subquery is treated as an individual value (or multiple values in the IN case) rather than as a table. Also, notice the query here compared a single value. If we returned an entire column, IN would need to be used to perform a logical argument. If we are returning an entire table, then we must use an alias for the table, and perform additional logic on the entire table.
 - > Sub-Q1. What was the month/year combo for the first order placed? Use ‘date_trunc( )’ to pull level information about the first order placed in the ‘orders’ table. Then use the result to find the orders that took place in the same month and year as the first order, and then pull the average for each type of paper qty in this month. 
```
SELECT date_trunc(‘month’, occurred_at)
FROM orders
ORDER BY occurred_at 
LIMIT 1  
#..Which is…using 'min()' function, we can get the inner query.
>SELECT date_trunc(‘month’, MIN(occurred_at))
>FROM orders

#then
SELECT avg(standard_qty) avg_std, avg(gloss_qty) avg_gls, avg(poster_qty) avg_pos
FROM orders
WHERE date_trunc(‘month’, occurred_at) = 
(SELECT date_trunc(‘month’, min(occurred_at))
FROM orders)
```
<img src="https://user-images.githubusercontent.com/31917400/33800965-64ca56d8-dd44-11e7-83f4-06a1fbaf3ed4.jpg" width="400" height="40" />

 - > Sub-Q2. Provide the name of the sales_rep in each **region** with the largest amount of **total_amt_usd** sales.
 
*First, find the total_amt_usd totals associated with each sales rep, and region in which they were located. 
```
SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY 1,2
ORDER BY 3 DESC;
```
*Next, pull the max for each region, and then we can use this to pull those rows in our final result.
```
SELECT region_name, MAX(total_amt) total_amt
FROM
(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY 1, 2) inner_sub
GROUP BY 1;
```
*Lastly, JOIN of these two tables, where the region and amount match.
```
SELECT t1.rep_name, t1.region_name, t1.total_amt
FROM
(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY 1,2) t1
JOIN 
(SELECT region_name, MAX(total_amt) total_amt
FROM(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
 FROM sales_reps s
 JOIN accounts a
 ON a.sales_rep_id = s.id
 JOIN orders o
 ON o.account_id = a.id
 JOIN region r
 ON r.id = s.region_id
 GROUP BY 1, 2) inner_sub
GROUP BY 1) t2
ON t1.region_name = t2.region_name AND t1.total_amt = t2.total_amt;
```
<img src="https://user-images.githubusercontent.com/31917400/33800565-3271f9ac-dd3a-11e7-85b0-45c0c3da2b50.jpg" width="400" height="150" />

 - > Sub-Q3. For the region with the largest sales total_amt_usd, how many total orders were placed? 
 
*First, pull the total_amt_usd for each region.
```
SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY r.name;
```
*Then we want just want the region with the max amount from this table. There are two ways I considered getting this amount. One was to pull the max using a subquery. Another way is to order descending and just pull the top value.
```
SELECT MAX(total_amt)
FROM 
(SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY r.name) sub;
```
*Finally, we want to pull the total orders for the region with this amount:
```
SELECT r.name, SUM(o.total) total_orders
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY r.name
HAVING SUM(o.total_amt_usd) = 
(SELECT MAX(total_amt)
 FROM
 (SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
  FROM sales_reps s
  JOIN accounts a
  ON a.sales_rep_id = s.id
  JOIN orders o
  ON o.account_id = a.id
  JOIN region r
  ON r.id = s.region_id
  GROUP BY r.name) sub);
```
<img src="https://user-images.githubusercontent.com/31917400/33800636-b0097416-dd3b-11e7-8d5a-bc939293e30c.jpg" width="400" height="40" />

 - > Sub-Q4. For the name of the account that purchased the most (in total of their lifetime as a customer) standard_qty paper, how many accounts still had more in total purchases?

*First, we want to find the account that had the most standard_qty paper. The query here pulls that account, as well as the total amount:
```
SELECT a.name account_name, SUM(o.standard_qty) total_std, SUM(o.total) total
FROM accounts a
JOIN orders o
ON o.account_id = a.id
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```
*Now, I want to use this to pull all the accounts with more total sales:
```
SELECT a.name
FROM orders o
JOIN accounts a
ON a.id = o.account_id
GROUP BY 1
HAVING SUM(o.total) 
> (SELECT total
FROM 
(SELECT a.name act_name, SUM(o.standard_qty) tot_std, SUM(o.total) total
  FROM accounts a
  JOIN orders o
  ON o.account_id = a.id
  GROUP BY 1
  ORDER BY 2 DESC
  LIMIT 1) sub);
```
*This is now a list of all the accounts with more total orders. We can get the count with just another simple subquery.
```
SELECT COUNT(*)
FROM 
(SELECT a.name
FROM orders o
JOIN accounts a
ON a.id = o.account_id
GROUP BY 1
HAVING SUM(o.total) 
> (SELECT total 
FROM 
(SELECT a.name act_name, SUM(o.standard_qty) tot_std, SUM(o.total) total
  FROM accounts a
  JOIN orders o
  ON o.account_id = a.id
  GROUP BY 1
  ORDER BY 2 DESC
  LIMIT 1) inner_tab)) counter_tab;
```
<img src="https://user-images.githubusercontent.com/31917400/33800682-f5f766e4-dd3c-11e7-9f21-14c9d9877820.jpg" width="400" height="40" />

 - > Sub-Q5. For the customer that spent the most (in total of their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?
 
*we first want to pull the customer with the most spent in lifetime value.
```
SELECT a.id, a.name, SUM(o.total_amt_usd) tot_spent
FROM orders o
JOIN accounts a
ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY 3 DESC
LIMIT 1;
```
*Now, we want to look at the number of events on each channel this company had, which we can match with just the id.
```
SELECT a.name, w.channel, COUNT(*)
FROM accounts a
JOIN web_events w
ON a.id = w.account_id AND a.id =  (SELECT id
                     FROM (SELECT a.id, a.name, SUM(o.total_amt_usd) tot_spent
                           FROM orders o
                           JOIN accounts a
                           ON a.id = o.account_id
                           GROUP BY a.id, a.name
                           ORDER BY 3 DESC
                           LIMIT 1) inner_table)
GROUP BY 1, 2
ORDER BY 3 DESC;
```
<img src="https://user-images.githubusercontent.com/31917400/33800702-a134772c-dd3d-11e7-9ec3-b49360ae712a.jpg" width="400" height="150" />

 - > Sub-Q6. What is the lifetime average amount spent in terms of total_amt_usd for only the companies that spent more than the average of all orders.
 
*First, we want to pull the average of all accounts in terms of total_amt_usd:
```
SELECT AVG(o.total_amt_usd) avg_all
FROM orders o
JOIN accounts a
ON a.id = o.account_id;
```
*Then, we want to only pull the accounts with more than this average amount.
```
SELECT o.account_id, AVG(o.total_amt_usd)
FROM orders o
GROUP BY 1
HAVING AVG(o.total_amt_usd) > (SELECT AVG(o.total_amt_usd) avg_all
                               FROM orders o
                               JOIN accounts a
                               ON a.id = o.account_id);
```
*Finally, we just want the average of these values.
```
SELECT AVG(avg_amt)
FROM (SELECT o.account_id, AVG(o.total_amt_usd) avg_amt
    FROM orders o
    GROUP BY 1
    HAVING AVG(o.total_amt_usd) > (SELECT AVG(o.total_amt_usd) avg_all
                               FROM orders o
                               JOIN accounts a
                               ON a.id = o.account_id)) temp_table;
```
<img src="https://user-images.githubusercontent.com/31917400/33800735-6d043f72-dd3e-11e7-8058-0c3bfaaebabe.jpg" width="400" height="40" />















































