---
layout: nb_post
date: 2018-02-08
title: "The Futility of Buying the Dip"
---

### Background
This is an analysis I did in 2016 to convince myself that buying the dip is smart. At that point, I had two years of savings but not invested anything in the stock market because I thought that it was overpriced and that I would be better off sitting it out and going in after the inevitable big crash. Ironically, this analysis convinced me not to try to buy the dip and I ended up investing everything I had immediately after.

### Assumptions
I made some very strong simplifying assumptions. You should feel free to skip this post if they seem too naive to you. I considered them reasonable given the level of effort, risk and sophistication that I desired, but they certainly don't make sense for everyone.
1. I only looked at stocks, specifically the S&P500, as a proxy for an investment portfolio.
2. I ignore dividends and only consider the capital gains of stock.
3. I assumed that we don't use financial derivatives.
4. I assumed a pure buy-and-hold strategy. We don't have to think about selling, only when to buy and how much.

### Strategies
Assumption 4 simplifies things a lot, leaving us with one clean problem: Stock appreciates in the long run, so we want to figure out how to buy as much as possible as cheaply as possible. Often this means buying as soon as possible except when we think it's going to be cheaper at some point in future.

The naive way to study buying the dip would be to test out different ways of predicting (guessing) the dips. However, there are infinite techniques for making these predictions. So even if one strategy doesn't work out, it doesn't negate the *class* of strategies.

One ingenious thing we can do is to say: "Forget about the individual strategies. Let's just test the perfect strategy and the naive strategy and see how they stack up." The perfect strategy is very simply, knowing the future, which we can do since we're backtesting. In fact there are a range of perfect strategies depending on how far into the future the strategy is able to see. Each strategy then checks if there is a dip in sight, ie. a point were the price is the lowest, and saves up money to buy stock then.

One the other end of the spectrum is the naive strategy that has absolutely no foresight. As mentioned before, if we have no idea about the future except that stock appreciates in the long run, the best we can do is to buy as much as possible, right now.

In this analysis, I tested strategies with zero (naive), one, ten, hundred and thousand business days of foresight.

### Results
The astounding thing is that the thousand day strategy only saw a *14.6% increase* in net worth after 35 years (1980-2016). A thousand business days is roughly 4 years. The improbability of picking out the right day in a 4 year period, and the intestinal fortitude needed to sit on 2 years (expected value) of savings and go all in on that one day, makes this 14.6% payoff seem rather measly.

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


### The Grunt Work
The data analysis was quite straightforward. Here's the code in case you want to play around with it.

#### 1. Preparing Data
I obtained daily historical data of the S&P 500 from Yahoo Finance and did some basic preparation of the data. You can download it <a href='https://https://github.com/zichongkao/zichongkao.github.io/yahoo_s&p500_historical.csv'>here</a>.


```python
df = pd.read_csv('yahoo_s&p500_historical.csv')
df['Date'] = pd.to_datetime(df['Date'])

# Yahoo Finance data in reverse chronological order.
# Start from the future, use 'rolling' to work backwards to find the dips.
df['One Day Low'] = df['Close'].rolling(1+1, min_periods=1).min()
df['Ten Day Low'] = df['Close'].rolling(1+10, min_periods=1).min()
df['Hundred Day Low'] = df['Close'].rolling(1+100, min_periods=1).min()
df['Thousand Day Low'] = df['Close'].rolling(1+1000, min_periods=1).min()

# Sort the entries in chronological order so we can iterate through them later.
df.sort_values('Date', inplace=True)
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



#### 2. Account Abstraction
This is a simple abstraction for modeling my investment account. It serves a few basic functions:
1. Tracks current holdings of stock and cash. (`self.cur_hdg`)
2. Records historical net worth, ie. the total dollar value of all holdings on each day. (`self.hist_networth`)
3. Adds interest payments on the cash held. (`earn_interest()`)
4. Implements a function for buying stock that limits expediture based on current cash holdings. (`budget_buy()`)


```python
import collections
ANNUAL_BUSINESS_DAYS = 250
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
        # prevent going over budget due to rounding errors
        amt = np.floor(amt * 1e6) / 1e6
        total_expenditure = amt * unit_price
        if total_expenditure > self.cur_hdg['cash']:
            vetted_amt = self.cur_hdg['cash'] / unit_price
        else:
            vetted_amt = amt
        self._buy(asset, vetted_amt, unit_price)
        return vetted_amt * unit_price
            
    def update_networth(self, date, cur_asset_prices):
        cur_asset_prices['cash'] = 1.0
        networth = sum(amt * cur_asset_prices[asset]
                       for asset, amt in self.cur_hdg.items())
        self.hist_networth.append([date, networth] + list(self.cur_hdg.values()))
        
    def earn_interest(self, interest):
        self.cur_hdg['cash'] *= (1.0 + interest)
        
    def process_day(self, date, cur_asset_prices, daily_interest=True):
        if daily_interest:
            self.earn_interest(self.annual_interest / ANNUAL_BUSINESS_DAYS)
        self.update_networth(date, cur_asset_prices)
        
```

#### 3. Run Models
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
daily_salary = 100000.0 / ANNUAL_BUSINESS_DAYS
```

I iterated through the days in the time period, earning salary, making buying decisions and recording my networth each day.

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

Now we just have to extract the data stored in our account objects.

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

#### 5. Plotting Results


```python
final_df = pd.DataFrame(final_results)[['Strategy', 'Net Worth (MM)', 'Cash', 'Stock']]
greedy_networth = final_df[final_df['Strategy']=='greedy']['Net Worth (MM)'][0]
final_df['Net Worth Increase (%)'] = 100 * (final_df['Net Worth (MM)']/greedy_networth - 1)
final_df
# displays the table above
```

```python
for account in accounts:
    df = account['account_df']
    plt.plot(df['Date'], df['Networth']/1e3, label=account['name'], linewidth=1.5)
plt.legend(loc='upper left')
plt.ylabel('Thousands of Dollars')
plt.title('Networth over Time')
```

<img src="/pics/output_14_1.png">


```python
for account in accounts:
    df = account['account_df']
    plt.plot(df['Date'], df['s&p']/1e3, label=account['name'], linewidth=1.5)
plt.legend(loc='upper left')
plt.ylabel('Units of S&P500 Stock')
plt.title('Stock Holdings over Time')
```

<img src="/pics/output_15_1.png">

This graph shows the stock purchasing behavior of the strategies. In the lead-up to the dotcom crash, between 1998 and 2002, the thousand day buy-the-dip strategy was able to hoard cash. It didn’t purchase any stock during that time and only went in heavily when the market was at its lowest. The same thing happened between 2004 and 2008 in the lead-up to the the great financial crisis.

No doubt these episodes allowed the buy-the-dip strategies to come out ahead. However, the graph shows that for long stretches of time (1983 to 1998 and 2008 to 2016), the buy-the-dip strategies, even with all their unnatural foresight, still found it most optimal to slowly accumulate stock. And it was during these long periods of incremental growth that all strategies built up significant portions of their net worth, eventually dwarfing gains from the periods of hoarding.


This graph shows the stock purchasing behavior of the strategies. In the lead-up to the dotcom crash, between 1998 and 2002, the thousand day buy-the-dip strategy was able to hoard cash. It didn't purchase any stock during that time and only went in heavily when the market was at its lowest. The same thing happened between 2004 and 2008 in the lead-up to the the great financial crisis.

No doubt these episodes allowed the buy-the-dip strategies to come out ahead. However, the graph shows that for long stretches of time (1983 to 1998 and 2008 to 2016), the buy-the-dip strategies, even with all their unnatural foresight, still found it most optimal to slowly accumulate stock. And it was during these long periods of incremental growth that all strategies built up significant portions of their net worth, eventually dwarfing gains from the periods of hoarding. 

