# 01 — RSS Morning Digest

A scheduled n8n workflow that pulls articles from 5 RSS feeds every morning, filters by keywords, removes duplicates, and delivers a clean HTML digest to your inbox before 7 AM.

## Problem It Solves

Manually checking multiple news sources every morning is time-consuming and inconsistent. This workflow automates the entire process — aggregating, filtering, and formatting the most relevant articles into a single email so you start your day informed without effort.

## Workflow Architecture

```
Schedule Trigger (7 AM)
        │
        ├── RSS - TechCrunch
        ├── RSS - HackerNews
        ├── RSS - BBC Technology
        ├── RSS - n8n Blog
        └── RSS - OnlineKhabar
                │
           Merge Feeds
                │
           Parse & Filter
           (date filter, keyword match, deduplication, top 15)
                │
         Build HTML Email
                │
         Send via Gmail
```

## Features

- Pulls from 5 RSS sources simultaneously
- Filters articles published in the last 24 hours only
- Keyword matching on title + content (AI, automation, n8n, Nepal, fintech, banking, etc.)
- Deduplicates by URL to avoid repeat articles
- Sorts by newest first, caps at top 15 articles
- Renders a styled HTML email grouped by source feed
- Runs automatically every day at 7 AM

## Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow automation |
| RSS Feed Read node | Fetching articles from feeds |
| Code node (JS) | Filtering, deduplication, HTML building |
| Gmail | Sending the digest email |

## Setup Instructions

### Prerequisites
- n8n instance (self-hosted or cloud)
- Gmail account with OAuth2 configured in n8n

### Steps

1. Import `01-RSS-Morning-Digest.json` into n8n
2. Open the **Send Gmail Digest** node and update `YOUR_EMAIL_HERE` to your email address
3. In the **Parse dates** Code node, update the `keywords` array to topics you care about
4. Update RSS feed URLs in each RSS node to your preferred sources
5. Adjust the Schedule Trigger timezone to match yours
6. Connect your Gmail OAuth2 credential
7. Click **Activate**

### Customization

**Change keywords** — open the `Parse dates` Code node and edit the `keywords` array:
```javascript
const keywords = ['automation', 'ai', 'n8n', 'nepal', 'fintech'];
```

**Add more feeds** — duplicate any RSS node, set the new URL, connect it to the Merge node, and update the Merge node's input count.

**Change delivery time** — edit the Schedule Trigger and set your preferred hour.

## What I Learned Building This

- Parallel node execution in n8n (all 5 RSS nodes fire simultaneously)
- Merging and normalizing data from multiple sources with different schemas
- Writing JavaScript inside n8n Code nodes for filtering and deduplication
- Building and sending HTML emails programmatically via Gmail

## Sample Output

The email arrives grouped by source with article title, publication time, and a short snippet — one click opens the full article.

---

*Part of the [n8n Automation Portfolio](../README.md) by Basant Mote*
