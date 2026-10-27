# Delivery Exception Agent — Documentation

## Overview

The Delivery Exception Agent is an n8n workflow designed to process shipment delivery exceptions, evaluate shipment details and service-level agreement (SLA) conditions, classify exception scenarios with AI, and record the outcome for operational tracking.

## Workflow at a Glance

1. **Receive shipment data** — The webhook receives shipment and delivery-exception details.
2. **Validate the input** — The workflow checks required fields and detects duplicate reports.
3. **Evaluate the exception** — Rule-based checks assess the shipment data and SLA context.
4. **Classify with AI** — Google Gemini helps classify eligible exception cases and suggest an appropriate next action.
5. **Apply guardrails** — Missing or duplicate reports are handled without unnecessarily calling the AI step.
6. **Log the result** — The workflow appends the outcome to Google Sheets for tracking.
7. **Respond to the request** — The webhook returns the workflow result.

## Main Components

- **n8n:** Orchestrates the workflow and its decision paths.
- **Webhook:** Receives incoming shipment exception reports.
- **Google Gemini:** Supports classification of eligible exceptions.
- **Google Sheets:** Stores exception results for operational review.

## Configuration

Before running the workflow, configure the required credentials in n8n:

- Gemini API credentials for the AI HTTP Request node.
- Google Sheets OAuth credentials and the target spreadsheet.
- The webhook URL used by the system that submits shipment reports.

Keep credentials in n8n's credential manager or secure environment configuration. Do not place API keys, access tokens, or other secrets directly in the exported workflow JSON.

## Testing

Test the workflow with representative cases, including:

- A valid delivery exception.
- A report with missing required fields.
- A duplicate exception report.
- An exception that requires human review.

Confirm that the webhook response is appropriate and that only the expected records are written to Google Sheets.

## Related Files

- [Project README](../README.md)
- [Setup Guide](../setup/SETUP.md)
- [Workflow JSON](../workflow/Delivery_Exception_Agent.json)

## Notes

AI-generated classifications should be reviewed against operational policies before being used for consequential delivery decisions. The exact output depends on the workflow configuration and the data supplied.
