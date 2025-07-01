# Life1Recoveryfrom flask import Flask
from threading import Thread
import telebot
import requests
import threading
import time
import random

# Flask web server to keep bot alive
app = Flask('')

@app.route('/')
def home():
    return "I'm alive!"

def run():
    app.run(host='0.0.0.0', port=8080)

def keep_alive():
    t = Thread(target=run)
    t.start()

# Keep-alive for free Render hosting
keep_alive()

# Telegram & Hugging Face Setup
BOT_TOKEN = '7563892304:AAGv0A7W4aOTSrlThjw05_HO1xed5fdx6hA'
HF_TOKEN = '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'
bot = telebot.TeleBot(BOT_TOKEN)

GUMROAD_LINK = "https://cainjesse.gumroad.com/l/dcmlm"

motivational_quotes = [
    "Just for today, I will stay clean.",
    "You're stronger than the urge.",
    "Progress
