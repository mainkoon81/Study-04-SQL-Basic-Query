# Study-SQL
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
## 2. Join
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
 - > Join-Q2. Provide a table that provides the region for each sales_rep along with their associated accounts. Your final table should include three columns: the region name, the sales rep name, and the account name. Sort the accounts alphabetically (A-Z) according to account name.
```
>SELECT r.name region, s.name rep, a.name account
>FROM sales_reps s
>JOIN region r
>ON s.region_id = r.id
>JOIN accounts a
>ON a.sales_rep_id = s.id
>ORDER BY a.name;

>SELECT region.name AS region, sales_reps.name AS rep, accounts.name AS account
>FROM region
>JOIN sales_reps
>ON region.id = sales_reps.region_id 
>JOIN accounts
>ON sales_reps.id = accounts.sales_rep_id
>ORDER BY accounts.name






Q. Provide the name for each region for every order, as well as the account name and the unit price they paid (total_amt_usd/total) for the order. Your final table should have 3 columns: region name, account name, and unit price. A few accounts have 0 for total, so I divided by (total + 0.01) to assure not dividing by zero.

>SELECT r.name region, a.name account, o.total_amt_usd/(o.total + 0.01) unit_price
>FROM region r
>JOIN sales_reps s
>ON s.region_id = r.id
>JOIN accounts a
>ON a.sales_rep_id = s.id
>JOIN orders o
>ON o.account_id = a.id;

>SELECT region.name region, accounts.name account, orders.total_amt_usd/(orders.total+0.01) unit_price
>FROM region
>JOIN sales_reps
>ON  sales_reps.region_id = region.id
>JOIN accounts
>ON accounts.sales_rep_id = sales_reps.id 
>JOIN orders
>ON orders.account_id = accounts.id

## inner join : As seen above, only returns rows that appear in both tables. In the venn diagram, overlapping sets.
## outer join – (left/right/outer) join :  returns the inner join result set, as well as any unmatched rows from either of the two tables being joined (from older brother table). The use cases for a full outer join are very rare. #FROM is older brother, #LEFT JOIN is younger brother. 

>SELECT c.countryid, c.countryName, s.stateName
>FROM Country c
>LEFT JOIN State s
>ON c.countryid = s.countryid;

Q1. Provide a table that provides the region for each sales_rep along with their associated accounts. This time only for the Midwest region. Your final table should include three columns: the region name, the sales rep name, and the account name. Sort the accounts alphabetically (A-Z) according to account name. 
>SELECT region.name region, sales_reps.name rep, accounts.name account
>FROM region
>JOIN sales_reps
>ON region.id = sales_reps.region_id 
>JOIN accounts
>ON sales_reps.id = accounts.sales_rep_id  (or AND region.name = ‘Midwest’)
(or >WHERE region.name = ‘Midwest’)
>ORDER BY accounts.name

Q3. Provide a table that provides the region for each sales_rep along with their associated accounts. This time only for accounts where the sales rep has a last name starting with ‘K’ and in the Midwest region. Your final table should include three columns: the region name, the sales rep name, and the account name. Sort the accounts alphabetically (A-Z) according to account name.
>SELECT region.name region, sales_reps.name rep, accounts.name account
>FROM region 
>JOIN sales_reps
>ON region.id = sales_reps.region_id
>JOIN accounts
>ON sales_reps.id = accounts.sales_rep_id 
>WHERE region.name = 'Midwest' AND sales_reps.name LIKE '% K%'
>ORDER BY accounts.name

Q5. Provide the name for each region for every order, as well as the account name and the unit price they paid (total_amt_usd/total) for the order. However, you should only provide the results if the standard order quantity exceeds 100 and the poster order quantity exceeds 50. Your final table should have 3 columns: region name, account name, and unit price. Sort for the smallest unit price first. In order to avoid a division by zero error, adding .01 to the denominator here is helpful (total_amt_usd/(total+0.01).
>SELECT region.name region, accounts.name account, orders.total_amt_usd/(orders.total+0.01) unit_price
>FROM region 
>JOIN sales_reps
>ON region.id = sales_reps.region_id
>JOIN accounts
>ON sales_reps.id = accounts.sales_rep_id 
>JOIN orders
>ON accounts.id = orders.account_id
>WHERE orders.standard_qty > 100 AND orders.poster_qty > 50
>ORDER BY unit_price

Q7. What are the different channels used by account id 1001? Your final table should have only 2 columns: account name and the different channels. You can try SELECT DISTINCT to narrow down the results to only the unique values.
>SELECT DISTINCT accounts.name account, web_events.channel
>FROM accounts
>JOIN web_events
>ON accounts.id = web_events.account_id
>WHERE web_events.account_id = ‘1001’
 
Q8. Find all the orders that occurred in 2015. Your final table should have 4 columns: occurred_at, account name, order total, and order total_amt_usd.
>SELECT o.occurred_at, a.name, o.total, o.total_amt_usd
>FROM accounts a
>JOIN orders o
>ON a.id = o.account_id
>WHERE o.occurred_at BETWEEN ‘01-01-2015’ AND ‘12-31-2015’
>ORDER BY o.occurred_at DESC 


















































