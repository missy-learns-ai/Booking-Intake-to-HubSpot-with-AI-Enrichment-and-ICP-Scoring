# Calendly Booking Intake to HubSpot with AI Enrichment and ICP Scoring

This repository contains a n8n workflow for automating Calendly booking intake into HubSpot.

The workflow parses Calendly booking emails from Gmail, validates prospect bookings, creates or updates HubSpot contacts, records booking notes, enriches business-email leads with AI-assisted web research, calculates ICP fit, and sends a meeting briefing by Gmail.

## Workflow File

Import this file into n8n:

```text
workflows/booking-intake-calendar-to-hubspot.json
```

The export is sanitized for sharing. Credentials, workflow IDs, recipient emails, cache sheet references, webhook IDs, and sample execution data have been replaced with placeholders.

## What The Workflow Does

1. Gmail receives a Calendly booking notification.
2. The booking email is parsed into structured fields.
3. The workflow validates whether the booking is a valid prospect booking.
4. HubSpot contact data is created or updated.
5. Booking notes are added to HubSpot when present.
6. Business-email leads enter the enrichment path.
7. The workflow checks a Google Sheets company enrichment cache by domain.
8. Cached company data is reused when available.
9. If no cache exists, an AI Agent uses an OpenAI chat model, SerpAPI web search, and a structured output parser to research the company.
10. New enrichment data is saved back to the cache.
11. ICP score is calculated from company fit signals.
12. The ICP score is saved to the HubSpot contact.
13. A meeting briefing is built and sent by Gmail.
14. Invalid bookings or failed enrichment paths are routed to manual review.

## Production Features

- Sequential company processing to reduce rate-limit pressure.
- Company-domain cache to deduplicate enrichment.
- AI enrichment fallback only when no cached company profile exists.
- Structured parser for predictable enrichment output.
- Manual review path for validation or enrichment failures.
- Sanitized placeholders for safe sharing and review.

## Required n8n Credentials

After importing the workflow, reconnect these credentials:

```text
{{GMAIL_CREDENTIAL_ID}}
{{HUBSPOT_CREDENTIAL_ID}}
{{OPENAI_CREDENTIAL_ID}}
{{SERPAPI_CREDENTIAL_ID}}
{{GOOGLE_SHEETS_CREDENTIAL_ID}}
```

## Required Placeholders

Replace these placeholders inside n8n after importing:

```text
{{WORKFLOW_ID}}
{{WORKFLOW_VERSION_ID}}
{{N8N_INSTANCE_ID}}
{{CALENDLY_NOTIFICATION_EMAIL}}
{{MEETING_BRIEFING_RECIPIENT_EMAIL}}
{{GOOGLE_SHEETS_DOCUMENT_ID}}
{{GOOGLE_SHEETS_SHEET_ID}}
{{SEND_EMAIL_VIA_GMAIL_WEBHOOK_ID}}
```

Some placeholders are only used in pinned/sample data and do not need to be replaced for production execution.

## Google Sheets Cache

Create a Google Sheets tab for company enrichment cache data. Use `company_domain` as the dedupe key.

Recommended columns:

```text
company_domain
enrichment_status
enriched_company_name
website
industry
employee_count
employee_range
country
city
business_model
sells_to_enterprise
funding_stage
company_linkedin_url
technologies
source_urls
confidence
enrichment_source
enrichment_error
last_enriched_at
```

Array-like values such as `technologies` and `source_urls` are saved as readable text in Google Sheets.

## HubSpot Setup

The workflow expects a HubSpot contact property for ICP score.

Recommended property:

```text
icp_score
```

If your HubSpot property has a different internal name, update the `Patch HubSpot Contact ICP` node before activating the workflow.

## AI Enrichment Notes

The enrichment branch uses:

- AI Agent node for company research orchestration.
- OpenAI chat model for reasoning and extraction.
- SerpAPI tool for web search.
- Structured Output Parser for stable JSON output.
- Normalization code node to preserve downstream field names.

For production use, keep the Google Sheets cache enabled. This avoids repeat searches for the same company domain and reduces API cost, latency, and rate-limit errors.

## Import Steps

1. Open n8n.
2. Create a new workflow.
3. Import `workflows/booking-intake-calendar-to-hubspot.json`.
4. Reconnect Gmail, HubSpot, OpenAI, SerpAPI, and Google Sheets credentials.
5. Replace workflow-specific placeholders.
6. Configure the Google Sheets enrichment cache document and tab.
7. Confirm the HubSpot ICP score property name.
8. Test with one Calendly booking email.
9. Confirm cache lookup, AI enrichment, HubSpot update, and Gmail briefing behavior.
10. Activate the workflow.

## Privacy

This repository is intended to contain only sanitized workflow configuration. Do not commit live credentials, API keys, OAuth tokens, customer data, private email addresses, or pinned production execution data.
