[README.md](https://github.com/user-attachments/files/25788274/README.md)
# AI-Powered Gmail Auto-Labeler

An intelligent email classification tool that leverages **Claude AI (Anthropic)** to automatically analyze and label Gmail emails based on sender, subject, and content — replacing hours of manual inbox organization with a single command.

## Overview

Managing a cluttered inbox with hundreds of unlabeled emails is time-consuming. This tool automates the entire process:

1. **Connects** to your Gmail account via OAuth2
2. **Fetches** up to 500+ emails with metadata (sender, subject, preview)
3. **Sends** each email's context to Claude AI with a structured prompt
4. **Creates** Gmail labels dynamically if they don't exist
5. **Applies** the AI-suggested label to each email

The result: a fully organized inbox in minutes, with consistent, intelligent categorization.

## Architecture

```
┌──────────────┐     OAuth2      ┌──────────────┐
│   Gmail API  │◄───────────────►│  Python App  │
│  (REST API)  │   fetch/label   │              │
└──────────────┘                 │  - Auth Flow │
                                 │  - Caching   │
┌──────────────┐   Prompt Eng.   │  - Error     │
│  Claude API  │◄───────────────►│    Handling  │
│  (Anthropic) │   classify      │              │
└──────────────┘                 └──────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Language | Python 3.10+ |
| LLM | Claude (Anthropic API) — `claude-haiku-4-5-20251001` |
| Email API | Gmail REST API v1 |
| Authentication | Google OAuth2 (offline access, token refresh) |
| Prompt Design | Structured prompt engineering with role, rules & constraints |
| Config | python-dotenv for environment variable management |

## Key Features

- **LLM-Powered Classification** — Claude AI analyzes sender, subject, and content preview to assign contextual labels (e.g., Finance, Newsletter, Shopping, Work)
- **Prompt Engineering** — Structured prompt with role assignment, output rules, examples, and constraints for consistent single-word/phrase labels
- **OAuth2 Authentication** — Full Google auth lifecycle: first-time browser login, token persistence, and automatic refresh on expiry
- **Label Caching** — In-memory cache avoids redundant Gmail API calls when the same label is reused across emails
- **Dynamic Label Creation** — New labels are created on-the-fly in Gmail if Claude suggests one that doesn't exist yet
- **Duplicate Prevention** — Checks existing labels before applying to avoid duplicates
- **Error Resilience** — Try/except per email ensures one failure doesn't stop the batch
- **Scalable** — Handles pagination for 500+ emails with `list_next`

## Setup

### Prerequisites

- Python 3.10+
- A Google Cloud project with Gmail API enabled
- An Anthropic API key

### 1. Clone the repository

```bash
git clone https://github.com/aharkane/gmail-ai-labeler.git
cd gmail-ai-labeler
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure Google OAuth2

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a project and enable the **Gmail API**
3. Create **OAuth 2.0 credentials** (Desktop application)
4. Download the JSON file and save it as `credentials.json` in the project root

### 4. Set your Anthropic API key

```bash
cp .env.example .env
```

Edit `.env` and add your key:

```
ANTHROPIC_API_KEY=your-anthropic-api-key-here
```

### 5. Run

```bash
python label_emails.py
```

On first run, a browser window will open for Gmail authorization. After that, the token is saved locally and refreshes automatically.

## Usage

```
=== Gmail AI Labeler ===

Authenticating with Gmail...
✅ Gmail authenticated!

How many emails do you want to process? (e.g. 100, 500, 1000): 100

✅ Found 100 emails to process.

[1/100] 'Your Amazon order has shipped...' → Shopping
[2/100] 'Invoice #4521 from Stripe...' → Finance
[3/100] 'Weekly Python Newsletter...' → Newsletter
...
✅ Done! All emails have been labeled.
```

## Project Structure

```
gmail-ai-labeler/
├── label_emails.py      # Main script
├── credentials.json     # Google OAuth2 credentials (not tracked)
├── token.json           # Gmail auth token (not tracked)
├── .env                 # API keys (not tracked)
├── .env.example         # Template for environment variables
├── .gitignore           # Blocks sensitive files from Git
├── requirements.txt     # Python dependencies
└── README.md
```

## Security

Sensitive files are excluded from version control via `.gitignore`:
- `credentials.json` — Google OAuth client secrets
- `token.json` — Gmail access and refresh tokens
- `.env` — Anthropic API key

**Never commit these files to Git.** Use `.env.example` as a template.

## License

MIT
