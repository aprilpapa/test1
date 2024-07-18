import requests
import pandas as pd
from datetime import datetime
import numpy as np
import matplotlib.pyplot as plt

# CoinGecko API를 사용하여 비트코인 가격 데이터 불러오기 (최근 3년)
def fetch_coingecko_data():
    url = "https://api.coingecko.com/api/v3/coins/bitcoin/market_chart"
    params = {
        'vs_currency': 'usd',
        'days': '1095',  # 3년 (365일 * 3)
        'interval': 'daily'
    }
    response = requests.get(url, params=params)
    data = response.json()
    
    dates = [datetime.fromtimestamp(item[0] / 1000) for item in data['prices']]
    prices = [item[1] for item in data['prices']]
    
    df = pd.DataFrame({'Date': dates, 'Price': prices})
    return df

btc_data = fetch_coingecko_data()

# 현재 비트코인 가격과 보유량 설정
current_price = btc_data['Price'].iloc[-1]
btc_holdings = 10

# 최적 롱-숏 비율 계산
ratios = np.linspace(0, 1, 101)
portfolio_volatilities = []

for ratio in ratios:
    long_ratio = ratio
    short_ratio = 1 - ratio
    portfolio_values = []
    for price in btc_data['Price']:
        long_position = long_ratio * btc_holdings * price
        short_position = short_ratio * btc_holdings * (2 * current_price - price)
        portfolio_value = long_position + short_position
        portfolio_values.append(portfolio_value)
    portfolio_volatilities.append(np.std(portfolio_values))

# 최적 비율 선택
optimal_ratio = ratios[np.argmin(portfolio_volatilities)]

# 최적 비율 적용 포트폴리오 가치 계산
optimal_long_ratio = optimal_ratio
optimal_short_ratio = 1 - optimal_ratio
optimal_portfolio_values = []

for price in btc_data['Price']:
    long_position = optimal_long_ratio * btc_holdings * price
    short_position = optimal_short_ratio * btc_holdings * (2 * current_price - price)
    portfolio_value = long_position + short_position
    optimal_portfolio_values.append(portfolio_value)

btc_data['Optimal_Portfolio_Value'] = optimal_portfolio_values

# 그래프 그리기
plt.figure(figsize=(14, 7))
plt.plot(btc_data['Date'], btc_data['Price'] * btc_holdings, label='BTC Asset Value')
plt.plot(btc_data['Date'], btc_data['Optimal_Portfolio_Value'], label='Optimal Hedged Portfolio Value')
plt.xlabel('Date')
plt.ylabel('Value (USD)')
plt.title('BTC Asset Value vs. Optimal Hedged Portfolio Value Over 3 Years')
plt.legend()
plt.grid(True)
plt.show()
