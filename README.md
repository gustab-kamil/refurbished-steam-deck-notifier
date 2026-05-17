[![Latest Release](https://img.shields.io/github/v/release/oblassgit/refurbished-steam-deck-notifier?include_prereleases)](https://github.com/oblassgit/refurbished-steam-deck-notifier/releases)
[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/github/license/oblassgit/refurbished-steam-deck-notifier)](https://github.com/oblassgit/refurbished-steam-deck-notifier/blob/main/LICENSE)  
[![GitHub Stars](https://img.shields.io/github/stars/oblassgit/refurbished-steam-deck-notifier?style=social)](https://github.com/oblassgit/refurbished-steam-deck-notifier/stargazers)
[![Forks](https://img.shields.io/github/forks/oblassgit/refurbished-steam-deck-notifier?style=social)](https://github.com/oblassgit/refurbished-steam-deck-notifier/network/members)
[![Discord](https://img.shields.io/discord/1142517154370043974?label=Discord&logo=discord&style=flat)](https://discord.gg/5gpFTMkvJn)
[![Ko-fi](https://img.shields.io/badge/Buy%20me%20a%20coffee-Ko--fi-FF5E5B?logo=kofi&logoColor=white&style=flat)](https://ko-fi.com/looti)
# Refurbished Steam Deck Notifier

This script checks the availability of refurbished Steam Decks on Steam and sends notifications when stock availability changes. It supports both **Discord webhook** and **ntfy.sh** notification backends, and compares the current stock status with previously stored values.

## 🚀 Features

* Checks the availability of refurbished Steam Decks for a configurable country
* Sends notifications via **Discord webhook** or **ntfy.sh**
* Supports different Steam Deck models (LCD & OLED versions)
* Prevents duplicate notifications by storing the last known stock status
* **Optional CSV logging** for availability statistics
* **Configurable Discord role pings** via JSON file
* **Command-line arguments** for easy configuration
* **Wrapper script** for topic-specific ntfy checks

## 📋 Requirements (for Python script users)

### Install Dependencies

Ensure you have **Python 3.x** installed. Then install the required dependencies using:

```bash
pip install -r requirements.txt
```

## 🛠 Setup & Usage

### Option 1: Use the Prebuilt Executable (No Python Needed)

Download the prebuilt executable for your platform (Windows, Linux, etc.). The file is typically named:

```
steam_deck_notifier.exe (Windows)
steam_deck_notifier (Linux/macOS)
```

#### How to Run

Run it via terminal/command prompt:

```bash
./steam_deck_notifier --webhook-url "https://discord.com/api/webhooks/YOUR_WEBHOOK"
```

You can pass the same arguments as you would for the Python version.

### Option 2: Run the Python Script

```bash
python notifier.py --webhook-url "https://discord.com/api/webhooks/YOUR_WEBHOOK"
```

### Command Line Arguments

* `-h`: List available arguments
* `--notify-backend`: Notification backend (`discord` or `ntfy`, default: `discord`)
* `--webhook-url`: Discord webhook URL for notifications
* `--webhook-url-new`: Separate Discord webhook for new model notifications (optional)
* `--ntfy-topic`: ntfy.sh topic name to post notifications to
* `--ntfy-server`: ntfy server base URL (default: `https://ntfy.sh`)
* `--ntfy-token`: Bearer token for ntfy (optional)
* `--country-code`: Country code for Steam API (default: `DE`)
* `--role-mapping`: JSON file containing Discord role mappings (optional)
* `--csv-dir`: Directory path for daily CSV log files (optional)

### Full Example

```bash
python notifier.py \
  --notify-backend ntfy \
  --ntfy-topic YOUR_TOPIC \
  --country-code US \
  --csv-dir csv-logs
```

### Discord Role Mapping (Optional)

Create a `roles.json` file like this to ping specific Discord roles when stock is available:

```json
{
  "903905": "1343233406791716875",
  "903906": "1343233552896229508",
  "903907": "1343233731795881994",
  "1202542": "1343233909655343234",
  "1202547": "1343234052957802670"
}
```

**Format:** `"package_id": "discord_role_id"`

### Country Codes

Find valid country codes [here](https://github.com/RudeySH/SteamCountries/blob/master/json/countries.json)

## 💪 Steam Deck Models Monitored

The script checks availability for these models:

* **64GB LCD** (Package ID: 903905)
* **256GB LCD** (Package ID: 903906)
* **512GB LCD** (Package ID: 903907)
* **512GB OLED** (Package ID: 1202542)
* **1TB OLED** (Package ID: 1202547)

## 🔧 How It Works

1. Requests stock status for Steam Deck models via Steam’s API
2. Compares new status with the last known state stored in text files
3. Sends a notification if availability changes
4. Optionally pings configured roles via `roles.json`
5. Optionally logs the check results to a CSV file

## 📊 CSV Logging

When using `--csv-dir`, the script writes one CSV file for each day to the specified directory, with these fields:

* `unix_timestamp`: Time of check
* `storage_gb`: 64, 256, 512, or 1024
* `display_type`: LCD or OLED
* `package_id`: Steam product identifier
* `available`: `True` or `False`

## ⏲️ Running Periodically

This script/executable **does not run continuously**. Use cron (Linux/macOS) or Task Scheduler (Windows) to automate execution.

### Example (Linux/macOS) for notifier.py

Edit your crontab with:

```bash
crontab -e
```

Add this line to check every 5 minutes via ntfy:

```bash
*/5 * * * * /usr/bin/python3 /home/kamil/private-repos/refurbished-steam-deck-notifier/notifier.py \
  --notify-backend ntfy \
  --ntfy-topic kamil-steamdeck \
  --country-code DE >> /home/kamil/private-repos/refurbished-steam-deck-notifier/ntfy-check.log 2>&1
```

### Example wrapper script usage

If you prefer to use the included wrapper for the ntfy topic, run:

```bash
python run_steamdeck_ntfy.py
```

Add this wrapper to cron with:

```bash
*/5 * * * * /usr/bin/python3 /home/kamil/private-repos/refurbished-steam-deck-notifier/run_steamdeck_ntfy.py >> /home/kamil/private-repos/refurbished-steam-deck-notifier/ntfy-check.log 2>&1
```

## 📦 Dependencies & Attribution

This project uses the excellent [**python-discord-webhook**](https://github.com/lovvskillz/python-discord-webhook) library by [lovvskillz](https://github.com/lovvskillz)
Licensed under the MIT License.

It also makes use of Valve’s public Steam Store API — specifically the  
[`CheckInventoryAvailableByPackage`](https://api.steampowered.com/IPhysicalGoodsService/CheckInventoryAvailableByPackage/v1?origin=https:%2F%2Fstore.steampowered.com) endpoint ([documentation](https://steamapi.xpaw.me/#IPhysicalGoodsService)). Data and trademarks belong to [Valve Corporation](https://www.valvesoftware.com/), owners of Steam and Steam Deck.

Big thanks to all contributors and maintainers of the open-source packages used in this project.

## ❤️ Support

If this project helps you, consider supporting via [**Ko-fi**](https://ko-fi.com/Y8Y41BZ8SM)

## 🥇 Special Thanks

Huge thanks to [leo-petrucci](https://github.com/leo-petrucci) for helping improve the codebase and guiding proper Steam API usage!
