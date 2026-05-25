# 04 — AI Lead Enrichment Pipeline

An n8n workflow that takes a CSV of business leads, enriches them with company
data, analyzes their website using AI, scores them against predefined criteria,
generates personalized cold email intros, and pushes qualified leads into
Instantly for outreach — fully automated.

## Problem It Solves

Manually researching leads, scoring fit, and writing personalized cold emails
is slow and doesn't scale. This workflow processes an entire CSV in one run:
each lead gets enriched, scraped, scored by AI, and either pushed to your
outreach campaign with a custom intro — or logged as disqualified with a reason.

## Workflow Architecture

```
CSV File → Parse & Split
        │
  Hunter.io Enrichment
  (company size, industry, email pattern)
        │
  Website Scraper
  (fetches homepage, strips HTML → plain text)
        │
  Groq AI Scoring
  (Llama 3.3 70B — scores 1–10 against criteria)
        │
  Score Gate (Switch node)
        │
  ├── score ≥ 7 (qualified)
  │       │
  │   Groq AI Email Generation
  │   (personalized subject line + cold intro)
  │       │
  │   Push to Instantly API V2
  │   (campaign_id + personalization field)
  │       │
  │   Log to Google Sheets → Qualified tab
  │
  └── score < 7 (disqualified)
          │
      Log to Google Sheets → Disqualified tab
      (with reason and score)
```

## Features

- CSV ingestion with automatic parsing and batch processing (one lead at a time,
  rate-safe)
- Hunter.io domain search for company industry, size, and email pattern
- Live website scraping via HTTP — no external browser service needed for most sites
- Groq AI (Llama 3.3 70B) scoring against home remodeling ICP criteria
- AI-generated personalized subject line and cold email opener per qualified lead
- Instantly API V2 bulk push with AI intro in the `personalization` field and
  score stored in `custom_variables`
- All leads logged to Google Sheets — qualified and disqualified separately
- Built-in sample data for testing without a real CSV

## Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow automation |
| Groq API (llama-3.3-70b-versatile) | Lead scoring + email generation |
| Hunter.io API | Company enrichment |
| HTTP Request node | Website scraping |
| Switch node | Score-based routing |
| Instantly API V2 | Outreach campaign push |
| Google Sheets | Logging (qualified + disqualified) |

## AI Scoring Output

Each lead is scored with:

| Field | Description |
|---|---|
| `score` | 1–10 fit score (≥ 7 = qualified) |
| `qualified` | true / false |
| `industry_match` | strong / partial / none |
| `business_type` | e.g. "kitchen remodeler" |
| `key_signals` | 1–3 phrases that drove the score |
| `disqualify_reason` | Why the lead was skipped (if applicable) |

## AI Email Output

Each qualified lead gets:

| Field | Description |
|---|---|
| `subject_line` | Specific, under 60 chars, no clickbait |
| `email_intro` | 2–3 sentences referencing their business |

## Setup Instructions

### Prerequisites

- n8n instance (cloud or self-hosted)
- Groq API key — free at [console.groq.com](https://console.groq.com)
- Hunter.io API key — free tier (25 searches/month) at [hunter.io](https://hunter.io)
- Instantly account with a campaign created
- Google Sheets with two tabs: `Qualified Leads` and `Disqualified Leads`

### Steps

1. Import `04-AI-Lead-Enrichment.json` into n8n
2. Replace all placeholders in the Code nodes:

| Placeholder | Replace with |
|---|---|
| `YOUR_GROQ_API_KEY_HERE` | Your Groq API key (in Score Lead and Generate Email Intro nodes) |
| `YOUR_HUNTER_API_KEY_HERE` | Your Hunter.io API key |
| `YOUR_INSTANTLY_API_KEY_HERE` | Instantly → Settings → API → V2 key (needs `leads:all` scope) |
| `YOUR_CAMPAIGN_ID_HERE` | Your Instantly campaign ID |
| `YOUR_GOOGLE_SHEET_ID_HERE` | From your Google Sheets URL |

3. Connect your existing Google Sheets OAuth2 credential to the two log nodes
4. Click **Execute manually** to run with the built-in sample data first
5. Once confirmed working, upload your real CSV as binary input and re-run

### Testing Without a CSV

The Parse CSV node includes 3 sample home remodeling companies. Just hit
**Manual Trigger** — no file upload needed for your first test.

### Testing With a Real CSV

Your CSV should have these columns (extras are fine, they pass through):

```
company_name, first_name, last_name, email, domain, phone, city, state
```

## Notes

- Hunter.io free tier: 25 searches/month — sufficient for demos. Production
  use needs a paid plan or swap for Apollo.io
- Groq free tier: very generous limits, suitable for batches of hundreds of leads
- Instantly API V2 only — V1 keys will not work
- `custom_variables` values must be strings (Instantly requirement) — the
  workflow handles this automatically

## Part of the n8n Automation Portfolio by Basant Mote
