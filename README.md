# Analysis.flipsidecrypto
Projects completed from flipsidecrypto.xyz data analysis tasks.
 
```
SELECT
	date_trunc('day',block_timestamp) as DAY,
	sum(deposit_amount) as ust_deposited
from anchor.deposits
where block_timestamp >= current_date - 30
group by day
```
