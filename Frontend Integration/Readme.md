
Events API - Frontend Workflow

This repository contains the frontend delivery layer of the India Sports Events Automation system. Built inside n8n, this workflow serves as a lightweight, production-ready REST API endpoint that exposes the collected, deduplicated sports event database directly to client applications, frontend dashboards, or external programmatic consumers.

API Architecture & Data Flow

The workflow acts as an on-demand synchronous gateway mapping inbound web traffic directly to your data source:
[HTTP GET /events] ──> [Webhook Node] ──> [Google Sheets Node] ──> [Respond to Webhook] ──> [JSON Array Payload]
1. Webhook Node: Exposes a production HTTP listener waiting for incoming GET requests on the /events URL path. It is configured with responseMode: "responseNode" to prevent n8n from auto-replying before data loading finishes.
2. Get row(s) in sheet: Connects directly to the master India Sports Events Automation spreadsheet (Sheet ID: 1enCmB6AMF3DPEIZsV0gyVHCD-8Eus0zb14xfKjWGzD8) using a secure GCP Service Account. It reads all structural entries contained inside the Events tab.
3. Respond to Webhook: Flattens the multi-object input array structure using a JavaScript expression ({{ $input.all().map(item => item.json) }}). This formats and returns a clean, compliant JSON array directly to the client browser or calling client script with minimal network latency.

Endpoint Specification


Get All Events

Retrieves the complete list of upcoming, normalized sports calendar records.
• Method: GET
• Path: /events
• Headers: Content-Type: application/json

Example Response Payload (200 OK)

json
[
  {
    "event_name": "68th National Basketball Championship",
    "sport": "basketball",
    "event_category": "Senior Men and Women",
    "event_status": "Upcoming",
    "verification_status": "Pending",
    "source": "BFI",
    "official_url": "https://basketballfederationindia.org",
    "Organizer": "BFI",
    "date_precision": "day",
    "start_date": "2026-11-15",
    "end_date": "2026-11-22",
    "venue": "Jawaharlal Nehru Indoor Stadium",
    "City": "Chennai",
    "State": "Tamil Nadu",
    "description": "68th National Basketball Championship is organized in Chennai, Tamil Nadu, from 15th November 2026 to 22nd November 2026 for Senior Men and Women."
  }
]
Use code with caution.

Deployment & Production Configurations


1. Activating the Production URL

1. Open this workflow canvas inside your n8n workspace editor interface.
2. Open the Webhook node parameters.
3. Take note of the Production URL listed inside the node view (Note: Do not distribute the Test URL as it requires manual UI execution steps).
4. Flip the workflow tracking switch in the top menu from Inactive to Active.

2. Frontend Integration Quickstart (JavaScript Fetch Example)

To map the returned JSON array payload into a frontend user interface table or map dashboard card component, implement the following script pattern:
javascript
const N8N_API_URL = 'https://your-n8n-instance.com';

async function loadSportsEvents() {
  try {
    const response = await fetch(N8N_API_URL, { method: 'GET' });
    if (!response.ok) throw new Error(`HTTP network error: ${response.status}`);
    
    const eventsList = await response.json();
    console.log(`Loaded ${eventsList.length} active events:`, eventsList);
    
    // Insert rendering logic here (e.g., updating React state or DOM elements)
  } catch (error) {
    console.error('Failed to retrieve sports calendar dataset:', error);
  }
}

loadSportsEvents();
Use code with caution.
