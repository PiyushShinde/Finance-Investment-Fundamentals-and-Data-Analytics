
<h1 id="tocheading">Table of Contents</h1>
<div id="toc"></div>

## Using Capital Asset Pricing Model:
* [To calculate $\beta$ of a stock](#first-bullet)
* [To calculate expected return of a stock](#second-bullet)
* [To calculate the Sharpe ratio](#third-bullet)


```python
import numpy as np
import pandas as pd
from pandas_datareader import data as wb
```

## Calculate $\beta$ of a stock <a class="anchor" id="first-bullet"></a>

The $\beta$ of a stock shows the extent to which the change in the price of a stock is related to the overall performance of the market.

### $$ 
\beta_{stock} = \frac{\sigma_{stock,market}}{\sigma_{market}}
$$

Here, we will analyse the stock P&G relative to the market S&P 500.


```python
# data = pd.DataFrame()
# tickers = ['PG','^GSPC']

# for t in tickers:
#     data[t] = wb.DataReader(t, 'google', start = '2012-1-1', end = '2016-12-31')['Close']
    
# data.head()

data = pd.read_csv('CAPM_data.csv', index_col = 'Date')  
```

To calculate $\beta$ we need two values - <br > 
* Covariance between P&G and S&P 500 <br >
* Variance of S&P 500


```python
sec_returns = np.log( data / data.shift(1) )
sec_returns.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PG</th>
      <th>^GSPC</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2012-01-03</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2012-01-04</th>
      <td>-0.000449</td>
      <td>0.000188</td>
    </tr>
    <tr>
      <th>2012-01-05</th>
      <td>-0.004201</td>
      <td>0.002939</td>
    </tr>
    <tr>
      <th>2012-01-06</th>
      <td>-0.002408</td>
      <td>-0.002540</td>
    </tr>
    <tr>
      <th>2012-01-09</th>
      <td>0.004211</td>
      <td>0.002259</td>
    </tr>
  </tbody>
</table>
</div>




```python
cov = sec_returns.cov() * 250
cov
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PG</th>
      <th>^GSPC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>PG</th>
      <td>0.020843</td>
      <td>0.010025</td>
    </tr>
    <tr>
      <th>^GSPC</th>
      <td>0.010025</td>
      <td>0.016361</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Covariance between P&G and the market
cov_with_market = cov.iloc[0,1]
cov_with_market
```




    0.010025480950656808




```python
#Annualized Variance of S&P 500
market_var = sec_returns['^GSPC'].var() * 250
market_var
```




    0.016360592699269063




```python
beta = cov_with_market / market_var
beta
```




    0.61278225886674098



#### The value of $\beta$ lies between 0 and 1. This means P&G is a defensive stock. This means the market volatility does not affect the P&G prices extensively. To validate our calculated value of $\beta$ for P&G, the quoted $\beta$ coefficient found by searching 'PG' in Google Finance website is 0.62.

------------------------------------------------------

## Calculate expected return of a stock <a class="anchor" id="second-bullet"></a>

The expected return of the stock will be equal to the sum of the risk-free rate and the product of the $\beta$ and the market risk premium. 

### $$
r_{stock} = r_f + \beta_{stock}(r_{market} - r_f) 
$$

$\rightarrow r_f$ = risk-free rate


```python
#The market risk premium for S&P 500 is 5.4%
#Risk free rate = 2.5% (corresponding to the yield of a 10 year government bond as shown in Bloomberg)
er = 0.025 + beta * 0.054
er
```




    0.058090241978804011



------------------------------------------------------

## Calculate the Sharpe ratio <a class="anchor" id="third-bullet"></a>

The Shapre ratio is the ratio of the excess expected return of the stock to the volatility of the stock. So the ratio is directly propotional to the expected return and inversely propotional to the risk of the stock. It is used to compare between stocks and portfolios to decide which is preferable in terms of risk and return. 

### $$
Sharpe = \frac{\overline{r_{stock}} - r_f}{\sigma_{stock}}
$$


```python
Sharpe = (er - 0.025) / (sec_returns['PG'].std() * 250 ** 0.5)
Sharpe
```




    0.22920428814607627


