from binance.client import Client
import time

# Your API keys
API_KEY = 'your_api_key'
API_SECRET = 'your_api_secret'

# List of coins to exclude
excluded_symbols = ['BTCUSDT', 'ETHUSDT', 'XRPUSDT', 'EOSUSDT', 'PEPEUSDT', 'SOLUSDT', 'BNBUSDT', 'USDCUSDT', 'ADAUSDT', 'AVAXUSDT', 'CRVUSDT', 'DOGEUSDT', 'LINKUSDT', 'LTCUSDT', 'TRXUSDT', 'ETCUSDT', 'BCHUSDT', 'AAVEUSDT']

# Initialize the Binance client
client = Client(API_KEY, API_SECRET)

# Function to get symbol info and precision
def get_symbol_info(symbol):
    info = client.futures_exchange_info()
    for s in info['symbols']:
        if s['symbol'] == symbol:
            return s
    return None

# Function to get all USDT futures pairs
def get_futures_pairs():
    exchange_info = client.futures_exchange_info()
    symbols = exchange_info['symbols']
    futures_pairs = [symbol['symbol'] for symbol in symbols if symbol['status'] == 'TRADING' and 'USDT' in symbol['symbol']]
    return futures_pairs

# Function to get order book data
def get_order_book(symbol):
    order_book = client.futures_order_book(symbol=symbol)
    return order_book

# Function to get historical candles (with the correct interval)
def get_historical_candles(symbol, interval, limit=24):
    candles = client.futures_klines(symbol=symbol, interval=interval, limit=limit)
    return candles

# Function to find extremes (highs and lows)
def find_extremes(candles):
    highs = [float(candle[2]) for candle in candles]
    lows = [float(candle[3]) for candle in candles]
    return {
        'max': max(highs),
        'min': min(lows)
    }

# Function to get open orders for a futures symbol
def get_open_orders(symbol):
    orders = client.futures_get_open_orders(symbol=symbol)
    return orders

# Function to get open positions for a futures symbol
def get_open_positions(symbol):
    account_info = client.futures_account()
    positions = account_info['positions']
    open_positions = [p for p in positions if p['symbol'] == symbol and float(p['positionAmt']) != 0]
    return open_positions

# Function to calculate the quantity of coins for a $10 order
def calculate_quantity(price, amount_in_usd=10, symbol=None):
    quantity = amount_in_usd / price
    
    if symbol:
        # Get symbol info to get precision
        symbol_info = get_symbol_info(symbol)
        if symbol_info:
            # Get the quantity precision
            precision = symbol_info['quantityPrecision']
            # Round the quantity to the allowed precision
            quantity = round(quantity, precision)
    
    return quantity

# Function to place a limit buy order (long)
def place_limit_buy_order(symbol, amount, price):
    try:
        order = client.futures_create_order(
            symbol=symbol,
            side='BUY',
            type='LIMIT',
            quantity=amount,
            price=str(price),
            timeInForce='GTC'  # Good-Til-Canceled
        )
        print(f"Limit buy order placed at {price} for {amount} coins.")
    except Exception as e:
        print(f"Error placing buy order: {e}")

# Function to place a limit sell order (short)
def place_limit_sell_order(symbol, amount, price):
    try:
        order = client.futures_create_order(
            symbol=symbol,
            side='SELL',
            type='LIMIT',
            quantity=amount,
            price=str(price),
            timeInForce='GTC'  # Good-Til-Canceled
        )
        print(f"Limit sell order placed at {price} for {amount} coins.")
    except Exception as e:
        print(f"Error placing sell order: {e}")

# Function to analyze a specific futures symbol
def analyze(symbol):
    # Check if the symbol is in the excluded list
    if symbol in excluded_symbols:
        print(f"Symbol {symbol} is excluded from analysis.")
        return
    
    print(f"Analyzing symbol: {symbol}")
    
    # Check if there are open positions for the symbol
    open_positions = get_open_positions(symbol)
    if open_positions:
        print(f"There is an open position for symbol {symbol}, skipping analysis.")
        return

    # Get all open orders for the symbol
    open_orders = get_open_orders(symbol)
    long_order_exists = any(order['side'] == 'BUY' for order in open_orders)
    short_order_exists = any(order['side'] == 'SELL' for order in open_orders)
    
    # If a buy limit order is already placed, skip the symbol
    if long_order_exists:
        print(f"A buy limit order already exists for symbol {symbol}, skipping analysis.")
        return
    
    # If a sell limit order is already placed, skip the symbol
    if short_order_exists:
        print(f"A sell limit order already exists for symbol {symbol}, skipping analysis.")
        return
    
    # Get the last 24 1-hour candles
    candles = get_historical_candles(symbol, Client.KLINE_INTERVAL_1HOUR)
    
    # Find extremes
    extremes = find_extremes(candles)
    
    print(f"Extremes for the last 24 1-hour candles: ")
    print(f"Max: {extremes['max']}")
    print(f"Min: {extremes['min']}")
    
    # Get the current price from the order book (e.g., best bid)
    order_book = get_order_book(symbol)
    current_price = float(order_book['bids'][0][0])
    
    print(f"Current price: {current_price}")
    
    # Logic to place limit orders at found extremes
    # If the current price is higher than the minimum and the deviation is no more than 1%, place a buy order
    if extremes['min'] < current_price and (current_price - extremes['min']) / extremes['min'] <= 0.01:
        quantity = calculate_quantity(extremes['min'], symbol=symbol)  # Calculate the quantity for a $10 order
        print(f"Deviation from the minimum is less than 1%. Placing a buy limit order at {extremes['min']} for {quantity} coins.")
        place_limit_buy_order(symbol, quantity, extremes['min'])  # $10 buy order
    
    # If the current price is lower than the maximum and the deviation is no more than 1%, place a sell order
    elif extremes['max'] > current_price and (extremes['max'] - current_price) / extremes['max'] <= 0.01:
        quantity = calculate_quantity(extremes['max'], symbol=symbol)  # Calculate the quantity for a $10 order
        print(f"Deviation from the maximum is less than 1%. Placing a sell limit order at {extremes['max']} for {quantity} coins.")
        place_limit_sell_order(symbol, quantity, extremes['max'])  # $10 sell order

# Main loop to analyze all USDT futures pairs
def analyze_all():
    futures_pairs = get_futures_pairs()
    
    for symbol in futures_pairs:
        analyze(symbol)
        time.sleep(1)  # Add delay between requests to avoid overloading the API

# Start analyzing USDT futures pairs
analyze_all()

# Continuous analysis loop
def analyze_all_continuously():
    while True:
        try:
            # Analyze all USDT futures pairs
            analyze_all()
            
            # Add a delay before analyzing again (e.g., 60 seconds)
            time.sleep(60)
        
        except Exception as e:
            print(f"Error in analyze_all_continuously: {e}")
            # Optional: Add a longer delay after an error
            time.sleep(60)

# Start the continuous analysis
analyze_all_continuously()
