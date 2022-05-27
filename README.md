# Sample project: Cryptocurrency analysis using PostgreSQL

### My good friend, Bitzi McCoyne, sauntered over to where I was grilling at our neighborhood backyard BBQ.

She looked excited. "Jacob," she said, "do you know anything about this cryptocurrency craze?"

"A little," I replied, cautiously.

"Well, I have been reading a bunch about it, and I want to invest in some currency. Can you help me do some analysis? I have some tables with sales information from a group of people who are part of this internet community I found, but I don't know how to use SQL to get the information I want."

"Sure, I can definitely help out. Why don't you send me the tables and the things you want to know, and I'll see what I can do?"

## 🎯 Goals
* Understand the nature of the data
* Answer Bitzi's questions

## 🏗 Dependencies
* PostgreSQL 13.4

## 📂 Data
* 3 tables in trading schema:
  * members (14 records; 3 columns: member_id, first_name, region)
  * prices (3404 records; 8 columns: ticker, market_date, price, open, high, low, volume, change)
  * transactions (22918 records; columns: txn_id, member_id, ticker, txn_date, txn_type, quantity, percentage_fee, txn_time)

<br/>

&emsp;Entity Relationship Diagram (ERD):

&emsp;&emsp;&emsp;<img src="https://github.com/JacobTews/sql_crypto/blob/5ec63c4ea883ba3eeb73634f65d44d1bb0d5cbd1/crypto-erd.png" width="500" height="500" alt="entity relationship diagram">

&emsp;&emsp;&emsp;(Original source: Danny Ma's SQL Simplified course)

<br/>

## 💡 Questions answered:

1. What are the `market_date`, `price`, `volume`, and `price_rank` values for the days with the top 5 highest price values for each ticker in the trading.prices table?

| ticker |	market_date |	price |	volume |	price_rank |
| --- | --- | --- | --- | --- |
| BTC |	2021-04-13 |	$63540.9	| 126.56K	| 1 |
| BTC |	2021-04-15 | $63216	| 76.97K |	2 |
| BTC |	2021-04-14 | $62980.4 |	130.43K	| 3 |
| BTC |	2021-04-16 | $61379.7 |	136.85K	| 4 |
| BTC |	2021-03-13 | $61195.3 |	134.64K	| 5 |
| ETH |	2021-05-11 | $4167.78 |	1.27M	| 1 |
| ETH |	2021-05-14 | $4075.38 |	2.06M	| 2 |
| ETH |	2021-05-10 | $3947.9	| 2.70M	| 3 |
| ETH |	2021-05-09 | $3922.23	| 1.94M	| 4 |
| ETH |	2021-05-08 | $3905.55	| 1.34M	| 5 |

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#1-what-are-the-market_date-price-volume-and-price_rank-values-for-the-days-with-the-top-5-highest-price-values-for-each-ticker-in-the-tradingprices-table)**

<br/><br/>

2. What are the 7 day rolling averages for the price and volume columns in the trading.prices table for each ticker?

&emsp;&emsp;* Return only results for the first 10 days of August, 2021

|	ticker	|	market_date	|	price	|	moving_avg_price	|	volume	|	moving_avg_volume
|	--- |	--- |	--- |	--- |	--- |	--- |
|	BTC	|	2021-08-01	|	$39,878.30 |	$39,469.96 |	80330	| 98827.50
|	BTC	|	2021-08-02	|	$39,168.40 |	$39,942.13 |	74810	| 100041.25
|	BTC	|	2021-08-03	|	$38,130.30 |	$40,048.84 |	260	| 77870.00
|	BTC	|	2021-08-04	|	$39,736.90 |	$40,084.45 |	79220	| 75242.50
|	BTC	|	2021-08-05	|	$40,867.20 |	$40,192.45 |	130600	| 72952.50
|	BTC	|	2021-08-06	|	$42,795.40 |	$40,541.70 |	111930	| 77531.25
|	BTC	|	2021-08-07	|	$44,614.20 |	$40,843.05 |	112840	| 79330.00
|	BTC	|	2021-08-08	|	$43,792.80 |	$41,122.94 |	105250	| 86905.00
|	BTC	|	2021-08-09	|	$46,284.30 |	$41,923.69 |	117080	| 91498.75
|	BTC	|	2021-08-10	|	$45,593.80 |	$42,726.86 |	80550	| 92216.25
|	ETH	|	2021-08-01	|	$2,556.23 |	$2,368.62 |	1200000	| 1034463.75
|	ETH	|	2021-08-02	|	$2,608.04 |	$2,420.90 |	970670	| 1057430.00
|	ETH	|	2021-08-03	|	$2,506.65 |	$2,455.54 |	158450	| 840986.25
|	ETH	|	2021-08-04	|	$2,725.29 |	$2,508.67 |	1230000	| 838486.25
|	ETH	|	2021-08-05	|	$2,827.21 |	$2,574.69 |	1650000	| 923618.75
|	ETH	|	2021-08-06	|	$2,889.43 |	$2,638.25 |	1060000	| 975775.00
|	ETH	|	2021-08-07	|	$3,158.00 |	$2,725.38 |	64840	| 855130.00
|	ETH	|	2021-08-08	|	$3,012.07 |	$2,785.37 |	1250000	| 947995.00
|	ETH	|	2021-08-09	|	$3,162.93 |	$2,861.20 |	1440000	| 977995.00
|	ETH	|	2021-08-10	|	$3,140.71 |	$2,927.79 |	1120000	| 996661.25

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#2-calculate-a-7-day-rolling-average-for-the-price-and-volume-columns-in-the-tradingprices-table-for-each-ticker)**

<br/><br/>

3. What is the monthly cumulative volume traded for each ticker in 2020?

| ticker	| month_start	| cumulative_monthly_volume |
| --- | --- | --- |
| BTC	| 2020-01-01 | 23451920 |
| BTC	| 2020-02-01 | 46839130 |
| BTC	| 2020-03-01 | 94680450 |
| BTC	| 2020-04-01 | 134302740 |
| BTC	| 2020-05-01 | 172687010 |
| BTC	| 2020-06-01 | 188026610 |
| BTC	| 2020-07-01 | 201272600 |
| BTC	| 2020-08-01 | 216762630 |
| BTC	| 2020-09-01 | 300641440 |
| BTC	| 2020-10-01 | 303060020 |
| BTC	| 2020-11-01 | 307220090 |
| BTC	| 2020-12-01 | 311045880 |
| ETH	| 2020-01-01 | 412070000 |
| ETH	| 2020-02-01 | 969850000 |
| ETH	| 2020-03-01 | 1765210000 |
| ETH	| 2020-04-01 | 2520430000 |
| ETH	| 2020-05-01 | 3067520000 |
| ETH	| 2020-06-01 | 3286870000 |
| ETH	| 2020-07-01 | 3524140000 |
| ETH	| 2020-08-01 | 3772290000 |
| ETH	| 2020-09-01 | 4181770000 |
| ETH	| 2020-10-01 | 4394990000 |
| ETH	| 2020-11-01 | 4877850000 |
| ETH	| 2020-12-01 | 5177300000 |

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#3-calculate-the-monthly-cumulative-volume-traded-for-each-ticker-in-2020)**

<br/><br/>

4. What is the daily percentage change in volume for each ticker in the trading.prices table?

&emsp;&emsp;* Return only results for the first 10 days of August, 2021

| ticker | market_date	| volume	| previous_volume	| daily_change |
| --- | --- | --- | --- | --- |
| BTC	| 2021-08-01	| 80330	|	44650 |	79.91 |
| BTC	| 2021-08-02	| 74810	|	80330	|	-6.87 |
| BTC	| 2021-08-03	| 260	|	74810	|	-99.65 |
| BTC	| 2021-08-04	| 79220	|	260	|	30369.23 |
| BTC	| 2021-08-05	| 130600	|	79220	|	64.86 |
| BTC	| 2021-08-06	| 111930	|	130600	|	-14.3 |
| BTC	| 2021-08-07	| 112840	|	111930	|	0.81 |
| BTC	| 2021-08-08	| 105250	|	112840	|	-6.73 |
| BTC	| 2021-08-09	| 117080	|	105250	|	11.24 |
| BTC	| 2021-08-10	| 80550	|	117080	|	-31.2 |
| ETH	| 2021-08-01	| 1200000	|	507080	|	136.65 |
| ETH	| 2021-08-02	| 970670	|	1200000	|	-19.11 |
| ETH	| 2021-08-03	| 158450	|	970670	|	-83.68 |
| ETH	| 2021-08-04	| 1230000	|	158450	|	676.27 |
| ETH	| 2021-08-05	| 1650000	|	1230000	|	34.15 |
| ETH	| 2021-08-06	| 1060000	|	1650000	|	-35.76 |
| ETH	| 2021-08-07	| 64840	|	1060000	|	-93.88 |
| ETH	| 2021-08-08	| 1250000	|	64840	|	1827.82 |
| ETH	| 2021-08-09	| 1440000	|	1250000	|	15.2 |
| ETH	| 2021-08-10	| 1120000	|	1440000	|	-22.22 |

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#4-calculate-the-daily-percentage-change-in-volume-for-each-ticker-in-the-tradingprices-table)**

<br/><br/>

5. Which 3 people currently (as of the most recent date in the database) hold the highest quantity of Bitcoin?

| first_name |	total_quantity |
| --- | --- |
| Nandita |	4160.21987 |
| Leah |	4046.090897 |
| Ayush |	3945.198083 |

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#5-which-top-3-mentors-have-the-most-bitcoin-quantity)**

<br/><br/>

6. Which market_date values have fewer than 5 transactions?

| market_date	| transaction_count |
| --- | --- |
| 2021-08-29 | 0 |
| 2021-08-28 | 0 |
| 2021-07-17 | 3 |
| 2021-01-06 | 4 |
| 2020-01-17 | 4 |
| 2019-07-15 | 4 |
| 2019-06-14 | 3 |
| 2018-10-20 | 4 |

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#6-which-market_date-values-which-have-fewer-than-5-transactions)**

<br/><br/>

7. In a single table output, display:    
    1. What is the dollar cost average (btc_dca) for all Bitcoin purchases by region for each calendar year?
    2. What is the rank of each region each year?
    3. What is the dollar cost average yearly percentage change for each region?

| year_start |	region |	btc_dca |	dca_ranking |	dca_percentage_change |
| --- | --- | --- | --- | --- |
| 2017-01-01	| Africa |	3987.626287 |	4 |	 |
| 2018-01-01	| Africa |	7690.712833 |	3 |	92.86 |
| 2019-01-01	| Africa |	7368.82038 |	4 |	-4.19 |
| 2020-01-01	| Africa |	11114.12477 |	3 |	50.83 |
| 2021-01-01	| Africa |	44247.21526 |	2 |	298.12 |
| 2017-01-01	| Asia |	4002.938703 |	5 |	 |
| 2018-01-01	| Asia |	7829.998857 |	4 |	95.61 |
| 2019-01-01	| Asia |	7267.678553 |	1 |	-7.18 |
| 2020-01-01	| Asia |	10759.62115 |	2 |	48.05 |
| 2021-01-01	| Asia |	44570.90087 |	4 |	314.24 |
| 2017-01-01	| Australia |	3982.331493 |	3 |	 |
| 2018-01-01	| Australia |	7524.877929 |	1 |	88.96 |
| 2019-01-01	| Australia |	7368.453742 |	3 |	-2.08 |
| 2020-01-01	| Australia |	11413.90606 |	5 |	54.9 |
| 2021-01-01	| Australia |	44866.30462 |	5 |	293.08 |
| 2017-01-01	| India |	3680.764369 |	1 |	 |
| 2018-01-01	| India |	8031.110232 |	5 |	118.19 |
| 2019-01-01	| India |	7731.354536 |	5 |	-3.73 |
| 2020-01-01	| India |	10333.48559 |	1 |	33.66 |
| 2021-01-01	| India |	43793.71375 |	1 |	323.8 |
| 2017-01-01	| United States | 3812.006322 |	2 |	 |
| 2018-01-01	| United States | 7578.475104 |	2 |	98.81 |
| 2019-01-01	| United States | 7368.166254 |	2 |	-2.78 |
| 2020-01-01	| United States | 11123.66575 |	4 |	50.97 |
| 2021-01-01	| United States | 44456.22416 |	3 |	299.65 |

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#7-for-this-question-we-will-generate-a-single-table-output-which-solves-a-multi-part-problem-about-the-dollar-cost-average-of-btc-purchases)**

