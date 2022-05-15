# Analysis.flipsidecrypto
Projects completed from flipsidecrypto.xyz data analysis tasks.
 
 
How much UST was deposited into Anchor in the past 30 days?


```
SELECT
	date_trunc('day',block_timestamp) as DAY,
	sum(deposit_amount) as ust_deposited
from anchor.deposits
where block_timestamp >= current_date - 30
group by day
```
