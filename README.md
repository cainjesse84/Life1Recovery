from flask import Flask
from threading import Thread
import telebot
import requests
import threading
import time
import random

# --- Flask keep-alive server for Render ---
app = Flask('')

@app.route('/')
def home():
    return "RecoveryBot is alive!"

def run():
    app.run(host='0.0.0.0', port=8080)

def keep_alive():
    t = Thread(target=run)
    t.start()

keep_alive()

# === Tokens ===
BOT_TOKEN = '7563892304:AAGv0A7W4aOTSrlThjw05_HO1xed5fdx6hA'
HF_TOKEN = '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'
bot = telebot.TeleBot(BOT_TOKEN)

# === Product link ===
GUMROAD_LINK = "https://cainjesse.gumroad.com/l/dcmlm"

# === Motivation quotes ===
quotes = [
    "Just for today, I will stay clean.",
    "You are stronger than the craving.",
    "Progress, not perfection.",
    "Recovery is worth it.",
    "One day at a time.",
    "Relapse is not failure. Keep going.",
    "You’re not alone in this.",
    "Small steps every day."
]

chat_ids = set()

def send_daily_motivation():
    while True:
        time.sleep(86400)  # 24 hours
        quote = random.choice(quotes)
        for cid in chat_ids:
            try:
                bot.send_message(cid, f"🌟 Daily Motivation:\n\n{quote}")
            except Exception as e:
                print(f"Failed to send to {cid}: {e}")

# === Hugging Face Chat Function ===
def ask_ai(prompt):
    headers = {
        "Authorization": f"Bearer {HF_TOKEN}",
        "Content-Type": "application/json"
    }
    data = {
        "inputs": f"You are a recovery coach helping someone quit drugs.\n\nUser: {prompt}\nAI:"
    }
    try:
        response = requests.post(
            "https://api-inference.huggingface.co/models/mistralai/Mistral-7B-Instruct-v0.1",
            headers=headers,
            json=data
        )
        if response.status_code == 200:
            text = response.json()[0]["generated_text"]
            return text.split("AI:")[-1].strip()
        else:
            return "Sorry, I couldn't get a response right now. Try again later."
    except:
        return "There was an error contacting the AI service."

# === Telegram Commands ===
@bot.message_handler(commands=['start'])
def welcome(message):
    chat_ids.add(message.chat.id)
    bot.send_message(message.chat.id,
        "👋 Welcome to RecoveryBot!\n\nThis bot helps people quit drugs without rehab.\n\nType /buy to get my guide.\nType /12steps to see the 12 Steps.\nType /resources for support links.\nOr just talk to me about recovery."
    )

@bot.message_handler(commands=['buy'])
def buy_link(message):
    bot.send_message(message.chat.id, f"💳 Buy the guide here:\n{GUMROAD_LINK}")

@bot.message_handler(commands=['12steps'])
def steps(message):
    bot.send_message(message.chat.id,
        "📘 12 Steps of Recovery:\n"
        "1. Admit powerlessness\n"
        "2. Believe in a higher power\n"
        "3. Turn will over to God\n"
        "4. Moral inventory\n"
        "5. Admit wrongs\n"
        "6. Ready for God to remove defects\n"
        "7. Ask for removal of shortcomings\n"
        "8. Make amends list\n"
        "9. Make direct amends\n"
        "10. Continue personal inventory\n"
        "11. Seek God's will through prayer\n"
        "12. Carry message to others"
    )

@bot.message_handler(commands=['resources'])
def resources(message):
    bot.send_message(message.chat.id,
        "🌐 Resources:\n"
        "🔹 NA: https://na.org/meetingsearch/\n"
        "🔹 AA: https://aa.org/find-aa\n"
        "🔹 SAMHSA: https://samhsa.gov/find-help/national-helpline\n"
        "🔹 Celebrate Recovery: https://celebraterecovery.com\n"
        "🔹 Reddit: https://reddit.com/r/addiction"
    )

@bot.message_handler(commands=['help'])
def help_cmd(message):
    bot.send_message(message.chat.id,
        "📖 Commands:\n"
        "/start – Start the bot\n"
        "/buy – Buy the recovery guide\n"
        "/12steps – See the 12 Steps\n"
        "/resources – Support links\n"
        "/help – List of commands\n\n"
        "💬 Or just message me about recovery!"
    )

# === AI Chat Handler ===
@bot.message_handler(func=lambda m: True)
def chat(message):
    if message.text.startswith('/'):
        return
    reply = ask_ai(message.text)
    bot.send_message(message.chat.id, reply)

# === Launch daily motivation thread ===
threading.Thread(target=send_daily_motivation).start()

print("✅ RecoveryBot is running...")
bot.polling()
