# PMBC Centris Automation & Dashboard Workflow

[![Python Version](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Challenge: PMBC AI & Automation](https://img.shields.io/badge/PMBC-Hiring%20Challenge-00D1B2.svg)](#)

A state-of-the-art automated real estate ETL (Extract, Transform, Load) pipeline engineered specifically for the **PMBC Inc. Computer Engineer (AI, Automation & Dashboard Expertise) technical hiring assessment**. 

This system automatically harvests Quebec real estate inventory from **Centris.ca**, extracts deep financial metrics (municipal assessments and tax evaluations), synchronizes records in real-time with **Google Sheets** acting as a relational database with automated status indicator evaluation (`NEW` vs `UNCHANGED`), and dispatches rich, interactive notifications to a **Telegram Channel** via a custom bot.

---

## 🏛️ System Architecture & Key Features

```
[ Centris.ca Real Estate Portal ]
          │ (Two-Phase Extraction Pipeline)
          ▼
┌──────────────────────────────────────────────┐
│  Phase 1: General Info Scraping (AJAX/HTML)  │
│  (Type, Address, Price, Beds, Baths, URL)    │
└──────────────────────┬───────────────────────┘
                       │ (Deep Property Links)
                       ▼
┌──────────────────────────────────────────────┐
│  Phase 2: Detailed Financial Crawler         │
│  (Land/Bldg/Total Assessment & Municipal Tax)│
└──────────────────────┬───────────────────────┘
                       │ (Structured JSON/Dicts)
                       ▼
┌──────────────────────────────────────────────┐
│  Database Sync Engine & Status Evaluator     │
│  (Deduplication -> Assigns 'NEW' Status Tag) │
└──────────────┬───────────────────────────────┘
               │
       ┌───────┴───────────────┐
       ▼                       ▼
[ Google Sheets API ]   [ Telegram Channel Bot ]
 (Live Row Insertion)    (Rich HTML Broadcasts)
```

1. **Two-Phase Scraper Engine:**
   - **Phase 1 (General Metadata):** Interfaces with Centris listings and AJAX query endpoints to extract foundational attributes: Property Type, Postal Address, Asking Price, Bedroom/Bathroom count, and Listing ID.
   - **Phase 2 (Deep Financial Crawling):** Navigates into each specific property URL to extract granular municipal assessment valuations (Land, Building, Total Assessment) and taxation figures (Municipal Tax, School Tax, Annual Total).
2. **Google Sheets Real-Time Relational Database:**
   - Connects via Google Service Account credentials (`gspread` / `oauth2client`).
   - Automatically cross-references extracted IDs against existing rows to assign status tags (`NEW`, `UNCHANGED`).
   - Features an integrated fallback local CSV/SQLite synchronization layer (`centris_database_backup.csv`) to ensure flawless demonstrations even in network-restricted environments.
3. **Automated Telegram Bot Broadcasting:**
   - Formats listings flagged as `NEW` into clean, structured HTML alerts with bold price highlights, assessment summaries, and clickable deep links.
   - Includes a rich terminal visual mockup renderer for video recording demonstrations without active Telegram tokens.
4. **Interval Scheduler:**
   - Designed to run continuously in the background every 15–30 minutes, capturing newly posted market inventory autonomously.
5. **Presentation Simulator (`demo_runner.py`):**
   - Engineered for your required 4-to-5-minute submission recording. Synchronously orchestrates and narrates all workflow phases with visual pacing and formatted ASCII summary tables!

---

## 🛠️ Installation & Setup Manual

### 1. Requirements & Dependencies

Open terminal inside the project directory and install required Python packages:
```powershell
pip install -r requirements.txt
```

### 2. Configuration (`.env`)

Copy `.env.example` to `.env` and adjust your environment variables:
```ini
GOOGLE_SHEET_TITLE=Centris Real Estate Database
GOOGLE_SERVICE_ACCOUNT_FILE=credentials.json
TELEGRAM_BOT_TOKEN=your_bot_token_from_botfather
TELEGRAM_CHANNEL_ID=@your_channel_username
SCRAPE_INTERVAL_MINS=20
USE_DEMO_DATA=True
```

> [!TIP]
> **Network Restrictions / VPN Considerations (Iran & Region Lock Bypass):**
> Centris.ca deploys bot mitigation and regional IP blocking (requiring a VPN for connections from regions like Iran). By default, `USE_DEMO_DATA=True` enables our high-fidelity Montreal simulation mode. This guarantees that your live presentation recording and terminal logs run smoothly with zero network lag or Cloudflare interruptions! To test real network crawling over your VPN, simply set `USE_DEMO_DATA=False` in `.env`.

---

## 📊 Google Sheets API Credentials Setup (Optional for Demo)

To populate a real Google Sheet live during your screen recording:
1. Go to the [Google Cloud Console](https://console.cloud.google.com/) and create a new project.
2. Enable the **Google Sheets API** and **Google Drive API**.
3. Under **IAM & Admin > Service Accounts**, create a new Service Account and generate a JSON key.
4. Rename the downloaded JSON file to `credentials.json` and place it in the project root.
5. Create a Google Sheet named `Centris Real Estate Database`, click **Share**, and share edit permissions with the `client_email` found inside your `credentials.json`!

---

## 🤖 Telegram Bot Configuration (Optional for Demo)

1. Open Telegram and search for `@BotFather`.
2. Send `/newbot`, give it a name and username, and copy your HTTP API Token into `.env` (`TELEGRAM_BOT_TOKEN`).
3. Create a public or private Telegram Channel for your demo.
4. Add your newly created Bot as an **Administrator** to the channel with permission to post messages.
5. Set your channel handle (e.g. `@pmbc_centris_demo` or `-100xxxxxx`) as `TELEGRAM_CHANNEL_ID` in `.env`.

---

## 🚀 Running the Workflow

### Option A: Presentation Demo Simulator (Recommended for Recording!)
Designed specifically for your 4-to-5-minute submission video. Executes immediately with narrated visual stages, populating database tables and showing Telegram alerts in real time:
```powershell
python demo_runner.py
```

### Option B: Production Automated Scheduler
Runs the live pipeline immediately and configures an interval background daemon (defaulting to every 20 minutes):
```powershell
python main.py
```

---

## 🧪 Testing and Verification
To run offline verification of the scraping and parser modules:
```powershell
python -m unittest tests/test_parser.py
```

To test real Telegram Bot network connectivity directly from your terminal:
```powershell
python -c "from notifier.telegram_bot import test_connection; test_connection()"
```

---
*Engineered for PMBC Inc. Technical Assessment - Excellence in Automation & AI Workflow Solutions.*
