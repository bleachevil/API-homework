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

Set current amount of crypto assets<br />
my_btc = 1.2<br />
my_eth = 5.3<br />
