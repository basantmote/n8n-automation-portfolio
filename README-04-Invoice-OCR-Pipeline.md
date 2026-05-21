# 04 — Invoice OCR Pipeline

An n8n workflow that watches a Google Drive folder for new PDF invoices, extracts text using OCR, sends it to Groq AI for structured data extraction, and logs the results to Google Sheets — fully automated from file drop to spreadsheet row.

## Problem It Solves

Businesses that receive PDF invoices spend significant time manually reading and entering data into spreadsheets. This pipeline eliminates that entirely. Drop a PDF into a Google Drive folder — the workflow detects it, reads it, extracts all key fields using AI, and logs a structured row to your sheet within seconds.

## Workflow Architecture

```
Google Drive Trigger
(watches "Inbox - Invoices" folder for new PDFs)
        │
  Download PDF file
        │
  OCR.space API
  (converts PDF to raw text)
        │
  Prepare Groq Prompt
  (structures the OCR text for AI extraction)
        │
  Groq AI (llama-3.3-70b-versatile)
  (extracts structured invoice fields)
        │
  Parse & Clean Response
  (strips markdown, parses JSON)
        │
  Log to Google Sheets
```

## Features

- Auto-detects new PDFs dropped into a designated Google Drive folder
- Downloads and processes the file without any manual steps
- OCR handles scanned PDFs, photographs of invoices, and digital PDFs
- AI extracts 8 structured fields from unstructured invoice text
- Handles noisy OCR output — AI normalizes messy text into clean data
- Logs each invoice as a row in Google Sheets with a timestamp
- Works on any invoice format — no templates required

## Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow automation |
| Google Drive Trigger | Monitors folder for new files |
| Google Drive node | Downloads the PDF |
| OCR.space API | Extracts text from PDF |
| Groq API (llama-3.3-70b-versatile) | Structured data extraction |
| Code node (JS) | Payload preparation + response parsing |
| Google Sheets | Invoice data storage |

## Extracted Fields

| Field | Description |
|---|---|
| `vendor_name` | Supplier or company name |
| `invoice_number` | Invoice reference number |
| `invoice_date` | Date on the invoice |
| `due_date` | Payment due date |
| `total_amount` | Total amount payable |
| `currency` | Currency code (USD, NPR, etc.) |
| `bill_to_name` | Recipient name |
| `bill_to_email` | Recipient email |
| `processed_at` | Timestamp when workflow ran |
| `source_file` | Reference to source file |

## Setup Instructions

### Prerequisites
- n8n instance
- Google Drive OAuth2 credential configured in n8n
- Google Sheets OAuth2 credential configured in n8n
- OCR.space API key (free tier at [ocr.space](https://ocr.space/ocrapi))
- Groq API key (free at [console.groq.com](https://console.groq.com))
- Groq credential created in n8n under Credentials

### Steps

1. Import `04-Invoice-OCR-Pipeline.json` into n8n
2. Create a folder in Google Drive called `Inbox - Invoices`
3. In the **Google Drive Trigger** node:
   - Connect your Google Drive credential
   - Replace `YOUR_GOOGLE_DRIVE_FOLDER_ID_HERE` with your folder ID (found in the folder's URL)
4. In the **Extract Text via OCR** node, replace `YOUR_OCRSPACE_API_KEY_HERE` with your OCR.space key
5. In the **HTTP Request** node, connect your Groq credential
6. Create a Google Sheet with these headers in row 1:
   `vendor_name | invoice_number | invoice_date | due_date | total_amount | currency | bill_to_name | bill_to_email | processed_at | source_file`
7. In the **Log to Sheets** node, replace `YOUR_GOOGLE_SHEET_ID_HERE` with your sheet ID
8. Activate the workflow
9. Drop a PDF invoice into your `Inbox - Invoices` folder to test

### Finding Your Google Drive Folder ID

The folder ID is in the URL when you open the folder:
```
https://drive.google.com/drive/folders/YOUR_FOLDER_ID_IS_HERE
```

## What I Learned Building This

- Triggering n8n workflows from Google Drive file events
- Sending binary file data (PDFs) to external APIs via multipart form
- Chaining OCR → AI in a single workflow pipeline
- Prompt engineering for consistent JSON output from LLMs
- Handling and cleaning unpredictable OCR text output

---

*Part of the [n8n Automation Portfolio](../README.md) by Basant Mote*
