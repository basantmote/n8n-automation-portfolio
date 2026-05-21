# 05 — AI Email Auto-Responder

An n8n workflow that monitors your Gmail inbox, filters out promotional emails, uses Groq AI to generate a professional reply in your tone and voice, and saves it as a Gmail draft — ready for your review and one-click sending.

## Problem It Solves

Responding to client and business inquiries takes time, especially when the same types of questions come in repeatedly. This workflow drafts a personalized reply to every real inquiry that hits your inbox — within minutes of it arriving — so you only need to review and hit send, never start from a blank page.

## Workflow Architecture

```
Gmail Trigger
(polls inbox every minute)
        │
  Filter — Is Real Inquiry?
  (excludes CATEGORY_PROMOTIONS and CATEGORY_UPDATES)
        │
        ├── False → Stop (ignore newsletters/promotions)
        │
        └── True →
              Groq AI (llama-3.3-70b-versatile)
              (reads email, generates contextual reply in your voice)
                    │
              Save as Gmail Draft
              (ready for your review before sending)
```

## Features

- Polls Gmail inbox automatically (configurable interval)
- Intelligently filters out newsletters, promotions, and update emails using Gmail label detection
- Passes the real email subject, sender, and body to Groq AI
- AI writes a professional, friendly reply in your configured persona and tone
- Draft saved directly to your Gmail Drafts folder — you stay in control before anything is sent
- No accidental auto-sends — always human-reviewed

## Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow automation |
| Gmail Trigger | Monitors inbox for new emails |
| IF node | Filters promotional/update emails |
| Groq API (llama-3.3-70b-versatile) | Generates contextual AI reply |
| Gmail node | Creates draft in Gmail |

## Setup Instructions

### Prerequisites
- n8n instance
- Gmail OAuth2 credential configured in n8n
- Groq API key (free at [console.groq.com](https://console.groq.com))
- Groq credential created in n8n under Credentials

### Steps

1. Import `05-AI-Email-AutoResponder.json` into n8n
2. In the **New Email Received** node, connect your Gmail OAuth2 credential
3. In the **Generate AI Reply** node:
   - Connect your Groq Header Auth credential (type: Header Auth, Name: `Authorization`, Value: `Bearer YOUR_GROQ_API_KEY`)
   - In the JSON body, update the system prompt placeholder with your name, location, and email:
     ```
     You are [YOUR NAME], a [YOUR LOCATION]-based automation engineer...
     Always end with: 'Best regards, [YOUR NAME] | [YOUR TITLE] | [YOUR EMAIL]'
     ```
4. In the **Save as Gmail Draft** node, connect your Gmail OAuth2 credential
5. For production use, change the Gmail Trigger poll time from `everyMinute` to `everyFifteenMinutes`
6. Activate the workflow

### Customizing the AI Persona

The system prompt in the **Generate AI Reply** node controls how the AI writes. Update it to match your real background, services, and tone. The more specific you are, the better the replies:

```
You are [Name], a [location]-based [your specialty].
You help [target clients] with [what you do].
Write [tone: professional/friendly/concise] replies.
Always mention [key offerings] when relevant.
```

### Adjusting the Email Filter

The IF node currently blocks `CATEGORY_PROMOTIONS` and `CATEGORY_UPDATES` Gmail labels. To also block social notifications, add a third condition:
- Value: `{{ $json.labels.map(l => l.name).join(',') }}`
- Operation: Does Not Contain
- Value: `CATEGORY_SOCIAL`

## What I Learned Building This

- Triggering n8n workflows on incoming Gmail messages (not just sending)
- Working with Gmail label arrays in n8n expressions
- Using the IF node with array-based conditions and `.map()` expressions
- Prompt engineering for persona-consistent AI writing
- Creating Gmail drafts programmatically via the Gmail node

---

*Part of the [n8n Automation Portfolio](../README.md) by Basant Mote*
