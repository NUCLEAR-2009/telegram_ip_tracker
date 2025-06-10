from flask import Flask, request, jsonify
from telegram import Bot, Update
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters
import requests

app = Flask(__name__)

# Aapka bot token yahan dalen
TELEGRAM_TOKEN = '8193362916:AAFz6ed0aKo9GVuwaV3CCQupzbzAsulACpg'

bot = Bot(token=TELEGRAM_TOKEN)

@app.route('/webhook', methods=['POST'])
def webhook():
    update = Update.de_json(request.get_json(force=True), bot)
    dispatcher.process_update(update)
    return 'ok'

def start(update, context):
    update.message.reply_text('Welcome! Mujhe 10 digits ka phone number bhejiye, main uska IP address find karke aapko bhejta hoon.')

def handle_message(update, context):
    phone_number = update.message.text
    if len(phone_number) == 10 and phone_number.isdigit():
        ip_address = get_ip_from_phone_number(phone_number)
        if ip_address:
            update.message.reply_text(f'Phone number {phone_number} ka IP address yeh hai: {ip_address}.')
        else:
            update.message.reply_text('IP address retrieve nahi hua.')
    else:
        update.message.reply_text('Kripya valid 10 digits ka phone number bhejiye.')

def get_ip_from_phone_number(phone_number):
    # Yahan aapko phone number se IP address retrieve karne ke liye logic implement karna padega
    # Example ke liye main ek fake IP address return kar raha hoon
    return "192.168.1.1"

if __name__ == '__main__':
    updater = Updater(token=TELEGRAM_TOKEN, use_context=True)
    dispatcher = updater.dispatcher

    dispatcher.add_handler(CommandHandler('start', start))
    dispatcher.add_handler(MessageHandler(Filters.text & ~Filters.command, handle_message))

    updater.start_webhook(listen='0.0.0.0', port=8443, url_path=TELEGRAM_TOKEN, webhook_url='https://your-deployed-app-url/webhook')
    updater.idle()
