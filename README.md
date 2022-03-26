# API-homework
## Unit 5 - Financial Planning
### intial import (we need follow pip for this homework)
```
import os
import requests
import pandas as pd
import json
from dotenv import load_dotenv
import alpaca_trade_api as tradeapi
from MCForecastTools import MCSimulation
from alpaca_trade_api.rest import REST, TimeFrame
from datetime import date, timedelta
import datetime
```
## Part 1 - Personal Finance Planner
### Collect Crypto Prices Using the requests Library

#### Set current amount of crypto assets<br />
my_btc = 1.2<br />
my_eth = 5.3<br />

#### Crypto API URLs
```
btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=CAD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=CAD"
```

#### Fetch current BTC price
```
btc_request = requests.get(btc_url)
btc_data = btc_request.json()
btc_price = btc_data['data']['1']['quotes']['CAD']['price']
```
#### Fetch current ETH price
```
eth_request = requests.get(eth_url)
eth_data = eth_request.json()
eth_price = eth_data['data']['1027']['quotes']['CAD']['price']
```
#### Compute current value of my crpto
```
my_btc_value = float(btc_price) * my_btc
my_eth_value = float(eth_price) * my_eth
```
#### Print current crypto wallet balance
```
print(f"The current value of your {my_btc} BTC is ${my_btc_value:0.2f}")
print(f"The current value of your {my_eth} ETH is ${my_eth_value:0.2f}")
```
### Result
The current value of your 1.2 BTC is $66368.83<br />
The current value of your 5.3 ETH is $20651.87<br />
