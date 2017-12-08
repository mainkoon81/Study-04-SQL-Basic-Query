# Study-SQL

### 1. Basic
<img src="https://user-images.githubusercontent.com/31917400/33782274-f5759f26-dc4f-11e7-95c5-9bbebc1fbaa7.png" width="600" height="300" />

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

WHERE name LIKE '%s';

WHERE name LIKE '%one%';
```
### WHERE + column + IN + ( value, value, value,,,,) : 
 - check many column values for which we want to pull data, but all within the same single query. (tips: NEED to use " for when apostrophe within the text you are attempting to pull, for example Macy's in our work space would be: 'Macy"s')
 - > imagine yourself as a sales manager. You want to see how several rows (jumbled) are performing. You need to filter data based on several, multiple IDs. For example, just want to see Walmart, Apple, Samsung, etc. at the same time.
 - > Ex) Use the accounts table to find the account name, primary_poc, and sales_rep_id for Walmart, Target, and Nordstrom. 
```
>SELECT name, primary_poc, sales_rep_id
>FROM accounts
>WHERE name IN ('Walmart', 'Target', 'Nordstrom');
```
### WHERE + (column + NOT  + LIKE (%) or IN(‘—‘, ‘—‘) ):   
it’s an inverse..
_imagine yourself as a sales manager. You are considering promoting 2 of top sales reps (sales representative) into management (so their accounts would be taken over by others). You need to figure out how to divvy up all of their accounts among the other sales reps. In deciding who to assign these accounts to, you need to consider which accounts the other sales reps are currently working. So you need to look at all the accounts that are not listed in the query that shows the 2 new managers’ previous accounts. 

Ex) Use the accounts table, find all the companies whose names do not start with 'C' but end with 's'.
>SELECT name
>FROM accounts
>WHERE (name NOT LIKE ‘C%’) AND (name LIKE ‘%s’) ;

Ex) Use the web_events table, to find all information regarding individuals who were contacted via organic or adwords and started their account at any point in 2016 sorted from newest to oldest.
>SELECT *
>FROM web_events
>WHERE (channel IN ('organic', 'adwords')) AND (occurred_at BETWEEN '2016-01-01' AND '2017-01-01')
>ORDER BY occurred_at desc ;

Ex) Use the accounts table, find all the company names that start with a 'C' or 'W', and the primary contact contains 'ana' or 'Ana', but it doesn't contain 'eana'.
>SELECT *
>FROM accounts
>WHERE (name LIKE 'C%' OR name LIKE 'W%') 
AND (primary_poc LIKE '%ana%' OR primary_poc LIKE '%Ana%') 
AND (primary_poc NOT LIKE '%eana%')  ;











