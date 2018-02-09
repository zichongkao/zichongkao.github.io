---
layout: post
date: 2018-02-08
title: "Why Not To Buy-The-Dip?"
---

### Background
I did this analysis in 2016, after graduating from college and working for two years. At that point, I had not invested a single cent in the stock market because I thought that it was overpriced and that I was being smart by sitting it out and waiting for the big crash before going in. This analysis convinced me otherwise.

### Method
I simplified things by only looking at stocks and by using the S&P500 as a proxy for a stock portfolio. I assumed that we don't use financial derivatives, and even ignored selling altogether - once a stock is bought, I assumed we hold it forever. This seems a little contrived, but in practice, many people do end up buying and holding vanilla index funds.

I compared two strategies:
- The greedy strategy, where I buy as much as I can as soon as I can, and
- The buy-the-dip stategy where I wait for sharp declines before buying. 

These two stategies actually bookmark the spectrum of forecasting ability. If we have no ability to forecast the future beyond the knowledge that stocks rise in the long term, the greedy strategy is best. If we have perfect knowledge of the future, we can wait until the right time and buy then. 

Perfect knowledge and the buy-the-dip strategy is clearly the best. But I wanted to know exactly how much better it is so I could decide if I should attempt to move in that direction. I also wanted to know given a more realistic forecasting ability, how well the strategy would perform. An elegant way to test this was to vary the time period that the algorithm was allowed to peek into the future. 

### 1. Preparing Data
I obtained daily historical data of the S&P 500 from Yahoo Finance and did some basic preparation of the data.


```python
df = pd.read_csv('yahoo_s&p500_historical.csv')
df['Date'] = pd.to_datetime(df['Date'])

# yahoo finance's data is in reverse chronological order which is convenient for us to start from the future 
# and work backwards to find the dips.
df['One Day Low'] = df['Close'].rolling(1+1, min_periods=1).min()
df['Ten Day Low'] = df['Close'].rolling(1+10, min_periods=1).min()
df['Hundred Day Low'] = df['Close'].rolling(1+100, min_periods=1).min()
df['Thousand Day Low'] = df['Close'].rolling(1+1000, min_periods=1).min()

# now sort the entries in chronological order so we can iterate through them later.
df.sort_values('Date', inplace=True)
```


```python
df.tail()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Adj Close</th>
      <th>One Day Low</th>
      <th>Ten Day Low</th>
      <th>Hundred Day Low</th>
      <th>Thousand Day Low</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>2016-09-02</td>
      <td>2177.489990</td>
      <td>2184.870117</td>
      <td>2173.590088</td>
      <td>2179.979980</td>
      <td>3091120000</td>
      <td>2179.979980</td>
      <td>2179.979980</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-09-06</td>
      <td>2181.610107</td>
      <td>2186.570068</td>
      <td>2175.100098</td>
      <td>2186.479980</td>
      <td>3447650000</td>
      <td>2186.479980</td>
      <td>2186.159912</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-09-07</td>
      <td>2185.169922</td>
      <td>2187.870117</td>
      <td>2179.070068</td>
      <td>2186.159912</td>
      <td>3319420000</td>
      <td>2186.159912</td>
      <td>2181.300049</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-09-08</td>
      <td>2182.760010</td>
      <td>2184.939941</td>
      <td>2177.489990</td>
      <td>2181.300049</td>
      <td>3727840000</td>
      <td>2181.300049</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
    </tr>
    <tr>
      <th>0</th>
      <td>2016-09-09</td>
      <td>2169.080078</td>
      <td>2169.080078</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>4233960000</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
      <td>2127.810059</td>
    </tr>
  </tbody>
</table>
</div>



### 2. Account Abstraction
This is a simple abstraction for modeling my investment account. It serves a few basic functions:
1. Tracks current holdings of stock and cash. (`self.cur_hdg`)
2. Records historical net worth, ie. the total dollar value of all holdings on each day. (`self.hist_networth`)
3. Adds interest payments on the cash held. (`earn_interest()`)
4. Implements a function for buying stock that limits expediture based on current cash holdings. (`budget_buy()`)


```python
import collections
class Account:
    def __init__(self, start_hdg, annual_interest=0.01):
        self.start_hdg = start_hdg
        self.cur_hdg = collections.OrderedDict(start_hdg)
        if 'cash' not in self.cur_hdg:
            self.cur_hdg['cash'] = 0.0

        self.annual_interest = annual_interest
        
        self.hist_networth = []
        
    def _buy(self, asset, amt, unit_price):
        total_expenditure = amt * unit_price
        assert total_expenditure <= self.cur_hdg['cash'], 'not enough cash'
        self.cur_hdg['cash'] -= total_expenditure
        self.cur_hdg[asset] = self.cur_hdg.get(asset, 0) + amt
        
    def budget_buy(self, asset, amt, unit_price):
        amt = np.floor(amt * 1e6) / 1e6  # prevent going over budget due to rounding errors
        total_expenditure = amt * unit_price
        if total_expenditure > self.cur_hdg['cash']:
            vetted_amt = self.cur_hdg['cash'] / unit_price
        else:
            vetted_amt = amt
        self._buy(asset, vetted_amt, unit_price)
        return vetted_amt * unit_price
            
    def update_networth(self, date, cur_asset_prices):
        cur_asset_prices['cash'] = 1.0
        networth = sum(amt * cur_asset_prices[asset] for asset, amt in self.cur_hdg.items())
        self.hist_networth.append([date, networth] + list(self.cur_hdg.values()))
        
    def earn_interest(self, interest):
        self.cur_hdg['cash'] *= (1.0 + interest)
        
    def process_day(self, date, cur_asset_prices, daily_interest=True):
        if daily_interest:
            self.earn_interest(self.annual_interest / 250) # assume 250 business days a year.
        self.update_networth(date, cur_asset_prices)
        
```

### 3. Run Models
I created five investment accounts, each starting with \$100K, earning \$100K annually, and having its own investment strategy. As mentioned before, the greedy model bought as much stock as it could, as soon as it could. The other four were buy-the-dip strategies with the ability to pick dips by looking 1, 10, 100 and 1000 days into the future.


```python
accounts = []
accounts.append({'name': 'greedy', 'account': Account({'cash': 100000}), 'low_column': 'Close'})
accounts.append({'name': 'one_day', 'account': Account({'cash': 100000}), 'low_column': 'One Day Low'})
accounts.append({'name': 'ten_day', 'account': Account({'cash': 100000}), 'low_column': 'Ten Day Low'})
accounts.append({'name': 'hundred_day', 'account': Account({'cash': 100000}), 'low_column': 'Hundred Day Low'})
accounts.append({'name': 'thousand_day', 'account': Account({'cash': 100000}), 'low_column': 'Thousand Day Low'})
```


```python
data_range = df[df.Date > pd.to_datetime('1980-01-01')]
daily_salary = 100000 / 250.0
```


```python
for _, row in data_range.iterrows():
    for account in accounts:
        account_object = account['account']
        account_object.cur_hdg['cash'] += daily_salary
        
        # Check the low for the given lookahead period. 
        # If that low occurs today, buy with what you have. Otherwise, wait.
        if row['Close'] <= row[account['low_column']]:
            account_object.budget_buy('s&p', account_object.cur_hdg['cash'] / row.Close, row.Close)
            
        account_object.process_day(row.Date, {'s&p': row['Close']})
```


```python
final_results = []
for account in accounts:
    account_df = pd.DataFrame(account['account'].hist_networth, columns=['Date', 'Networth', 'cash', 's&p'])
    account.update({'account_df': account_df})
    final_row = account_df.iloc[-1,:]
    final_results.append({'Net Worth (MM)': final_row['Networth']/1e6, 
                          'Cash': final_row['cash'], 
                          'Stock': final_row['s&p'], 
                          'Strategy':account['name']})
```

### Results


```python
final_df = pd.DataFrame(final_results)[['Strategy', 'Net Worth (MM)', 'Cash', 'Stock']]
greedy_networth = final_df[final_df['Strategy']=='greedy']['Net Worth (MM)'][0]
final_df['Net Worth Increase (%)'] = 100 * (final_df['Net Worth (MM)']/greedy_networth - 1)
final_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Strategy</th>
      <th>Net Worth (MM)</th>
      <th>Cash</th>
      <th>Stock</th>
      <th>Net Worth Increase (%)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>greedy</td>
      <td>21.132840</td>
      <td>0.001568</td>
      <td>9931.732545</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>one_day</td>
      <td>21.277338</td>
      <td>0.001068</td>
      <td>9999.641588</td>
      <td>0.683758</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ten_day</td>
      <td>21.686928</td>
      <td>0.000653</td>
      <td>10192.135118</td>
      <td>2.621925</td>
    </tr>
    <tr>
      <th>3</th>
      <td>hundred_day</td>
      <td>22.845556</td>
      <td>0.000647</td>
      <td>10736.651896</td>
      <td>8.104521</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thousand_day</td>
      <td>24.231639</td>
      <td>0.001233</td>
      <td>11388.065063</td>
      <td>14.663429</td>
    </tr>
  </tbody>
</table>
</div>



The startling result is that a *thousand* days of perfect foresight over a 25 year period only yields a *14.6% increase* in net worth. A thousand business days is roughly 4 years! 


```python
plt.figure(figsize=(14,10))
for account in accounts:
    df = account['account_df']
    plt.plot(df['Date'], df['Networth']/1e3, label=account['name'], linewidth=1.5)
plt.legend(loc='upper left')
plt.ylabel('Thousands of Dollars')
plt.title('Networth over Time')
```




    <matplotlib.text.Text at 0x7f6addb25400>




<img src="/pics/output_14_1.png" width="600x">



```python
plt.figure(figsize=(14,10))
for account in accounts:
    df = account['account_df']
    plt.plot(df['Date'], df['s&p']/1e3, label=account['name'], linewidth=1.5)
plt.legend(loc='upper left')
plt.ylabel('Units of S&P500 Stock')
plt.title('Stock Holdings over Time')
```




    <matplotlib.text.Text at 0x7f6add071eb8>




<img src="/pics/output_15_1.png" width="600x">


This graph shows the stock purchasing behavior of the strategies. In the lead-up to the dotcom crash, between 1998 and 2002, the thousand day buy-the-dip strategy was able to hoard cash. It didn't purchase any stock during that time and only went in heavily when the market was at its lowest. The same thing happened between 2004 and 2008 in the lead-up to the the great financial crisis.

No doubt these episodes allowed the buy-the-dip strategies to come out ahead. However, the graph shows that for long stretches of time (1983 to 1998 and 2008 to 2016), the buy-the-dip strategies, even with all their unnatural foresight, still found it most optimal to slowly accumulate stock. And it was during these long periods of incremental growth that all strategies built up significant portions of their net worth, eventually dwarfing gains from the periods of hoarding. 

