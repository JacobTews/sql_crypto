# O'Reilly SQL Training with Danny Ma
Topic: Cryptocurrency price/sales data (CTEs, window functions, table joins)

## 1. What are the `market_date`, `price`, `volume`, and `price_rank` values for the days with the top 5 highest price values for each ticker in the trading.prices table?

    * The `price_rank` column is the ranking for price values for each ticker with rank = 1 for the highest value.
    * Return the output for Bitcoin followed by Ethereum, each in price rank order.

```sql
WITH ranked_crypto AS(
  SELECT
    ticker
    , market_date
    , price
    , volume
    , RANK() OVER(
        PARTITION BY ticker
        ORDER BY price DESC
      ) AS price_rank
  FROM trading.prices
)

SELECT
  ticker
  , market_date
  , price
  , volume
  , price_rank
FROM ranked_crypto
WHERE price_rank <= 5
ORDER BY ticker, price_rank
;
```


2. Calculate a 7 day rolling average for the price and volume columns in the trading.prices table for each ticker.

    * Return only the first 10 days of August, 2021.

```sql
WITH rolling_cte AS (
  SELECT
      ticker
    , market_date
    , price
    , AVG(price) OVER(
      PARTITION BY ticker
      ORDER BY market_date
      ROWS BETWEEN 7 PRECEDING AND CURRENT ROW
    ) AS moving_avg_price
    , CASE
        WHEN RIGHT(volume, 1) = 'K'
          THEN LEFT(volume, LENGTH(volume) - 1)::NUMERIC * 1e3
        WHEN RIGHT(volume, 1) = 'M'
          THEN LEFT(volume, LENGTH(volume) - 1)::NUMERIC * 1e6
        ELSE 0
      END AS cast_vol
  FROM trading.prices
)

SELECT
  ticker
  , market_date
  , price
  , moving_avg_price
  , cast_vol AS volume
  , AVG(cast_vol) OVER(
    PARTITION BY ticker
    ORDER BY market_date
    ROWS BETWEEN 7 PRECEDING AND CURRENT ROW
  ) AS moving_avg_volume
FROM rolling_cte
WHERE market_date BETWEEN '2021-08-01' AND '2021-08-10'
ORDER BY
  ticker
  , market_date
;
```

3. Calculate the monthly cumulative volume traded for each ticker in 2020

    * Sort the output by ticker in chronological order with the month_start as the first day of each month.

```sql
-- transform string volume to numeric, calculate monthly totals
WITH cast_vol AS (
  SELECT
    ticker
    , DATE_TRUNC('MONTH', market_date) AS month_start
    , SUM(
      CASE
        WHEN RIGHT(volume, 1) = 'K'
            THEN LEFT(volume, LENGTH(volume) - 1)::NUMERIC * 1e3
        WHEN RIGHT(volume, 1) = 'M'
            THEN LEFT(volume, LENGTH(volume) - 1)::NUMERIC * 1e6
        ELSE 0
      END
      ) AS monthly_vol
      
  FROM trading.prices
  WHERE EXTRACT('YEAR' FROM market_date) = '2020'
  GROUP BY ticker, month_start
)
-- calculate the cumulative monthly volume
, cume_sum AS (
  SELECT
    ticker
    , month_start
    , SUM(monthly_vol) OVER (
      PARTITION BY ticker
      ORDER BY month_start
      RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_monthly_volume
  FROM cast_vol
)
-- final output:
SELECT * FROM cume_sum
ORDER BY ticker, month_start
;
```

4. Calculate the daily percentage change in volume for each ticker in the trading.prices table

    * Percentage change can be calculated as (current - previous) / previous
    * Multiply the percentage by 100 and round the value to 2 decimal places
    * Return data for the first 10 days of August 2021

```sql
-- First, convert the volume to numeric with conditional logic
WITH num_vol_cte AS (
  SELECT
    ticker
    , market_date
    , CASE
        WHEN RIGHT(volume, 1) = 'K'
            THEN LEFT(volume, LENGTH(volume) - 1)::NUMERIC * 1e3
        WHEN RIGHT(volume, 1) = 'M'
            THEN LEFT(volume, LENGTH(volume) - 1)::NUMERIC * 1e6
        ELSE 0
      END AS volume
  FROM trading.prices
)
-- Then calculate the one-day lag
, lag_cte AS (
  SELECT
    ticker
    , market_date
    , volume
    , LAG(volume, 1) OVER(
      PARTITION BY ticker
      ORDER BY market_date
    ) AS previous_volume
  FROM num_vol_cte
)
-- Lastly, calculate the daily percentage change and return final resultant table
SELECT
  ticker
  , market_date
  , volume
  , previous_volume
  , ROUND(
    (volume - previous_volume)::NUMERIC / previous_volume * 100
    , 2
  ) AS daily_change
FROM lag_cte
WHERE market_date BETWEEN '2021-08-01' AND '2021-08-10'
ORDER BY ticker, market_date
;
```

### Cryptocurrency price/sales/people data (JOINs)

1. Which top 3 mentors have the most Bitcoin quantity?

    * Use INNER JOIN
    * Return the first_name of the mentors and sort the output from highest to lowest total_quantity

```sql
WITH quant_cte AS (
  SELECT
    ticker
    , member_id
    , CASE
      WHEN txn_type = 'SELL'
        THEN -quantity::NUMERIC
      ELSE quantity::NUMERIC
    END AS sales
  FROM trading.transactions
)
SELECT
  m.first_name
  , SUM(sales) AS total_quantity
FROM quant_cte
INNER JOIN trading.members AS m
  USING(member_id)
WHERE ticker = 'BTC'
GROUP BY first_name
ORDER BY total_quantity DESC
LIMIT 3
;
```

2. Which market_date values which have fewer than 5 transactions?

    * Use LEFT JOIN
    * Sort the output in reverse chronological order.

```sql
WITH count_cte AS (
  SELECT
    p.market_date AS market_date
    , COUNT(t.txn_id) AS transaction_count
  FROM trading.prices AS p
  LEFT JOIN trading.transactions AS t
    ON p.market_date = t.txn_date
    AND p.ticker = t.ticker
  GROUP BY p.market_date
)
SELECT
  market_date
  , transaction_count
FROM count_cte
WHERE transaction_count < 5
ORDER BY market_date DESC
;
```

3. For this question, we will generate a single table output which solves a multi-part problem about the dollar cost average of BTC purchases.

    * Use multiple table joins
    
    1. What is the dollar cost average (btc_dca) for all Bitcoin purchases by region for each calendar year?

        * Create a column called year_start and use the start of the calendar year
        * The dollar cost average calculation is btc_dca = SUM(quantity x price) / SUM(quantity)

    2. What is the rank of each region each year?

        * Use this btc_dca value to generate a dca_ranking column for each year
        * The region with the lowest btc_dca each year has a rank of 1

    3. What is the dollar cost average yearly percentage change for each region?

        * Calculate the yearly percentage change in DCA for each region to 2 decimal places
        * This calculation is (current - previous) / previous
    
    * Finally, order the output by region and year_start columns.

```sql
-- First, calculate dollar cost average
-- Note: region is from members table
WITH cte_dca AS (
  SELECT
    DATE_TRUNC('YEAR', t.txn_date) AS year_start
    , m.region
    , SUM(t.quantity * p.price) / SUM(t.quantity) AS btc_dca
  FROM trading.transactions AS t
  INNER JOIN trading.prices AS p
    ON t.txn_date = p.market_date
    AND t.ticker = p.ticker
  INNER JOIN trading.members AS m
    ON t.member_id = m.member_id
  WHERE t.ticker = 'BTC'
    AND t.txn_type = 'BUY'
  GROUP BY m.region, year_start
)
-- Then use the calculated DCAs to rank the regions each year
, cte_dca_rank AS (
  SELECT
    year_start
    , region
    , btc_dca
    , RANK() OVER (
      PARTITION BY year_start
      ORDER BY btc_dca
    ) AS dca_ranking
  FROM cte_dca
)
/*
Then calculate the percentage change in dca for each year in the final output

Final output should have the following cols:
  year_start
  , region
  , btc_dca [Bitcoin dollar cost average for that region that year]
  , dca_ranking
  , dca_percentage_change
*/
SELECT
  year_start
  , region
  , btc_dca
  , dca_ranking
  , ROUND(
    ((btc_dca - LAG(btc_dca, 1) OVER (PARTITION BY region ORDER BY year_start)) /
      LAG(btc_dca, 1) OVER (PARTITION BY region ORDER BY year_start) *
      100)::NUMERIC
    , 2) AS dca_percentage_change
FROM cte_dca_rank
ORDER BY region, year_start
;
```