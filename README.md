import logging
from binance.client import Client
from binance.exceptions import BinanceAPIException, BinanceOrderException
import argparse
import sys

API_KEY = 'rLQMXH8QTiF25ZQnt9BOv5JdG7rT3li6KZUVeA6o7CeNN3qzyVPg2o7tcahQBLub'
API_SECRET = 'JANfrZGVaG03uahCbVcasThnvvmVtCmweNJSY01po3pwefG5AcW99czQcGi8AZdS'

client = Client(API_KEY,API_SECRET,testnet=True)
client.futures_account()

symbol = 'BTCUSDT'
buy_price_threshold = 55000
sell_price_threshold = 61000
trade_quantity = 0.002

def get_current_price(symbol):
    ticker = client.get_symbol_ticker(symbol=symbol)
    return float(ticker['price'])

get_current_price(symbol)

# Market BUY
def place_futures_buy_order(symbol, quantity):
    """CORRECT: Futures Market BUY order"""
    try:
        order = client.futures_create_order(
            symbol=symbol,           # BTCUSDT
            side='BUY',              # Futures uses 'BUY'/'SELL'
            type='MARKET',           # Market order
            quantity=quantity        # 0.001 (check min quantity)
        )
        print(f"Futures BUY order: {order['orderId']}")
        return order
    except BinanceAPIException as e:
        print(f"Futures order failed: {e}")
        return None

# Usage
symbol = "BTCUSDT"
trade_quantity = 0.002  # Small test size
place_futures_buy_order(symbol, trade_quantity)

# Market SELL  
def place_futures_sell_order(symbol, quantity):
    """CORRECT: Futures Market SELL order"""
    try:
        order = client.futures_create_order(
            symbol=symbol,           # BTCUSDT
            side='SELL',              # Futures uses 'BUY'/'SELL'
            type='MARKET',           # Market order
            quantity=quantity        # 0.001 (check min quantity)
        )
        print(f"Futures SELL order: {order['orderId']}")
        return order
    except BinanceAPIException as e:
        print(f"Futures order failed: {e}")
        return None

# Usage
symbol = "BTCUSDT"
trade_quantity = 0.002  # Small test size
place_futures_sell_order(symbol, trade_quantity)

def trading_bot():
     in_position = False

     while True:
         current_price = get_current_price(symbol)
         print(f"current price of {symbol}: {current_price}")

         if not in_position:
             if current_price < buy_price_threshold:
                 print(f"Price is below {buy_price_threshold}. Placing buy order.")
                 place_futures_buy_order(symbol, trade_quantity)
                 in_position = True
             else:
                 if current_price < sell_price_threshold:
                     print(f"Price is below {sell_price_threshold}. Placing sell order.")
                     place_futures_sell_order(symbol, trade_quantity)
                     in_position = False 
                     
                     time.sleep(3)
  
if __name__ == "__main__":
    trading_bot()
    
