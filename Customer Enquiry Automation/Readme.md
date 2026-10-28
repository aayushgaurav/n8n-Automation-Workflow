
Customer Enquiry Automation Blueprint

This repository contains an automated workflow blueprint built for Make.com (formerly Integromat). The pipeline listens for new submissions on a targeted customer questionnaire, records the details cleanly into an internal tracker spreadsheet, handles dynamic outbound consumer acknowledgment messaging, and triggers instant telemetry team notifications.

Workflow Architecture & Data Flow

The automation framework links specialized data collection endpoints together sequentially on a coordinated linear canvas:
[Google Form Response] ──> [Save Enquiry (Sheets)] ──> [Email Customer (Gmail)] ──> [Notify Telegram]
1. Watch Form Responses (Module ID: 1): Polls your tracking form (Form ID: 1gZs6pJ1ZDZFNNS3Abe0Io2rpAgG5-tu3v7DtS7wP38Y) for fresh consumer enquiries. It natively matches and maps multi-tier nested structural schemas for fields including:
	• Full Name (ID: 50413868)
	• Email Address (ID: 59181a8c)
	• Service of Interest (ID: 1112c671)
	• Custom Requirements / Description (ID: 655a7e38)
	• Acquisition Source (ID: 0834f281)
2. Save Enquiry (Module ID: 2): Automatically parses incoming variables and logs individual entries into Sheet1 of your master tracker (Spreadsheet ID: 1PR_S1WNNSJg7tGEteJjF2Z7Bz_9QqUfeWcOykRu8rII) via structural column insertions.
3. Email Customer (Module ID: 3): Triggers an automated, context-aware notification email to acknowledge the customer's enquiry using data from their submission.
4. Notify Telegram (Module ID: 4): Transmits a formatted message to your operations or sales chat group to ensure instant internally tracked response workflows can begin.

Technical Specifications


Parameter	Configuration Detail
Platform Engine	Make.com (Zone: ://make.com)
Polling Threshold	limit: 2 items per polling cycle
Google Form Asset	CSF form
Google Sheets Asset	CSF spreadsheet (Mode: USER_ENTERED)
Output Destination	Spreadsheet Row Columns A through Z


Deployment & Setup Guide


1. Prerequisites

• A active Make.com account (operating in the EU1 zone or adjusted via migration).
• Management access to the originating Google Form and target Google Sheet.
• An operational email server profile or a connected Google Workspace/Gmail account.
• A configured Telegram Bot API token along with your targeted target group channel chat ID.

2. Installation Steps

1. Download the raw blueprint JSON file (Customer Enquiry Automation.json) provided in this repository.
2. Navigate to your Make.com dashboard workspace, select Scenarios, and click Create a new scenario.
3. In the bottom control panel toolbar area, click on the More icon (represented visually by three dots ...) and select Import Blueprint.
4. Upload the downloaded .json blueprint file to populate your visual builder workspace automatically.
5. Click into the Watch Form Responses module and authenticate or link your Google OAuth credential profile (Aayush's Google connection).
6. Click into the Save Enquiry module, verify that it resolves cleanly against your spreadsheet location (/1PR_S1WNNSJg7tGEteJjF2Z7Bz_9QqUfeWcOykRu8rII), and ensure the incoming fields map correctly to your preferred sheet column headers.
7. Resolve the explicit Connection Setup Requests flagged on both the Email Customer (Module 3) and Notify Telegram (Module 4) blocks by authorizing your active sender profiles.
8. Toggle the primary scheduling switch in the lower left corner from OFF to ON to start monitoring entries.
