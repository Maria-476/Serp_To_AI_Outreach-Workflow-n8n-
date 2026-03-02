**AI Lead Generation Automation**

Automated lead prospecting system using SerpAPI, PageSpeed Insights, Apify, and OpenAI with normalized data handling and upsert-based duplicate protection.

🚀 Overview

This project is an end-to-end automated lead generation workflow built in n8n.

It:

Searches business leads via SerpAPI

Audits website performance using Google PageSpeed Insights

Extracts additional data using Apify

Generates personalized outreach content using OpenAI

Stores structured data in Google Sheets

Prevents duplicates using normalized URLs and Upsert logic

Designed as a production-ready portfolio automation project with cost control and scalable architecture.

🧠 Key Features

Automated lead search by keyword & location

URL normalization (removes http/https, trailing slash, lowercase conversion)

Duplicate-safe storage using Upsert (match on Website_Link)

Website performance filtering

AI-generated personalized outreach message

Cost-optimized execution (limited batch processing)

Schedule-based automation support

⚙️ Tech Stack

n8n (workflow automation)

SerpAPI (lead search)

Google PageSpeed Insights API

Apify (data extraction)

OpenAI API (message generation)

Google Sheets (data storage)

📂 Workflow Architecture

Trigger
→ Lead Search
→ Split & Batch Processing
→ Website Audit
→ Data Enrichment
→ AI Personalization
→ Data Normalization
→ Append or Update (Upsert) in Google Sheets

🔐 Setup Instructions

Before running this workflow:

Add your own SerpAPI key

Add your own Google PageSpeed API key

Add your own OpenAI API key

Add your own Apify API key

Connect Google Sheets credentials

API keys are not included for security reasons.

📌 Production Notes

Duplicate protection implemented using normalized Website_Link field

Designed with cost optimization in mind

Schedule trigger supported for live automation

Safe for scaling to higher lead volumes

🎯 Purpose

This project was built as a portfolio demonstration of:

Automation system design

AI integration

API orchestration

Data normalization

Production-ready workflow thinking
