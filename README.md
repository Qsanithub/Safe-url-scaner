# Safe-url-scaner
Directory Structure

safe-url-scanner/

├── README.md

├── requirements.txt

├── config.py

├── bot.py

├── urls.txt

└── .github/

└── workflows/

└── url_scan.yml

==============================

config.py

(DON'T upload this if your token is private)

BOT_TOKEN = "your_bot_token_here" ADMIN_ID = 123456789  # your Telegram user ID

==============================

requirements.txt

python-telegram-bot==20.3 requests

==============================

README.md

Safe URL Scanner Bot

A Telegram bot that checks if a URL is safe using a basic keyword and format checker.

Commands

/start - Welcome message

/scan <url> - Check if a URL is suspicious

/add <url> - Add URL to scan list (admin only)

/list - Show all added URLs


==============================

urls.txt

(This is the URL storage file. Start with empty or preload some)

https://example.com

==============================

bot.py

import logging from telegram import Update from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes from config import BOT_TOKEN, ADMIN_ID

URL_FILE = "urls.txt" logging.basicConfig(level=logging.INFO)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE): await update.message.reply_text("Welcome to the Safe URL Scanner Bot! Use /scan <url> to check a URL.")

async def scan(update: Update, context: ContextTypes.DEFAULT_TYPE): if not context.args: await update.message.reply_text("Please provide a URL to scan. e.g. /scan https://example.com") return url = context.args[0] if any(word in url for word in ["porn", "phish", "scam", "hack"]): await update.message.reply_text(f"🚨 Suspicious link detected: {url}") else: await update.message.reply_text(f"✅ This URL seems okay: {url}")

async def add(update: Update, context: ContextTypes.DEFAULT_TYPE): if update.effective_user.id != ADMIN_ID: await update.message.reply_text("You are not allowed to add URLs.") return if not context.args: await update.message.reply_text("Provide a URL to add. e.g. /add https://example.com") return url = context.args[0] with open(URL_FILE, "a") as f: f.write(url + "\n") await update.message.reply_text(f"Added {url} to the scan list.")

async def list_urls(update: Update, context: ContextTypes.DEFAULT_TYPE): try: with open(URL_FILE, "r") as f: urls = f.read() await update.message.reply_text("\u2728 Saved URLs:\n" + urls) except FileNotFoundError: await update.message.reply_text("No URLs saved yet.")

if name == 'main': app = ApplicationBuilder().token(BOT_TOKEN).build() app.add_handler(CommandHandler("start", start)) app.add_handler(CommandHandler("scan", scan)) app.add_handler(CommandHandler("add", add)) app.add_handler(CommandHandler("list", list_urls)) app.run_polling()

==============================

.github/workflows/url_scan.yml

name: Python Bot CI

on: push: branches: [ "main" ] pull_request: branches: [ "main" ]

jobs: build: runs-on: ubuntu-latest

steps:
- uses: actions/checkout@v3
- name: Set up Python
  uses: actions/setup-python@v4
  with:
    python-version: '3.10'

- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt

- name: Run bot (test)
  run: |
    echo "Testing complete"

