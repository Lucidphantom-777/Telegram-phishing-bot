

🧠 Overview

This is a Telegram‑controlled phishing tool that gives each subscribed user their own unique phishing link (via Serveo tunnel). Every user can:

· Choose their own login page template (e.g., Instagram, Facebook, etc.).
· Start/stop their own Flask server and tunnel.
· Receive real‑time credential alerts on Telegram.
· Manage subscriptions (free trial, then paid plans via admin).

The bot uses SQLite to store credentials and user subscriptions. It runs entirely on your local machine or server – no cloud hosting required.

---

🔧 Features

Feature Description
Multi‑user Each Telegram user gets a separate port and Serveo URL.
Per‑user templates Users can choose any HTML template from the templates/ folder.
Subscription system 24‑hour free trial, then 1‑day, 7‑day, 30‑day paid plans.
Admin panel Grant/revoke subscriptions, list all users.
Live credential alerts Every captured credential is sent to the admin(s) instantly.
Self‑contained No external databases – uses SQLite.
Automatic port allocation Finds free ports starting from 5001.
Serveo tunnels Provides public HTTPS links via SSH reverse tunnels (no need for ngrok).

---

📦 Prerequisites

· Python 3.8+ (3.10 recommended)
· Git (optional, for version control)
· SSH (to connect to Serveo)
· Termux (if on Android) or any Linux/macOS/Windows with Python.

---

📁 Project Structure

```
phishing-bot/
├── phish.py              # Your main script
├── templates/            # Folder containing HTML login pages
│   ├── instagram.html
│   ├── facebook.html
│   └── ... (any number)
├── instance/             # Created automatically; stores SQLite DB
│   └── creds.db
└── requirements.txt      # (optional) List of dependencies
```

---

🛠️ Setup Instructions

1. Install Dependencies

```bash
pip install flask requests python-telegram-bot
```

If you use a requirements.txt, add:

```
flask
requests
python-telegram-bot
```

2. Get Your Telegram Bot Token

· Open Telegram, search for @BotFather.
· Send /newbot and follow steps to create a bot.
· Copy the token (e.g., 1234567890:ABCdefGHIjklMNOpqrsTUVwxyz).

3. Get Your Telegram User ID (for Admin)

· Send /start to your bot or use @userinfobot to get your numeric ID.
· Example: 123456789

4. Edit the Configuration in phish.py

Find these lines near the top:

```python
BOT_TOKEN = "your bot token "
ADMIN_IDS = [your telegram ID]
```

Replace them with your own token and admin ID(s). For multiple admins, use [123456, 789012].

5. Add Your HTML Templates

Place all your login page HTML files (e.g., instagram.html, facebook.html) into the templates/ folder. Each must have a form that POSTs to /login with fields named username and password. The tool will automatically detect them.

---

🚀 Running the Bot

Local Machine / Termux

```bash
python phish.py
```

You will see:

```
==================================================
      PHISHING TOOL WITH TELEGRAM BOT
      MULTI‑USER LINKS VERSION
==================================================
Found templates: instagram, facebook, ...
Default template: instagram
Bot is starting...
Press Ctrl+C to stop.
🤖 Telegram bot is running...
```

The bot is now active and responds to Telegram commands.

---

🤖 Telegram Commands

Command Description
/start Show welcome message, subscription status, and available commands.
/templates List all available login page templates.
/select <name> Choose a template (e.g., /select facebook).
/startserver Start your own phishing server (allocates a port and Serveo tunnel).
/stopserver Stop your running server.
/buy Display purchase options and contact owner.
/creds Show last 5 captured credentials.
/status Show your current subscription, template, and link.
/help Show help message.

Admin Commands (only for users in ADMIN_IDS)

Command Description
/admin Open admin panel with buttons.
/grant <user_id> <plan> Activate subscription (plans: 1day, 7day, 30day).
/revoke <user_id> Revoke a user's subscription.

---

🧠 How It Works – Architecture

1. Flask Server per User

· When a user runs /startserver, the bot:
  · Checks subscription status.
  · Finds a free port (starting from 5001).
  · Starts a separate Flask process on that port, serving the user’s selected template.
  · Launches a Serveo tunnel for that port, giving a public HTTPS URL.

2. Database

· instance/creds.db contains two tables:
  · credentials – stores captured login data (platform, username, password, IP, user‑agent, timestamp).
  · users – stores Telegram user info, subscription start/end, plan, and admin flag.

3. Credential Capture

· The login page submits to /login (inside the Flask app).
· The route saves credentials to the database and sends an alert to the admin(s) via Telegram.

4. Subscription & Free Trial

· New users get a 24‑hour free trial automatically on /start.
· Paid plans are activated manually by an admin via /grant.
· /startserver is blocked if the user’s subscription has expired.

5. Multi‑Process Management

· Each user’s Flask server runs in its own multiprocessing.Process.
· The bot tracks these processes in user_sessions and terminates them when the user runs /stopserver.

---

🧪 Testing

1. Start the bot and send /start to your bot.
2. Send /templates to list available pages.
3. Send /select instagram (or any other).
4. Send /startserver. The bot will return a Serveo URL.
5. Open that URL in a browser – you’ll see your chosen login page.
6. Submit a test login. You’ll receive an alert on Telegram and the credential will appear in /creds.

---

⚙️ Customisation Tips

· Add more templates: Just drop .html files into templates/ – they appear automatically in /templates.
· Change pricing: Edit the /buy command text and the plan_map dictionary in the grant_cmd function.
· Modify trial duration: Change hours=24 in create_user() to any number.
· Use different tunnel: Replace start_serveo() with ngrok or another tool (but Serveo works well on mobile).

---

⚠️ Important Notes

· Serveo requires SSH – ensure SSH is installed (apt install openssh-client on Linux, pkg install openssh on Termux).
· Ports – the bot uses ports from 5001 upward. If you have firewalls, ensure outgoing connections are allowed.
· Security – This tool is for educational and authorised testing only. Do not use it maliciously. Always get explicit permission before testing on any system you don’t own.
· Multi‑user concurrency – Each user gets a separate process; on low‑end devices, many simultaneous servers may slow things down. The bot handles up to ~10 users comfortably.

---

🛑 Stopping the Bot

Press Ctrl+C in the terminal. The bot will clean up all running processes before exiting.

---

📌 Summary

You now have a fully functional, multi‑user phishing bot that:

· Runs on your local machine or any server.
· Gives each user a unique link.
· Manages subscriptions and templates.
· Sends real‑time credential alerts via Telegram.
· Is self‑contained with SQLite.

All you need to do is customise the token and admin IDs, add your templates, and run it.

