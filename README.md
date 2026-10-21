Project Summary: Automated Google Sheets to Gmail Reporting Pipeline via n8n
1. Overview & Objective
The goal of this project was to design and deploy an end-to-end automation workflow in n8n that retrieves tabular records from a Google Sheet, cleans and formats the data into a responsive HTML report, and dispatches it via Gmail without manual intervention.

2. Workflow Architecture
┌───────────────────────────────┐
│ Manual / Schedule Trigger     │  Initiates workflow execution
└──────────────┬────────────────┘
               ▼
┌───────────────────────────────┐
│ Google Sheets Node            │  Fetches row data via Google Sheets OAuth2 API
│ [Get row(s) in sheet]         │  Target: "Students Report Data" -> Tab: "Project / Task Status Report"
└──────────────┬────────────────┘
               ▼
┌───────────────────────────────┐
│ Code Node (JavaScript)        │  - Strips metadata fields (e.g., row_number)
│ [Data Transformation]         │  - Formats HTML table with dynamic headers/rows
│                               │  - Constructs dynamic Subject line & custom sign-off
└──────────────┬────────────────┘
               ▼
┌───────────────────────────────┐
│ Gmail Node                    │  Delivers the compiled HTML email to designated recipients
│ [Send Message]                │  Authenticated via Google Cloud Console OAuth2 credentials
└───────────────────────────────┘
3. Key Components & Implementation
Trigger Layer:

Node: When clicking ‘Test workflow’ (Manual Trigger) for testing, with seamless transition capability to a Schedule Trigger (e.g., daily/weekly automated runs).

Data Extraction Layer:

Node: Google Sheets (Get row(s) in sheet).

Target Sheet: Students Report Data / Project / Task Status Report.

Authentication: Configured via a dedicated Google Cloud Console project with OAuth 2.0 Web Application client credentials and explicit test-user provisioning.

Data Transformation & Formatting Layer:

Node: Code in JavaScript (Mode: Run Once for All Items).

Operations:

Filters out internal n8n metadata (row_number).

Dynamically generates table headers and data cells using row keys and values.

Incorporates custom email structure:

Subject Line: Students Report Data - Project / Task Status Report: X records processed

Email Header: <h2>Students Report Data - Project / Task Status Report</h2>

Sign-off: Regards, GS Automation

Delivery Layer:

Node: Gmail (Send Message).

Configuration: Set to HTML format, dynamically mapping {{ $json.emailSubject }} and {{ $json.emailHtml }} from the Code node output.

4. Key Issues Encountered & Resolved
Google OAuth2 "Access blocked (403: access_denied)": Resolved by adding the execution account to the Test users list in the Google Cloud Console OAuth consent screen.

Client Authentication Error (invalid_client): Resolved by ensuring the credential type was strictly set to Web application, trimming whitespace, and aligning the authorized redirect URIs.

Referenced Node Resolution Error: Fixed expressions in the Code node to accurately target the active upstream node label (Get row(s) in sheet).

URL Parameter Leaks in Email Headers: Replaced raw URI/sheet ID strings with explicit file and tab titles to ensure clean, human-readable email presentation.

5. Final Outcome
An end-to-end automated reporting pipeline running on self-hosted n8n that reads live spreadsheet records, formats them into a clean HTML table, and delivers them directly to the recipient's inbox.
