Limit records the right way
###########################
:date: 2010-08-03 05:24
:author: Laurence Noton
:category: Development

If you want to bring back only 100 rows, or rows 10-20 from a SQL query
(i.e pagination etc) the correct way is not just to use rownum. Rownum
is assigned before any order by clause and thus you can get unexpected
results depending on how Oracle is configured to manage its data. Oracle
by default is configured to insert new rows into empty blocks that could
be available from a delete statement that has been executed. Therefore
new rows appear on disk between old rows!

To restrict rows correctly use the OVER keyword to ensure that
row\_number is applied based on your order by.

SELECT \* FROM

(

SELECT my.\*, Row\_Number() OVER (ORDER BY name) myrow FROM mytable mt

)

WHERE myrow <= 20;


