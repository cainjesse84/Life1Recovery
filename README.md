from flask import Flask
from threading import Thread
import telebot
import requests
import threading
import time
import random

# Flask web server to keep bot alive (for Render)
app = Flask('')

@app.route('/')
def home():
    return "Bot is alive!"

def run():
    app.run(host='0.0.0.0', port=8080)

def keep_alive():
    t = Thread(target=run)
    t.start()

keep_alive()

# === Your tokens ===
BOT_TOKEN = '7563892304:AAGv0A7W4aOTSrlThjw05_HO1xed5fdx6hA'
HF_TOKEN = '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'
bot = telebot.TeleBot(BOT_TOKEN)

# === Product link ===
GUMROAD_LINK = "https://cainjesse.gumroad.com/l/dcmlm"

# === Daily motivation quotes ===
quotes = [
    "Just for today, I will stay clean.",
    "You're stronger than the urge.",
    "Progress, not perfection.",
    "Sobriety is a daily commitment.",
    "You're not alone in this fight.",
    "Every day clean is a victory.",
