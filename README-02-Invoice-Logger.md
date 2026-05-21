# 02 — Invoice Email Logger

An n8n workflow that scans your Gmail inbox for invoice and receipt emails, uses Groq AI to extract structured data from each one, and logs the results into a Google Sheet — automatically labeled and organized.

## Problem It Solves

Manually tracking invoices received by email is tedious and error-prone. Finance teams and freelancers often miss invoices or spend hours copying data into spreadsheets. This workflow reads your inbox, identifies real invoices using AI, extracts the key fields, and logs everything to a sheet with zero manual effort.

## Workflow Architecture

```
Manual Trigger
      │
 Gmail — Fetch emails with "invoice/receipt/bill" in subject
      │
 Groq AI (llama-3.3-70b-versatile)
 Extracts: vendor, amount, currency, date, confidence score
      │
 Filter — Keep only confirmed invoices (is_invoice: true)
      │
      ├── Add Gmail Label ("Processed - Invoice")
      └── Append row to Google Sheet
```

## Features

- Searches Gmail for emails containing invoice-related keywords
- Sends each email to Groq AI for intelligent data extraction
- Extracts: vendor name, amount, currency, invoice date, confidence level
- Filters out non-invoice emails (AI determines what is and isn't an invoice)
- Logs confirmed invoices to Google Sheets with all extracted fields
- Labels processed emails in Gmail for easy reference
- Returns confidence score (high/medium/low) so you know which to double-check

## Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow automation |
| Gmail node | Fetching emails |
| Groq API (llama-3.3-70b-versatile) | AI data extraction |
| Code node (JS) | Prompt building + response parsing |
| Google Sheets | Invoice log storage |
| Gmail node | Labeling processed emails |

## Extracted Fields

| Field | Description |
|---|---|
| `vendor_name` | Company or sender name |
| `amount` | Invoice total (numeric) |
| `currency` | USD, EUR, NPR, etc. |
| `invoice_date` | Date on the invoice (YYYY-MM-DD) |
| `confidence` | high / medium / low |
| `sender_email` | Email address of sender |
| `subject` | Original email subject |
| `received_at` | Timestamp when email arrived |
| `message_id` | Gmail message ID for traceability |

## Setup Instructions

### Prerequisites
- n8n instance
- Gmail OAuth2 credential configured in n8n
- Google Sheets OAuth2 credential configured in n8n
- Groq API key (free at [console.groq.com](https://console.groq.com))

### Steps

1. Import `02-Invoice-Logger.json` into n8n
2. Create a new Google Sheet with these column headers in row 1:
   `Date Received | Sender Email | Vendor Name | Amount | Currency | Invoice Date | Subject | Gmail Message ID`
3. In the **Append or update row in sheet** node, replace `YOUR_GOOGLE_SHEET_ID_HERE` with your sheet's ID (found in the sheet URL)
4. In the **call Grok** Code node, replace `YOUR_GROQ_API_KEY_HERE` with your Groq API key
5. Connect your Gmail and Google Sheets OAuth2 credentials
6. Create a Gmail label called `Processed - Invoice` and copy its Label ID into the two **Add label** nodes
7. Click **Execute Workflow** to run manually

### Finding Your Google Sheet ID

The Sheet ID is in the URL of your Google Sheet:
```
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_IS_HERE/edit
```

## What I Learned Building This

- Using Groq AI inside a Code node with raw HTTP requests
- Structuring prompts for reliable JSON output from an LLM
- Filtering n8n items conditionally using JavaScript
- Writing to Google Sheets with dynamic column mapping
- Gmail label management via n8n

---

*Part of the [n8n Automation Portfolio](../README.md) by Basant Mote*
