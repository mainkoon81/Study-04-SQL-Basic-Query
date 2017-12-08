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
 - > Ex) Write a query that returns the top 5 rows from 'orders' ordered according to newest to oldest, but with the largest 'total_amt_usd' for each date listed first for each date. (You will notice each of these dates shows up as unique because of the **time element**. You might have noticed that the comparisons on **dates** in this way doesn't make a lot of sense because no two dates are the same with this type of granularity!)
``` 
>SELECT *
>FROM orders
>ORDER BY occurred_at DESC, total_amt_usd DESC
>LIMIT 5 ;
```
### LIMIT  : 
 - ... just like checking few rows..like …. head()…tail() ?? 












