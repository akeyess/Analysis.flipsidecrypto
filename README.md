# Analysis.flipsidecrypto
Projects completed from flipsidecrypto.xyz data analysis tasks.

# #project1- [Terra] 2. UST Deposits on Anchor Protocol 
 
## How much UST was deposited into Anchor in the past 30 days?


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
