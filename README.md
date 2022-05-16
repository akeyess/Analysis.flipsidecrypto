# Analysis.flipsidecrypto
Repository for completed data analysis projects from flipsidecrypto.xyz. Problem solving SQL queries are broken down here, while the results of the query can be found linked in each project description. Linked is a dashboard containing visualizations, results tables, and brief summaries.

These projects are conducted through *flipsidecrypto.xyz*, which provides a plethora of data tasks related to blockchain technology, as well as programs for SQL queries, data visuals, and dashboard creation.

# #project1- [Terra] UST on Anchor Protocol 
 
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

## *What was the volume of UST borrowed from Anchor daily from February 1 - February 28, 2022?*

```
SELECT
	date_trunc('day',block_timestamp) as day,
	sum(deposit_amount) as ust_deposited
from anchor.deposits
where block_timestamp >= '2022-02-01'
and block_timestamp <= '2022-02-28'
group by day
```
- same function as previous except move from borrows table to deposits table

Visual comparison of the two variable: UST deposits and borrows on Anchor, depicts multiple relationship throughout the month. Around Feb 7 both charts depicted a major downtrennd until both began to head the opposite direction starting on Feb 12. A spike in dollar amount of both variables was found on Feb 23, leading to an overall downtrend of both amounts until the end of the month.

[Data visualization and results table from SQL query are available here](https://app.flipsidecrypto.com/dashboard/terra-2-ust-deposits-on-anchor-protocol-sb3qpz)

# #project2- [Ethereum] Ethereum_Core Table: Swaps 
 
## *Find swaps in the USDC-WETH Sushi Pool from the past 7 days?*

```
SELECT
   *
FROM
   ETHEREUM_CORE.FACT_EVENT_LOGS
WHERE
   block_timestamp >= CURRENT_DATE - 6
   AND contract_address = LOWER('0x397FF1542f962076d0BFE58eA045FfA2d347ACa0') 
-- this is the USDC-WETH SushiSwap Pool Address
   AND event_name IN ('Swap')
```
- Used 'select *' to preview all of the columns
- This particular SQL project focuses on the new 'ethereum_core' tables
- 'where' block timestamp is equal to or less than 6 days ago, totaling to 7 days including present date
- 'and' contract address where the event occured, in this case a swap. We will want to filter this for the USDC-WETH pool address above.
- 'and' the name of the event emiited by the contract. Contracts can have many event types for the same contract address, so we'll want to filter this only for swap events

SAMPLE OUTPUT for a value of 'EVENTS_INPUTS' column (came in JSON format)- describes the transaction in this case as token0 out and token1 in. With 5355072563 token0 out, and 1830000000000000000 token1 in.
```
{  "amount0In": 0,  
"amount0Out": 5355072563,
"amount1In": 1830000000000000000,
"amount1Out": 0,
"sender": "0xd9e1ce17f2641f24ae83637ab66a2cca9c378b9f",
"to": "0x5f09ef7d8a3f9b63c5227fd6aabe0b3822aa6fdb"}
```

## *Which token is amount0 and which is amount1?*
## *What is the true value of tokens for each token in the transaction? (not actually recieving 1830000000000000000 tokens, it is in scientific notation)

Flipside has a DIM_DEX_LIQUIDITY_POOLS table, which contains details about different liquidity pools on Ethereum. We will query this table for the USDC-WETH pool address to find details on the pool.

```
SELECT
   pool_name,
   pool_address,
   token0,
   token1
FROM
   ETHEREUM_CORE.DIM_DEX_LIQUIDITY_POOLS
WHERE
   pool_address = LOWER('0x397FF1542f962076d0BFE58eA045FfA2d347ACa0')
```

## **OUTPUT**
**POOL_NAME**  |          **POOL_ADDRESS**          |  **TOKEN0**  |  **TOKEN1**
:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:
USDC-WETH SLP | 0x397ff1542f962076d0bfe58ea045ffa2d347aca0 |  0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48  |  0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

- displayed/'selected' pool name, token address, 'token0' and 'token1'
- 'from' table containing info on liquidity pools
- 'where' pool address equals USDC-WETH pool address

Above query was for demonstration, now we need to reference the dim_contracts table to find details on the respective tokens, we can use a Common Table Expression (CTE) to filter the contracts table for token0 and token1

```
WITH pools AS (
   SELECT
       pool_name,
       pool_address,
       token0,
       token1
   FROM
       ETHEREUM_CORE.DIM_DEX_LIQUIDITY_POOLS
   WHERE
       pool_address = LOWER('0x397FF1542f962076d0BFE58eA045FfA2d347ACa0')
)
SELECT
   address,
   symbol,
   decimals
FROM
   ETHEREUM_CORE.DIM_CONTRACTS
WHERE
   address = (
       SELECT
           LOWER(token1)
       FROM
           pools
   )
   OR address = (
       SELECT
           LOWER(token0)
       FROM
           pools
   )
```
- 'select' pools with the parameters set by the where clause, bringing the exact token0 token1 pooladdress and poolname from the dataset
- 'select' address.symbol,decimals
- 'from' contract table
- 'where' address equals token0 or token1

## **OUTPUT**

**POOL_NAME**  |          **POOL_ADDRESS**          |  **TOKEN0**  |  **TOKEN1**
:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:
USDC-WETH SLP | 0x397ff1542f962076d0bfe58ea045ffa2d347aca0 |  0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48  |  0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

Dispplays the same output as previously seen. Now with the address, symbol, and decimals for both token0 and token1, we can put this info into one query building onto the CTE from earlier

```
WITH pools AS (
SELECT 
pool_name, 
pool_address, 
token0, 
token1 
FROM ETHEREUM_CORE.DIM_DEX_LIQUIDITY_POOLS 
WHERE pool_address = LOWER('0x397FF1542f962076d0BFE58eA045FfA2d347ACa0')),
decimals AS (
   SELECT
       address,
       symbol,
       decimals
   FROM
       ETHEREUM_CORE.DIM_CONTRACTS
   WHERE
       address = (
           SELECT
               LOWER(token1)
           FROM
               pools
       )
       OR address = (
           SELECT
               LOWER(token0)
           FROM
               pools
       )
)
SELECT
   pool_name,
   pool_address,
   token0,
   token1,
   token0.symbol AS token0symbol,
   token1.symbol AS token1symbol,
   token0.decimals AS token0decimals,
   token1.decimals AS token1decimals
FROM
   pools
   LEFT JOIN decimals AS token0
   ON token0.address = token0
   LEFT JOIN decimals AS token1
   ON token1.address = token1
```

### **OUTPUT**

**POOL_NAME**  |          **POOL_ADDRESS**          |  **TOKEN0**  |  **TOKEN1**  |  **TOKEN0SYMBOL**  |  **TOKEN1SYMBOL**  |  **TOKEN0DECIMALS**  |  **TOKEN1DECIMALS**
:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:
USDC-WETH SLP | 0x397ff1542f962076d0bfe58ea045ffa2d347aca0 |  0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48  |  0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2  |  USDC  |  WETH  |  6  |  18

We now can see that token0 is USDC and uses 6 decimal transformation off the original value, while token1 is WETH and is 18 transformed decimal places.
