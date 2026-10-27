# Delivery Exception Agent — Documentation

## Purpose
This project demonstrates logistics delivery-exception triage with n8n and Google Gemini. The workflow receives a report, looks up demo shipment facts, asks the model to classify the exception, applies deterministic guardrails, and returns a structured decision.

## Safety design
- Rules calculate SLA facts; the AI does not control SLA values.
- Low-confidence, unknown, damaged/lost, and complaint cases are routed to human review.
- A breached or at-risk SLA can override an AI-suggested customer notification.
- Customer-facing messages are drafts and require human approval before sending.

## Demo data and production notes
The workflow uses synthetic sample records only. Replace the sample lookup with an approved shipment/customer system. The workflow JSON has no embedded API key or credential binding. Configure the Gemini HTTP Header Auth credential in n8n after import. Before production, add webhook authentication, durable duplicate tracking, persistent audit storage, and the actual approval/notification integrations.

## Input
Send a POST request to the webhook with JSON containing shipment_id and exception_text.

~~~json
{
  "shipment_id": "SHP-1002",
  "exception_text": "Heavy rain has delayed the delivery vehicle."
}
~~~
