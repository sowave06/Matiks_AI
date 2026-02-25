# Matiks_ai

MATIKS AI is a self-hosted, full-stack sentiment analysis pipeline that autonomously scrapes social media data, sanitizes payloads, and classifies sentiment, urgency, and category using rate-limited LLMs via OpenRouter. The system stores structured results in MongoDB, generates automated daily CSV reports via Gmail, and powers a live Google sheet dashboard for real-time feedback tracking — delivering cost-optimized, scalable AI intelligence without reliance on expensive SaaS platforms.

## General Info
*   Describe your project in more detail here.
MATIKS AI solves these problems by:
Automating the entire data collection and classification process
Sanitizing data before AI processing to reduce token costs
Implementing rate-limited LLM calls to prevent crashes
Structuring outputs into deterministic JSON for database storage
Providing both machine-level storage (MongoDB) and human-readable reporting (CSV + Sheets)


## Technologies

n8n (Self-Hosted) – Workflow automation engine for scheduling, scraping orchestration, rate-limiting, data processing, and routing.
Schedule Trigger (Cron Job) – Executes the pipeline automatically every 24 hours.
HTTP Request Nodes – Custom REST API integration with Apify for synchronous scraping.

Apify API – Social media data scraping (Reddit / Twitter).
Custom REST API Calls (POST & GET) – Actor execution and dataset retrieval strategy for real-time data ingestion.

OpenRouter API – Gateway for LLM access.

MongoDB – Primary NoSQL database for structured sentiment storage.
Mongoose (ODM) – Schema modeling and database interaction.
Node.js – Backend runtime environment.
Express.js – RESTful server and dashboard backend.

Google Sheets API – Secondary human-readable storage.
Gmail API – Automated daily CSV report delivery.
Convert to File Node (CSV Generation) – Business-ready export format.
Custom Express.js Dashboard – Real-time sentiment visualization

## Installation
Install & Run n8n Locally
npm install -g n8n

docker run -it --rm \
-p 5678:5678 \
n8nio/n8n
http://localhost:5678

Inside n8n:

Add Schedule Trigger (24-hour cron)
Add HTTP Request (POST) → Apify Run Actor
Add HTTP Request (GET) → Fetch Dataset
Add Limit Node
Add Edit Fields Node
{username,platform,body,url,created_at,type}
Add Merge Node
Add Loop Node (Batch Size = 1)
Add Wait Node (2–3 seconds)
Add LLM Node (OpenRouter)

Add Structured Output Parser
```javascript
return items.map(item => {
  const rawText =
    item.json.output[0].content[0].text;
  const parsed = JSON.parse(rawText);
  return { json: parsed };
```
Add Merge (Done Output)
Add:
MongoDB Node
Google Sheets Node
{match the llm output in columns}
Convert to File (CSV)
Gmail Node
Execute\

FINAL WORKFLOW:

![workflow](assets/n8n_workflow.png)

Scraped data is classified
Sheets is updated.

SHEET DATA:

![sheet](assets/sheet_data.png)

MongoDB is updated
Daily CSV email is sent
