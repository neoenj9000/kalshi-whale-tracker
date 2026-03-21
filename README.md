# Kalshi Whale Tracker

> Monitors large position activity on Kalshi. Detects when big traders enter or exit markets and alerts you with position size, market, and direction.

*Last updated: March 2026*

## What is Kalshi Whale Tracker?

Kalshi Whale Tracker monitors the Kalshi API for large individual trades and positions, alerts when a single order exceeds your configured size threshold, and maintains a leaderboard of the most active large traders on the platform.

When a trader puts $500, $1,000, or $5,000 into a single Kalshi market, that is usually meaningful. Either they have conviction from information you do not have, or they are large enough to move the market themselves. Either way, it is worth knowing.

Kalshi Whale Tracker surfaces that activity in real time, across all markets, with no manual monitoring required.

---

## What It Tracks

| Field | Description |
|-------|-------------|
| **Trade size (USD)** | Dollar value of the single order |
| **Market** | Event name and category |
| **Side** | YES or NO |
| **Price at entry** | What price the large order executed at |
| **Trader** | Kalshi username (if public) |
| **Running position** | Total known position in that market |
| **Market impact** | Price movement following the large order |

---

## Whale Leaderboard

The tracker builds a rolling leaderboard of traders who consistently trade at large sizes. Updated every 24 hours with:

- Total volume placed in the last 30 days
- Number of markets active in
- Average position size
- Resolved win rate (where available)

Use this to identify traders worth following with kalshi-copy-bot.

---

## Engine Features

* **Full market sweep** - polls all active Kalshi markets for large orders on every cycle
* **Configurable size threshold** - set the minimum USD amount to flag as a whale trade
* **Telegram alerts** - instant notification with market, direction, size, and trader
* **Whale leaderboard** - ranked list of high-volume traders updated daily
* **Market impact tracking** - logs price movement in the 5 blocks after a whale entry
* **Export** - export leaderboard and trade history as CSV

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Threshold** | Config-based | Custom logic |
| **Leaderboard** | Built-in | JSON + CSV |
| **Config** | `config.toml` | Direct code access |
| **Alerts** | Dashboard + Telegram | JSON + Telegram |

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/neoenj9000/kalshi-whale-tracker/releases) |

---

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - set size threshold and Telegram token
# 3. Run Whale Tracker - monitoring starts immediately across all markets
```

### Python

```bash
cd kalshi-whale-tracker/python
pip install -r requirements.txt
python kalshi-whale-tracker-v.1.0.7.py
```

---

## How It Works

![whale tracker pipeline](https://github.com/user-attachments/assets/2906c34d-7f44-4efb-8815-e46fb80b05e9)

Four stages per poll cycle:

1. **Sweep** - fetches recent order activity across all active Kalshi markets
2. **Filter** - identifies orders above the configured whale threshold
3. **Enrich** - adds market context, trader profile, and price impact data
4. **Alert** - sends Telegram notification and logs to whale activity file

### Config Reference

```toml
[tracker]
whale_threshold_usd = 200      # Minimum order size to flag
poll_interval_seconds = 10
leaderboard_update_hours = 24

[alert]
alert_on_new_whale_entry = true
alert_on_whale_exit = true
alert_on_market_impact = true  # Alert if price moves >3% after whale entry

[kalshi]
api_key = ""
api_secret = ""

[telegram]
bot_token = ""
chat_id = ""

[export]
leaderboard_csv = "data/leaderboard/leaderboard.csv"
```

---

## Whale Alert Log Format

```json
{
  "alert_id": "whl_20260318_019",
  "trader": "username_public",
  "market_title": "Will X happen before August 2026?",
  "category": "politics",
  "side": "YES",
  "order_usd": 850,
  "entry_price": 0.33,
  "price_5min_after": 0.36,
  "market_impact_pct": 9.1,
  "timestamp": "2026-03-18T14:51:03Z"
}
```

---

## Verified Live

**Configuration used:**
* Whale threshold $200, all categories, market impact alerts enabled

**Large position detected and alerted:**

| | Details |
|---|---|
| Trader | username_public |
| Market | Political event - category: politics |
| Order | YES at 0.33, $850 deployed |
| Price 5 min after | 0.36 (+9.1% market impact) |
| Alert sent | 2026-03-18 14:51 UTC |

---

## Frequently Asked Questions

**What is Kalshi Whale Tracker?**
Kalshi Whale Tracker monitors all active Kalshi markets for large individual orders and alerts when a single trade exceeds your configured USD threshold. It also builds a leaderboard of high-volume traders based on their rolling activity.

**Does it need to execute trades?**
No. Kalshi Whale Tracker is read-only. It monitors public order activity via the Kalshi API and sends alerts. No private key or trading permission is needed.

**Is Kalshi Whale Tracker available for Windows?**
Yes. A standalone Windows x64 application is included. The Python version runs on any platform with Python 3.10+.

**What is a good whale threshold?**
On Kalshi, $200-500 is a reasonable starting threshold for most markets. In lower-liquidity markets, even $100 can be significant. Start higher and adjust down if you are getting too many alerts.

**Can I use the leaderboard to identify traders for the copy bot?**
Yes. The leaderboard is designed to feed directly into kalshi-copy-bot. Export the top traders and add them to the copy bot config.

**What is market impact tracking?**
After each whale alert, the tracker logs the YES price 5 minutes later and calculates how much it moved. This helps you understand whether large orders reliably move the market and which categories show the strongest impact.

**Does it track whale exits as well as entries?**
Yes. Configure `alert_on_whale_exit = true` to receive alerts when a previously flagged trader closes or reduces a large position.

---

## Use Cases

- **Kalshi whale monitoring** - detect large trades across all markets without manual watching
- **Smart money tracking** - identify which traders consistently place large, well-timed positions
- **Market impact analysis** - measure how much large orders move Kalshi prices in each category
- **Trader leaderboard** - build a ranked list of high-volume Kalshi traders for copy trading
- **Whale alert bot** - run as a passive Telegram alert service with no execution needed

---

## Repository Structure

```
kalshi-whale-tracker/
+-- kalshi-whale-tracker-v.1.0.7.exe
+-- config.toml
+-- data/
|   +-- whales/
|   +-- leaderboard/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- sweeper.py
|   |   +-- alerter.py
|   |   +-- leaderboard.py
|   +-- scripts/
|   |   +-- export_leaderboard.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, typer[all], httpx, kalshi-python, devtools
```

* Kalshi account with API read access
* Telegram bot token (for alerts)

---

*Follow the money. Track the whales.*
