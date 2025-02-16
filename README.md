# Binance-level-trading

The Binance Level Trading v.1_0 bot analyzes the last 24 hourly candles on all USDT futures pairs on the Binance exchange (you can specify a list of coins to exclude from the analysis) and places a buy order (if the extreme is the minimum) or a sell order (if the extreme is the maximum)

To run the bot on your laptop, you need to follow a few steps:

1. Install Python
Make sure Python (version 3.6 or higher) is installed on your laptop. You can download it from the official website: https://www.python.org/downloads/.
Check if Python is installed by running the command:
python --version

2. Install necessary libraries
The bot uses the python-binance library to interact with the Binance API, as well as standard Python libraries. To install all required dependencies, run the following command in the command line:
pip install python-binance
If you encounter installation issues, make sure you have the latest version of pip.

3. Create Binance API keys
To work with the API, you need to create API keys on Binance:
Log in to your Binance account.
Go to the "API Management" section.
Create a new API key to be used for the bot to access data on Binance.
Copy your API Key and Secret Key.

4. Set up the API keys in the code
In the code, replace the placeholder values with your actual API keys from Binance:
API_KEY = 'your_api_key'
API_SECRET = 'your_api_secret'

If you need help with installation, setup, or customization, feel free to contact me on Telegram
https://t.me/Pin_Gvinn

If this script has helped you, tips are much appreciated:
USDT (BEP-20) 0x191baf84fa45a0ee9fbf1b261c41dfa0af12fb50
