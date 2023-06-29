
from flask import Flask, request
import requests
import os

app = Flask(__name__)

# Retrieve API key and account ID from environment variables
api_key = os.environ.get('OANDA_API_KEY')
account_id = os.environ.get('OANDA_ACCOUNT_ID')

@app.route('/webhook', methods=['POST'])
def webhook():
    data = request.get_json()
    print('Received alert:', data)

    # Extract information from the alert
    message = data['message']
    parts = message.split(' ')
    action = parts[9].replace('{{strategy.order.action}}', '')  # 'buy' or 'sell'
    symbol = parts[15].replace('{{ticker}}', '')  # ticker symbol

    # Send order to OANDA
    send_order_to_oanda(symbol, action)

    return '', 200

def send_order_to_oanda(symbol, action):
    # Ensure that API key and account ID are provided
    if not api_key or not account_id:
        print('API key or account ID is missing. Please set the environment variables.')
        return

    # Set up the headers for the API request
    headers = {
        'Content-Type': 'application/json',
        'Authorization': f'Bearer {api_key}',
    }

    # Set up the data for the order request
    data = {
        'order': {
            'instrument': symbol,
            'units': 100 if action == 'buy' else -100,
            'type': 'MARKET',
        }
    }

    # Send the order request to OANDA
    response = requests.post(f'https://api-fxtrade.oanda.com/v3/accounts/{account_id}/orders', headers=headers, json=data)
    print('OANDA response:', response.json())

if __name__ == '__main__':
    app.run(port=5000)
				
