# Analysis.flipsidecrypto
Repository for completed data analysis projects from flipsidecrypto.xyz. Problem solving SQL queries are broken down here, while the results of the query can be found linked in each project description. Linked is a dashboard containing visualizations, results tables, and brief summaries.

# #project1a- [Terra] UST Deposits on Anchor Protocol 
 
## *How much UST was deposited into Anchor daily in the past 30 days?*


```
SELECT
	date_trunc('day',block_timestamp) as DAY,
	sum(deposit_amount) as ust_deposited
from anchor.deposits
where block_timestamp >= current_date - 30
group by day
```
- used select to select/display certain columns of the table
- 'date_trunc' function truncates (shortens) the timestamp to a specific value we want
	- example truncate it to 'day', all timestamp lose their hour, minutes, + seconds value 
	- ‘2022-01-01 12:00PM’ truncated to ‘2022-01-01 00:00’
	- benefit of trunc function is in aggregating values, like doing a sum
	- 'as day' at the to name new column of truncated values
- 'sum(deposit_amount)' sums all rows from the column called deposit amount (amount of UST deposited)
- querying from the 'anchor.deposits' table 
- where the timestamp is within the past 30 days
- added a 'group by' because "whenever we have an aggregate, like the sum function, we need to tell the query which column we are ‘grouping it’ by"


# #project1b- [Terra] UST Borrows on Anchor Protocol 
 
## *What was the volume of UST borrowed from Anchor daily from February 1 - February 28, 2022?*

```
SELECT
	date_trunc('day',block_timestamp) as DAY,
	sum(amount) as ust_borrowed
from anchor.borrows
where block_timestamp >= '2022-02-01'
and block_timestamp <= '2022-02-28'
group by day
```
- used select and from functions in the same manner as the previous project, replaced deposit with borrow
- where the block is greater than or equal to Feb 1st and less than or equal to Feb 28
- added group by for the same reason as previous query

[Data visualization and results table from SQL query are available here](https://app.flipsidecrypto.com/dashboard/terra-2-ust-deposits-on-anchor-protocol-sb3qpz)
