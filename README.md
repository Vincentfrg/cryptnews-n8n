# 🚀 CryptNews — Automated Crypto Daily Report

> An automated workflow built with **n8n** that fetches real-time crypto market data, analyzes it with **Groq AI (Llama 3.3 70B)**, and sends a styled daily HTML report to your inbox every morning at 9am.

![Workflow](assets/workflow.png)

---

## 📋 What it does

Every morning at **9:00 AM**, the workflow:

1. **Fetches** the Top 10 cryptocurrencies by trading volume from CoinGecko (with 24h & 7d variations)
2. **Fetches** Pi Network price history over the last 48 hours
3. **Merges** both data sources and filters tokens with incomplete data
4. **Formats** the data into a clean market summary
5. **Analyzes** it with Groq AI (Llama 3.3 70B) to generate a detailed French-language report
6. **Sends** a styled HTML email to your inbox via Gmail SMTP

---

## 🧩 Workflow Architecture

```
Schedule Trigger (9h)
    ├── HTTP_Req_AllCrypto (CoinGecko Top 10 by volume)
    ├── HTTP_Req_PI (Pi Network 48h chart)
    ├── Merge (Append)
    ├── Format_report (Code — data formatting + AI prompt)
    ├── HTTP_Req_Groq (Llama 3.3 70B analysis)
    ├── Format_mail (Code — HTML email generation)
    └── Send Email (Gmail SMTP)
```

---

## 🛠️ Tech Stack

| Tool | Usage |
|---|---|
| **n8n** | Workflow automation |
| **CoinGecko API** | Crypto market data — free, no API key required |
| **Groq API** | AI analysis with Llama 3.3 70B — free tier |
| **Gmail SMTP** | HTML email delivery |

---

## 📦 Prerequisites

- [n8n](https://n8n.io) — self-hosted via Docker or n8n Cloud
- [Groq API key](https://console.groq.com) — free account
- Gmail account with [App Password](https://myaccount.google.com/apppasswords) enabled (requires 2FA)

---

## 🚀 Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/Vincentfrg/cryptnews-n8n.git
cd cryptnews-n8n
```

### 2. Start n8n with Docker

```bash
docker run -it --rm \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

Open [http://localhost:5678](http://localhost:5678)

### 3. Import the workflow

In n8n: **Menu (⋯) → Import from file** → select `workflow.json`

### 4. Configure credentials

#### Groq API
- In the `HTTP_Req_Groq` node → Edit credential
- **Type** → Header Auth
- **Name** → `Authorization`
- **Value** → `Bearer YOUR_GROQ_API_KEY`

#### Gmail SMTP
- In the `Send Email` node → Create new credential
- **Host** → `smtp.gmail.com`
- **Port** → `465`
- **SSL** → enabled
- **User** → `your.email@gmail.com`
- **Password** → Your Gmail App Password (16 characters)

### 5. Update your email

In the `Send Email` node, update:
- **From** → `your.email@gmail.com`
- **To** → `your.email@gmail.com`

### 6. Activate the workflow

Toggle the workflow to **Active** — it will run every day at 9:00 AM.

---

## 📧 Email Preview

The report is delivered as a styled HTML email with:
- Dark header with terminal aesthetic
- Full AI-generated market analysis (no inline source citations)
- Clickable source buttons (CoinGecko, CoinMarketCap, Binance, Reuters, CryptoPanic, Pi Network)
- Branded footer linking to the author's portfolio

---

## 📊 Data Coverage

- **Top 10 cryptocurrencies** by 24h trading volume — filtered to remove tokens with incomplete data
- **Price variations** over 24h and 7 days
- **Pi Network** dedicated 48h price chart analysis
- **AI analysis** covering market trend, key crypto highlights, Pi Network, and short-term outlook

---

## 🔧 Customization

### Change the number of cryptos tracked
Edit the `HTTP_Req_AllCrypto` URL — increase `per_page`:
```
https://api.coingecko.com/api/v3/coins/markets?vs_currency=eur&order=volume_desc&per_page=20&page=1&price_change_percentage=24h,7d
```

### Change the currency
Replace `vs_currency=eur` with `vs_currency=usd` for USD pricing.

### Change the report language
Edit the prompt in `Format_report` — replace `en français` with your target language.

### Change the send time
Edit the `Schedule Trigger` node → update the **Hour** field.

### Add more specific coins
Duplicate the `HTTP_Req_PI` node pattern with any CoinGecko coin ID:
```
https://api.coingecko.com/api/v3/coins/{coin-id}/market_chart?vs_currency=eur&days=2
```

---

## 📁 Project Structure

```
cryptnews-n8n/
├── README.md               # This file
├── CryptNews.json           # n8n workflow export (v2 with HTML template)
├── CryptNews_old.json      # v1 archive
├── .gitignore              # Ignores .env files
└── assets/
    └── workflow.png        # Workflow screenshot
```

---

## ⚠️ Security

- Never commit your API keys or app passwords
- The `workflow.json` does not contain credentials — they are stored locally by n8n
- All email addresses have been replaced with `YOUR_EMAIL@gmail.com` in the exported file

---

## 📄 License

MIT — feel free to use, modify and share.

---

## 👤 Author

**Vincent Ferrag**
- GitHub: [@Vincentfrg](https://github.com/Vincentfrg)
- Portfolio: [vincentfrg.vercel.app](https://vincentfrg.vercel.app)