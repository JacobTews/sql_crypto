# Sample project: Cryptocurrency analysis using PostgreSQL

### My good friend, Bitzi McCoyne, sauntered over to where I was grilling at our neighborhood backyard BBQ.

She looked excited. "Jacob," she whispered, "do you know anything about this cryptocurrency craze?"

"A little," I replied, cautiously.

"Well, I have been reading a bunch about it, and I want to invest in some currency. Can you help me do some analysis? I have some tables with sales information from a group of people who are part of this internet community I found, but I don't know how to use SQL to get the information I want."

"Sure, I can definitely help out. Why don't you send me the tables and the things you want to know, and I'll see what I can do?"

## 🎯 Goals
* Understand the nature of the data
* Answer Bitzi's questions

## 🏗 Dependencies
* PostgreSQL 13.4

## 📂 Data
* 3 tables in schema:
  * members (14 records; 3 columns: member_id, first_name, region)
  * prices (3404 records; 8 columns: ticker, market_date, price, open, high, low, volume, change)
  * transactions (22918 records; columns: txn_id, member_id, ticker, txn_date, txn_type, quantity, percentage_fee, txn_time)

ERD:


(Original source: Danny Ma's SQL Simplified course)

## 💡 Insights

