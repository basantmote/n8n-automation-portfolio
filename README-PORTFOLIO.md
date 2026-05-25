# n8n Automation Portfolio

Production-ready automation workflows built with n8n, Groq AI, and Google Workspace. Each project solves a real business problem and is fully documented with setup instructions.

## About

I'm Basant Mote, a Nepal-based automation engineer with 10 years of experience in tech, education, and banking. I build custom n8n + AI workflows that help small businesses eliminate repetitive manual work.

📧 basantmote@gmail.com

## Workflows

| # | Workflow | What It Does | Stack | Difficulty |
|---|---|---|---|---|
| 01 | RSS Morning Digest | Pulls 5 RSS feeds, filters by keywords, emails a daily digest at 7 AM | n8n, Gmail | Beginner |
| 02 | Invoice Email Logger | Scans Gmail for invoices, extracts data with AI, logs to Google Sheets | n8n, Groq, Gmail, Sheets | Intermediate |
| 03 | AI Lead Router | Classifies contact form submissions, routes to Slack/email/sheets by urgency | n8n, Groq, Slack, Gmail, Sheets | Intermediate |
| 04 | Invoice OCR Pipeline | Watches Google Drive, OCRs PDF invoices, extracts fields with AI, logs to Sheets | n8n, OCR.space, Groq, Drive, Sheets | Advanced |
| 05 | AI Email Auto-Responder | Monitors inbox, filters real inquiries, drafts AI replies in your voice | n8n, Groq, Gmail | Intermediate |
| 06 | AI Lead Enrichment | Takes CSV leads, enriches with Hunter.io, scrapes websites, scores with AI, generates cold email intros, pushes qualified leads to Instantly | n8n, Groq, Hunter.io, Instantly, Sheets | Advanced |

## Tech Stack Used Across Projects

- **n8n** — self-hosted workflow automation
- **Groq API** — free, fast LLM inference (llama-3.3-70b-versatile)
- **Gmail OAuth2** — sending, receiving, labeling emails
- **Google Sheets** — lightweight data logging and CRM
- **Google Drive** — file-based workflow triggers
- **OCR.space** — PDF and image text extraction
- **Slack** — real-time notifications
- **Hunter.io** — company and email enrichment
- **Instantly API V2** — cold outreach campaign management

## How to Use These Workflows

1. Clone or download this repository
2. Open your n8n instance
3. Go to the workflow folder you want
4. Import the `.json` file into n8n (top right menu → Import)
5. Follow the setup instructions in that folder's `README.md`
6. Replace all `YOUR_*_HERE` placeholders with your own credentials
7. Activate

## Skills Demonstrated

- Workflow design and automation architecture
- LLM prompt engineering for structured data extraction
- OAuth2 credential setup for Google Workspace APIs
- Binary data handling (PDF download and processing)
- Conditional routing and data filtering in n8n
- Gmail inbox management and labeling automation
- Webhook-driven event processing
- REST API integration (Hunter.io, Instantly, Groq, OCR.space)
- CSV parsing and batch processing
- End-to-end outbound sales pipeline automation

## License

MIT — free to use, modify, and build on. Attribution appreciated but not required.
