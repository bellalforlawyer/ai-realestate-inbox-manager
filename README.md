# AI Real Estate Inbox Manager

An AI-powered email automation system that reads incoming business inquiries, categorizes them, and drafts professional replies — built with n8n and Google Gemini.

## Business Problem

Small and medium businesses (real estate agencies, consultants, service providers) receive dozens of inquiry emails daily. Common challenges:

- Delayed responses cause potential customers to go to competitors ("speed to lead" problem)
- Business owners waste time manually reading and replying to every email
- Spam, genuine inquiries, and complaints get mixed together in one inbox
- Reply tone/quality is inconsistent across team members

## Business Value

- **Speed to lead**: AI drafts a professional reply within 1 minute of an email arriving
- **Time saving**: Owner only reviews and sends, instead of writing from scratch
- **Consistency**: Every reply follows the same professional brand voice
- **Organization**: Spam is automatically filtered; sales inquiries and complaints are distinguished
- **Safety**: AI creates a *draft*, not a sent email — a human always reviews before sending

## Target Clients

- Real estate agents/agencies
- Freelance consultants (legal, financial, coaching)
- Small service businesses (interior designers, event planners, contractors)
- E-commerce stores (pre-sales inquiries)

## Architecture

Gmail Trigger (unread emails, polls every minute)
↓
AI Agent (Google Gemini + Structured Output Parser)

Categorizes email: Sales Inquiry / Support-Complaint / Spam-Irrelevant
Drafts a professional reply based on category
↓
IF Node (category ≠ Spam/Irrelevant?)
↓ ↓
TRUE branch FALSE branch
Create Gmail Draft No Operation (ignored)
(same thread, using
Thread ID)

## Key Implementation Details

- **Structured Output Parser**: Forces the AI Agent to return clean JSON (`category`, `reply`) instead of free text, making downstream logic (IF node) reliable
- **Thread ID handling**: The Gmail draft is created with the original email's `threadId`, so replies appear inside the same conversation thread instead of as a new, disconnected email
- **No memory node used**: Unlike chat-based bots (Telegram/WhatsApp), email threads carry their own context in the quoted message history, so conversation memory isn't needed here
- **Draft, not Send**: For safety, the workflow creates a Gmail draft rather than sending automatically — a human reviews before sending

## Setup / Deployment Guide

1. Import the workflow JSON into n8n
2. Connect your Gmail account credentials (OAuth)
3. Update the AI Agent's System Message with your business details (name, services, contact info)
4. Set the Gmail Trigger to "Unread emails only" with your preferred poll interval
5. Test using "Execute step" before activating
6. Activate the workflow once satisfied with test results

## Testing

Tested with three email types:
- Sales inquiry (property rental request) → correctly categorized, relevant reply drafted
- Complaint (missed viewing appointment) → correctly categorized as Support/Complaint, apologetic tone
- Thread continuity verified — draft appears in the same Gmail conversation thread

## Future Improvements

- Google Sheets logging of all inquiries (like the Law Firm Lead Agent project)
- Multi-language detection (Urdu/English)
- Auto-send for high-confidence, low-risk categories
- Slack/Telegram notification for urgent complaints

## Tech Stack

- n8n (workflow automation)
- Google Gemini (AI Agent + language model)
- Gmail API (trigger + draft creation)
- n8n Structured Output Parser
