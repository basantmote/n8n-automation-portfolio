# 03 — AI Lead Router

An n8n workflow triggered by a contact form webhook that classifies incoming inquiries using Groq AI, routes them by category (urgent/regular/newsletter/spam), sends Slack alerts for hot leads, auto-replies to regular inquiries, and logs everything to Google Sheets.

## Problem It Solves

When you run a freelance or small business, not all inbound messages deserve the same response time. Manually triaging contact form submissions is slow and leads to missed opportunities. This workflow reads every submission, scores it with AI, routes it to the right channel, and handles the response — all within seconds of the form being submitted.

## Workflow Architecture

```
Contact Form → Webhook
        │
  Groq AI Classification
  (urgent / regular / newsletter / spam)
  + urgency score + budget signals
        │
   Switch — Route by Category
        │
   ├── urgent    → Slack alert to you (immediate notification)
   ├── regular   → AI-generated auto-reply email + log to Sheets
   ├── newsletter→ Log to Sheets (newsletter list)
   └── spam      → Log to Sheets (spam log, no reply)
```

## Features

- Webhook endpoint accepts any contact form POST payload
- AI classifies each inquiry into one of 4 categories
- Scores urgency (1–10) and extracts budget signals from message text
- Instant Slack notification for urgent/high-value leads
- Auto-reply email drafted and sent for regular inquiries (AI-generated, personalized)
- All leads logged to separate Google Sheets tabs by category
- No lead falls through the cracks — spam is logged, not just dropped

## Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow automation |
| Webhook node | Receives form submissions |
| Groq API (llama-3.3-70b-versatile) | Lead classification + reply generation |
| Switch node | Category-based routing |
| Slack | Urgent lead notifications |
| Gmail | Auto-reply emails |
| Google Sheets | CRM-style logging |

## AI Classification Output

Each submission is scored with:

| Field | Description |
|---|---|
| `category` | urgent / regular / newsletter / spam |
| `urgency_score` | 1–10 (10 = respond immediately) |
| `estimated_budget` | Dollar amount extracted from message, or 0 |
| `key_signals` | 1–3 phrases that drove the classification |
| `suggested_reply_tone` | executive / consultative / friendly / polite_decline |
| `reasoning` | One sentence explaining the decision |

## Setup Instructions

### Prerequisites
- n8n instance (must be publicly accessible for webhook, or use ngrok for testing)
- Groq API key (free at [console.groq.com](https://console.groq.com))
- Slack workspace with incoming webhook or bot configured
- Gmail OAuth2 credential in n8n
- Google Sheets OAuth2 credential in n8n

### Steps

1. Import `03-AI-Lead-Router.json` into n8n
2. In the **Classify Inquiry** Code node, replace `YOUR_GROQ_API_KEY_HERE` with your key
3. In the **Generate Reply** Code node, do the same
4. Connect your Slack credential to the **Send a message** node and set your target channel
5. Connect your Gmail credential to the **Send Auto-Reply** node
6. Create a Google Sheet with tabs for: Regular, Newsletter, Spam — update the Sheet IDs in each log node
7. Activate the workflow and copy the webhook URL
8. Point your contact form's action to the webhook URL

### Testing Without a Form

Send a test POST request using curl:
```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test Client",
    "email": "test@example.com",
    "company": "ABC Corp",
    "inquiry_type": "automation",
    "message": "We need to automate our invoice processing. Budget around $500. Can we talk this week?",
    "timestamp": "2026-01-01T10:00:00Z"
  }'
```

## What I Learned Building This

- Building webhook-triggered workflows in n8n
- Multi-category routing with the Switch node
- Chaining two AI calls (classify → generate reply) in one workflow
- Sending Slack messages programmatically via n8n
- Designing a lightweight CRM pipeline with Sheets as the database

---

*Part of the [n8n Automation Portfolio](../README.md) by Basant Mote*
