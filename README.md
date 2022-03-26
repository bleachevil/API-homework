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

### Collect Investments Data Using Alpaca: SPY (stocks) and AGG (bonds)

#### Set current amount of shares
my_agg = 200<br />
my_spy = 50<br />

#### Set Alpaca API key and secret
```
alpaca_api_key = os.getenv('ALPACA_API_KEY')
alpaca_secret_key = os.getenv('ALPACA_SECRET_KEY')
```
#### Create the Alpaca API object
```
alpaca = tradeapi.REST(alpaca_api_key,alpaca_secret_key,api_version='v2')
```
#### set open and close day and with limit of 1000
```
today = date.today()
td = timedelta(1000)
open_day = today - td
close_day = today - timedelta(1) 
openday = open_day.strftime("%Y-%m-%d")
closeday = close_day.strftime("%Y-%m-%d")
```
#### Set the tickers
```
tickers = ["AGG", "SPY"]
```
#### Get current closing prices for SPY and AGG
```
df = alpaca.get_bars(tickers, TimeFrame.Day,openday, closeday, adjustment='raw').df
df.index = df.index.date
df1 = pd.pivot_table(df,values='close', index=df.index, columns = ['symbol'])
df1
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/resultfordf1.png?raw=true)

#### Pick AGG and SPY close prices
```
agg_close_price = df1.iloc[-1][0]
spy_close_price = df1.iloc[-1][1]
```
#### Print AGG and SPY close prices
```
print(f"Current AGG closing price: ${agg_close_price}")
print(f"Current SPY closing price: ${spy_close_price}")
```
### result
Current AGG closing price: $106.1<br />
Current SPY closing price: $452.69<br />

#### Compute the current value of shares
```
my_spy_value = spy_close_price * my_spy
my_agg_value = agg_close_price * my_agg
```
#### Print current value of shares
```
print(f"The current value of your {my_spy} SPY shares is ${my_spy_value:0.2f}")
print(f"The current value of your {my_agg} AGG shares is ${my_agg_value:0.2f}")
```
### result
The current value of your 50 SPY shares is $22634.50<br />
The current value of your 200 AGG shares is $21220.00<br />

### Savings Health Analysis
#### Set monthly household income
```
monthly_income = 12000
```
#### Consolidate financial assets data
```
crypto = my_btc_value + my_eth_value
shares = my_spy_value + my_agg_value
data = {'amount':[crypto,shares]}
```
#### Create and display savings DataFrame
```
df_savings = pd.DataFrame(data, index = ['crypto','shares'])
display(df_savings)
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/resultdisplay1.png?raw=true)

#### Plot savings pie chart
```
df_savings.plot.pie(subplots=True, figsize=(11, 6))
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/resultdisplay2.png?raw=true)

#### Set ideal emergency fund (equal to three times monthly income which is 36000)
```
emergency_fund = monthly_income * 3
```
#### Calculate total amount of savings
```
total_savings = float(df_savings.sum())
```
#### Validate saving health
```
if total_savings > emergency_fund:
    print("congratulating the person for having enough money in this fund.")
elif total_savings == emergency_fund:
    print("congratulating the person on reaching this financial goal.")
else:
    print(f"${emergency_fund - total_savings} dollars away the person is from reaching the goal.")
```
### result
congratulating the person for having enough money in this fund.

## Part 2 - Retirement Planning
### Monte Carlo Simulation
Note: finish the homework with all Optional Challenge - Early Retirement (forecast 5 and 10 years)<br />

**will not present code for 5 and 10 years forecast however will present the result**

#### Get 5 years' worth of historical data for SPY and AGG
```
start_date = '2016-05-01'
end_date = '2021-05-01'

data = alpaca.get_bars('AGG', TimeFrame.Day, start_date, end_date, adjustment='raw').df
data.drop(columns=['trade_count','vwap'], inplace=True)
name = 'AGG'
col = pd.MultiIndex.from_product([[name],data.columns])
data.columns = col

data2 = alpaca.get_bars('SPY', TimeFrame.Day, start_date, end_date, adjustment='raw').df
data2.drop(columns=['trade_count','vwap'], inplace=True)
name2 = 'SPY'
col2 = pd.MultiIndex.from_product([[name2],data2.columns])
data2.columns = col2

df_stock_data = data.merge(data2, how="inner", left_index=True, right_index=True)
df_stock_data.head()
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/project2data1.png?raw=true)

#### Configuring a Monte Carlo simulation to forecast 30 years cumulative returns
```
MC_thrityyear = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.40,.60],
    num_simulation = 500,
    num_trading_days = 252*30)
```
#### Printing the simulation input data and result
```
MC_thrityyear.portfolio_data.head()
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/project2data2.png?raw=true)

#### Running a Monte Carlo simulation to forecast 30 years cumulative returns
```
MC_thrityyear.calc_cumulative_return()
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/project2data3.png?raw=true)

#### Plot simulation outcomes
```
line_plot = MC_thrityyear.plot_simulation()
line_plot.get_figure().savefig("MC_thrityyear_sim_plot.png", bbox_inches="tight")
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/MC_thrityyear_sim_plot.png?raw=true)

#### Plot probability distribution and confidence intervals
```
dist_plot = MC_thrityyear.plot_distribution()
dist_plot.get_figure().savefig('MC_thrityyear_dist_plot.png',bbox_inches='tight')
```
### result
![](https://github.com/bleachevil/API-homework/blob/main/MC_thrityyear_dist_plot.png?raw=true)

## Retirement Analysis

#### Fetch summary statistics from the Monte Carlo simulation results
```
tbl = MC_thrityyear.summarize_cumulative_return()
print(tb1)
```
### result
1[](https://github.com/bleachevil/API-homework/blob/main/project2data4.png?raw=true)

### Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment
```
initial_investment = 20000
ci_lower = round(tbl[8]*10000,2)
ci_upper = round(tbl[9]*10000,2)
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")
      
```
### result
There is a 95% chance that an initial investment of $20000 in the portfolio over the next 30 years will end within in the range of $47255.79 and $461847.23
 
### Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a 50% increase in the initial investment.
```
initial_investment = 20000 * 1.5
ci_lower = round(tbl[8]*10000,2)
ci_upper = round(tbl[9]*10000,2)
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")
```
### result
There is a 95% chance that an initial investment of $30000.0 in the portfolio over the next 30 years will end within in the range of $47255.79 and $461847.23

## Optional Challenge - Early Retirement

### Five Years Retirement Option (result only)

#### Plot simulation outcomes
![](https://github.com/bleachevil/API-homework/blob/main/MC_fiveyear_sim_plot.png?raw=true)

#### Plot probability distribution and confidence intervals
![](https://github.com/bleachevil/API-homework/blob/main/MC_tenyear_dist_plot.png?raw=true)

#### Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment.
There is a 95% chance that an initial investment of $20000 in the portfolio over the next 5 years will end within in the range of $9447.07 and $25364.85

### ten Years Retirement Option (result only)

#### Plot simulation outcomes
![](https://github.com/bleachevil/API-homework/blob/main/MC_tenyear_sim_plot.png?raw=true)

#### Plot probability distribution and confidence intervals
![](https://github.com/bleachevil/API-homework/blob/main/MC_tenyear_dist_plot.png?raw=true)

#### Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment.
There is a 95% chance that an initial investment of $20000 in the portfolio over the next 10 years will end within in the range of $12966.3 and $49890.23

