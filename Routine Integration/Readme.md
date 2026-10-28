# Routine Integration

An n8n automation workflow that synchronizes routine tasks and task completion records with Google Sheets.

## Features

- Receives routine data through a webhook
- Receives task completion updates through a separate webhook
- Inserts or updates spreadsheet rows using unique identifiers
- Returns JSON responses after processing requests

## Tech Stack

- n8n
- Webhooks
- Google Sheets API
- JSON

## Workflow Architecture

1. Routine webhook receives task details.
2. Google Sheets updates the matching routine using `task_id`.
3. Completion webhook receives task status and completion details.
4. Google Sheets updates the matching task record using `record_id`.
5. Webhook response nodes return confirmation messages
