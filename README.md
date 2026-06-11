# Telegram Business Alerts Bot

A production-ready Telegram bot that sends business alert notifications. Responds to commands, sends scheduled briefings, monitors metric thresholds, and delivers CSV reports.

## Features

- **`/status`** — Live business metrics dashboard (revenue, orders, system health)
- **`/report`** — Full weekly performance report with channel/country breakdown
- **`/report_csv`** — Sends weekly data as an attached CSV file
- **`/alerts`** — Lists all currently active alerts
- **`/help`** — Command reference
- **Scheduled daily briefing** — Morning summary at a configurable time
- **Threshold monitoring** — Auto-alerts when revenue, error rate, or response time cross limits
- **Markdown formatting** — All messages use Telegram MarkdownV2

## Setup

### 1. Create a Telegram Bot

1. Open Telegram and message **@BotFather**
2. Send `/newbot` and follow the prompts
3. Copy the **bot token** you receive

### 2. Get Your Chat ID

1. Start a conversation with your new bot
2. Visit: `https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates`
3. Send any message to the bot, then refresh the URL
4. Copy the `"id"` value inside `"chat"` — that is your Chat ID

### 3. Configure the environment

```bash
cd telegram_bot
cp .env.example .env
```

Edit `.env` with your values:

```env
TELEGRAM_BOT_TOKEN=123456789:ABCdefGhIJKlmNoPQRsTUVwxyZ
TELEGRAM_CHAT_ID=987654321
REVENUE_THRESHOLD=10000
ERROR_RATE_THRESHOLD=5.0
RESPONSE_TIME_THRESHOLD=2000
DAILY_REPORT_TIME=09:00
```

### 4. Install dependencies and run

```bash
pip install -r requirements.txt
python bot.py
```

## Project Structure

```
telegram_bot/
├── bot.py           # Main bot — handlers, scheduler, entry point
├── formatters.py    # MarkdownV2 message formatters
├── data.py          # Data layer (simulated; swap for real sources)
├── requirements.txt
├── .env.example
└── README.md
```

## Connecting to Real Data

Replace the functions in `data.py` with your actual data sources:

```python
# Example: replace get_current_metrics() with a database query
def get_current_metrics() -> dict:
    conn = psycopg2.connect(os.getenv("DATABASE_URL"))
    cursor = conn.cursor()
    cursor.execute("SELECT SUM(amount) FROM orders WHERE date = CURRENT_DATE")
    revenue = cursor.fetchone()[0]
    # ... rest of your query
    return {"revenue_today": revenue, ...}
```

## Scheduled Alerts

| Schedule | What it sends |
|---|---|
| Daily at `DAILY_REPORT_TIME` | Morning business briefing |
| Every 30 minutes | Threshold checks (revenue / error rate / response time) |

Adjust intervals in `bot.py` in the `_start_scheduler()` function.

## Alert Levels

| Icon | Level | Triggered by |
|---|---|---|
| 🚨 | Critical | Error rate > threshold, payment failures |
| ⚠️ | Warning | Revenue below threshold, low inventory |
| ℹ️ | Info | Traffic spikes, informational events |

## Deployment

To keep the bot running permanently:

**Linux/macOS (systemd or screen):**
```bash
screen -S telegram-bot
python bot.py
# Ctrl+A then D to detach
```

**Docker:**
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "bot.py"]
```

**Environment variables** must be provided to the container via `-e` flags or a `.env` file mount.
