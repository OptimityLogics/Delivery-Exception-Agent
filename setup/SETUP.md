# Project Setup Guide

This guide explains how to import and configure the Delivery Exception Agent workflow in n8n.

## Prerequisites

- An n8n instance (cloud or self-hosted).
- A Google Gemini API key.
- A Google account and a Google Sheet for decision logs.
- Permission to create credentials and workflows in your n8n instance.

## 1. Import the workflow

1. Download workflow/Delivery_Exception_Agent.json from this repository.
2. Open n8n.
3. Choose Workflows → Import from File (menu wording may vary).
4. Select the JSON file.
5. Keep the workflow inactive while configuring credentials and testing.

## 2. Configure Google Gemini

1. In n8n, open Credentials and create an HTTP Header Auth credential.
2. Set the header name to x-goog-api-key and the value to your Gemini API key, following the current Gemini API instructions for your account.
3. Save the credential.
4. Open the Gemini - Classify & Decide HTTP Request node and select the credential.
5. Verify the model endpoint is available for your Gemini project. If needed, update the model endpoint to a model enabled for your account.

**Security:** never paste the API key into the workflow JSON or commit it to GitHub.

## 3. Configure Google Sheets logging

1. Create a Google Sheet named something like Delivery Exception Log.
2. Add a header row with these column names:

   timestamp, shipment id, exception type, action, priority, confidence, channel, recipient, message, reason, approval required, record status, guardrail overrides

3. In n8n, create/select a Google Sheets OAuth2 credential for your account.
4. Open Google Sheets - Log Record and select that credential.
5. Replace REPLACE_WITH_YOUR_GOOGLE_SHEET_ID with your own spreadsheet ID, or select the document using the n8n UI.
6. Confirm the target sheet/tab and column mappings.

The spreadsheet ID is configuration, not a credential; still, use your own sheet and avoid committing private sheet URLs.

## 4. Review the sample data and rules

The Lookup Data & Compute Facts Code node contains demo shipment and customer records. Replace them with an approved source before operational use.

Review the slaRules object:
- Standard tier warning threshold: 12 hours; escalation recipient label: ops-team.
- Premium tier warning threshold: 24 hours; escalation recipient label: ops-manager.

These recipient values are labels only. Connect them to a real internal notification destination if required.

## 5. Test the webhook

1. Open Webhook - Delivery Exception.
2. Use the n8n test URL while the workflow is in test/listening mode.
3. Send a POST request with JSON:

   ~~~json
   {
     "shipment_id": "SHP-1002",
     "exception_text": "Heavy rain has delayed the delivery vehicle."
   }
   ~~~

4. Inspect the execution output and verify the selected action, confidence, priority, and reason.
5. Check the Google Sheet for a new row.
6. Test a missing shipment ID, missing exception text, a damaged/lost report, an SLA breach, and a duplicate report.

Use only synthetic test data during setup.

## 6. Understand the approval boundary

The workflow prepares customer-facing email content and marks it as requiring approval. It does **not** send the customer email by itself. Add and test a human approval step and a notification/sending node before any real customer communication.

SLA escalations are prepared as records with destination labels; connect those labels to your approved internal notification system if needed.

## 7. Production checklist

- [ ] Replace demo shipment/customer data with a trusted system.
- [ ] Secure the webhook using authentication or signed requests.
- [ ] Configure Gemini and Google Sheets credentials in n8n.
- [ ] Verify the Google Sheet ID and column mappings.
- [ ] Add a human approval and delivery step before sending customer messages.
- [ ] Replace workflow-static-data deduplication/history with persistent storage.
- [ ] Add error monitoring, retries, and operational alerts.
- [ ] Test against realistic cases and review AI accuracy before activation.
- [ ] Keep API keys, OAuth tokens, personal data, and private URLs out of Git.

## Troubleshooting

- **Gemini authentication error:** confirm the selected HTTP Header Auth credential uses the expected header name and a valid API key.
- **Google Sheets error:** reconnect the OAuth credential and verify the spreadsheet ID, tab, and column headers.
- **Webhook returns no expected result:** inspect each node's execution data and confirm the request contains shipment_id and exception_text.
- **Unexpected human review:** check confidence, exception type, missing fields, and SLA guardrails; human review can be the intended safe outcome.
