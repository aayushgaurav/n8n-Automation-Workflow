
India Sports Event Extraction & Aggregation Workflow

This repository contains an automated data pipeline built using n8n that scrapes, extracts, normalizes, filters, and saves upcoming sports events in India to a unified Google Sheet. It handles data collection simultaneously from two primary sports portals: the International Schools Sports Organization (ISSO) and the Basketball Federation of India (BFI).

Workflow Architecture

The workflow is scheduled to trigger weekly and runs through two distinct parallel ingestion pipelines that eventually merge into a deduplicated analytics destination:
                     ┌── [HTTP Request3] ──> [HTML Extraction] ──> [Custom JS Regex Engine] ──┐
                     │                                                                         │
[Schedule Trigger] ──┤                                                                         ├──> [Merge & Cross-Source De-duplication] ──> [Google Sheet]
                     │                                                                         │
                     └── [HTTP Request4] ──> [HTML Extraction] ──> [Gemini 3.5 AI Extraction] ─┘

Ingestion Pipeline 1: ISSO Portal (https://issoindia.com)

1. HTTP Request3 & HTML3: Downloads and extracts the raw text body from the ISSO events page.
2. Extract Events (JS): Uses structured RegExp pattern matching to isolate text between UPCOMING EVENTS 42 and PAST EVENTS tags. It systematically parses sequential lines to map event names, raw categories, dates, and locations.
3. Normalize Dates: Transforms raw dates (e.g., October 28th, 2026) into standardized ISO YYYY-MM-DD strings.
4. Filter Upcoming: Discards past events dynamically based on execution day.
5. Normalize Location & Map City to State: Splits unstructured location strings into distinct venue and city parameters, executing a lookup dictionary to cleanly map major Indian cities (e.g., Gurugram, Hyderabad, Kolkata) to their respective States.
6. If2 (Validation Guard): Ensures that only records containing complete metadata properties (event_name, sport, start_date, end_date, city) advance forward.
7. Adding a proper descriptor: Auto-generates a human-readable summary string (e.g., "Event Name is organized in City, State from Start Date to End Date for Category.").

Ingestion Pipeline 2: BFI Portal (https://basketballfederationindia.org)

1. HTTP Request4 & HTML4: Downloads and extracts text body from the BFI National Calendar.
2. Information Extractor4 (LLM Node): Utilizes models/gemini-3.5-flash-lite running a detailed system prompt instruction sheet to parse out structured data matching the unified target schema.
3. Split Out4: Converts the array of generated event objects from the LLM engine into standard standalone individual n8n data items.
4. Normalize BFI Fields & Filter: Maps explicit tracking values (source: "BFI", verification_status: "Pending") and enforces structural time window filtering.

Post-Merge & Analytics Sync

• Merge: Collects the standardized outputs from both branches.
• Remove Cross-Source Duplicates: Employs a compound lookup hash key (eventName|startDate|city) to safely identify and prevent identical items gathered from different sites from creating duplicate database entries.
• Append or update row in sheet: Upserts data directly into the India Sports Events Automation spreadsheet (Sheet ID: 1enCmB6AMF3DPEIZsV0gyVHCD-8Eus0zb14xfKjWGzD8). Rows match explicitly against the event_name anchor column.

Unified Data Schema

Every event synchronized to the master Google Sheet adheres strictly to the following parameters:

Column Field Name	Format/Type	Description
event_name (Match Key)	String	Primary identity title of the sporting event.
sport	String	Lowercase standardized sport classification (e.g., basketball, table tennis).
event_category	String	Format cleaned division label (e.g., U14, Under 17).
event_status	String	Life cycle positioning status flag (Defaults to Upcoming).
verification_status	String	Verification auditing posture value (Defaults to Pending).
source	String	Upstream provenance source indicator (ISSO or BFI).
official_url	String	Web destination origin tracking anchor link.
Organizer	String	Body organization running the platform event.
date_precision	String	Temporal granularity metric (Set to day).
start_date	Date (YYYY-MM-DD)	Calendar opening start date timestamp.
end_date	Date (YYYY-MM-DD)	Calendar concluding expiration date timestamp.
venue	String	Venue building infrastructure locale mapping.
City	String	Title-cased municipal city area registration.
State	String	Full state regional geopolitical name placement.
description	String	Automated structured natural text description paragraph.


Setup & Deployment Instructions


Prerequisites

• An active n8n instance (Cloud or self-hosted Docker container setup).
• A Google Cloud Platform (GCP) Service Account with the Google Sheets API enabled.
• A Google Gemini API Key configured for model inferencing.

Installation Steps

1. Create a blank spreadsheet inside your Google Drive directory. Title the primary active sheet tab as Final_Events.
2. Ensure you add your GCP Service Account email address as an Editor to the spreadsheet's share settings.
3. Copy the raw JSON workflow code block from Event Extraction workflow.json inside this repository.
4. Open your n8n workspace canvas editor interface, click your mouse onto the canvas workspace, and press Ctrl+V (or Cmd+V on macOS) to instantly paste the system architecture layout.
5. Open the Google Gemini Chat Model4 node configuration panel and select or configure your active Gemini credentials.
6. Open the final Append or update row in sheet node configuration panel, authenticate using your Google Service Account credential profile, and map the workflow targeting parameters to your newly generated sheet ID.
7. Toggle the workflow execution status flag located in the upper right workspace layout panel from Inactive to Active.
