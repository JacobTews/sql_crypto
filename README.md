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

&emsp;Entity Relationship Diagram (ERD):

&emsp;&emsp;&emsp;<img src="https://github.com/JacobTews/sql_crypto/blob/5ec63c4ea883ba3eeb73634f65d44d1bb0d5cbd1/crypto-erd.png" width="500" height="500" alt="entity relationship diagram">

(Original source: Danny Ma's SQL Simplified course)

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

![image](https://user-images.githubusercontent.com/64455045/170350899-7314c323-6baa-4db4-a6a8-89c5d6f07287.png)

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#3-calculate-the-monthly-cumulative-volume-traded-for-each-ticker-in-2020)**

<br/><br/>

4. What is the daily percentage change in volume for each ticker in the trading.prices table?

&emsp;&emsp;* Return only results for the first 10 days of August, 2021

![image](https://user-images.githubusercontent.com/64455045/170350396-53531853-d175-4a58-9645-4aed87f16b0b.png)

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#4-calculate-the-daily-percentage-change-in-volume-for-each-ticker-in-the-tradingprices-table)**

<br/><br/>

5. Which 3 people currently (as of the most recent date in the database) hold the highest quantity of Bitcoin?

![image](https://user-images.githubusercontent.com/64455045/170351337-184e06a4-54d8-432b-9ae8-fd8daeecff19.png)

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#5-which-top-3-mentors-have-the-most-bitcoin-quantity)**

<br/><br/>

6. Which market_date values have fewer than 5 transactions?

![image](https://user-images.githubusercontent.com/64455045/170351581-4a49ba6c-bfbb-410f-8fc1-d013a1bec08c.png)

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#6-which-market_date-values-which-have-fewer-than-5-transactions)**

<br/><br/>

7. In a single table output, display:    
    1. What is the dollar cost average (btc_dca) for all Bitcoin purchases by region for each calendar year?
    2. What is the rank of each region each year?
    3. What is the dollar cost average yearly percentage change for each region?

![image](https://user-images.githubusercontent.com/64455045/170351895-10ce61f1-586b-4d4e-8648-77253129522e.png)

<br/>

**[Full query here](https://github.com/JacobTews/sql_crypto/blob/main/full_solutions.md#7-for-this-question-we-will-generate-a-single-table-output-which-solves-a-multi-part-problem-about-the-dollar-cost-average-of-btc-purchases)**

