# SmartOutreach AI

### Automated Lead Generation & Personalised Outreach Pipeline for Web Agencies

> Finds local businesses with underperforming websites, audits them with real Google PageSpeed data, scrapes contact details, and delivers AI-written personalised cold emails to your inbox — fully automated, every single day.

---

## The Problem

Web agencies and freelance developers spend 2–3 hours every day just finding prospects. Shared lead platforms like Angi give the same lead to 5 competitors. Google Ads cost $500–$2,000 per month. Cold outreach gets ignored because it is generic.

Most businesses with slow, broken websites do not even know their site is failing them — they are invisible on Google while competitors get their customers.

**There was no tool that combined outbound discovery, real website auditing, contact enrichment, and AI personalised outreach in one automated pipeline. So I built one.**

---

## What SmartOutreach AI Does

One form submission triggers a fully automated pipeline that:

1. **Discovers** local businesses matching your target niche and city using SerpAPI
2. **Audits** every website using Google PageSpeed Insights API — filters out sites scoring above 60
3. **Enriches** each lead using Apify — scrapes owner email, phone, LinkedIn URL
4. **Generates** a unique personalised cold email using GPT-4o-mini referencing the business's actual score
5. **Delivers** every lead to a structured Google Sheet and creates a ready-to-send Gmail draft
6. **Logs** every processed lead to Supabase — prevents duplicates across all future runs

---

## Live Output Example

**Google Sheet output:**

| Client Name | Website | Score | Email | AI Message |
|---|---|---|---|---|
| A&E Solar Panel Cleaning | aesolarpanelcleaning.com | 50 | contact@ae... | Hi Team at A&E Solar... |
| Lone Star Solar Services | lonestarsolar... | 44 | info@lone... | Hi Team at Lone Star... |

**Gmail draft generated:**

```
Subject: A&E Solar Panel Cleaning — your site scored 50/100

Hi A&E Solar Panel Cleaning team,

Your website currently scores 50 out of 100 on Google PageSpeed — 
meaning Google is ranking faster competitors above you in local search 
right now, sending potential customers to them instead of you.

Our team has helped solar companies in Texas go from similar scores 
to above 85 in under 7 days.

Reply and we will send you a free 5-minute video audit showing exactly 
what is slowing your site and the precise steps to fix it.

Looking forward to helping A&E Solar Panel Cleaning win more 
customers online.

Best regards,
Alex Reid
WebSpeed Pro
```

---

## Architecture

### Two-Workflow Design

The system is split into two n8n workflows for clean separation of concerns:

```
┌─────────────────────────────────────────────────────┐
│  WORKFLOW 1 — Lead Discovery                         │
│                                                       │
│  Trigger (Form / Schedule)                           │
│       ↓                                              │
│  Global Config (10 keyword variations)               │
│       ↓                                              │
│  Split Out (one item per keyword)                    │
│       ↓                                              │
│  SerpAPI Search (finds matching businesses)          │
│       ↓                                              │
│  Split Leads (splits organic results)                │
│       ↓                                              │
│  Call Worker Workflow → passes each lead             │
└─────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────┐
│  WORKFLOW 2 — Enrichment, AI & Delivery              │
│                                                       │
│  Receive lead from Workflow 1                        │
│       ↓                                              │
│  CHECK_Duplicate (Supabase query)                    │
│       ↓                                              │
│  IF Filter — skip if already processed               │
│       ↓                                              │
│  PageSpeed Audit → Filter Low Performance            │
│       ↓                                              │
│  Edit Fields (Data Prep)                             │
│       ↓                                              │
│  Apify Scraper (email, phone, LinkedIn)              │
│       ↓                                              │
│  Edit Fields (Final List)                            │
│       ↓                                              │
│  AI Agent GPT-4o-mini (writes personalised email)   │
│       ↓                                              │
│  Append to Google Sheet                              │
│       ↓                                              │
│  LOG_Lead (saves to Supabase — prevents duplicates)  │
│       ↓                                              │
│  Email Exists Filter                                 │
│       ↓                                              │
│  Create Gmail Draft                                  │
└─────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer | Tool | Cost |
|---|---|---|
| Automation engine | n8n (self-hosted on Google Cloud) | Free |
| Lead discovery | SerpAPI | Free 250/month |
| Website auditing | Google PageSpeed Insights API | Free |
| Contact enrichment | Apify — Website Contact Extractor | Free tier |
| AI email writing | OpenAI GPT-4o-mini | ~$0.01–0.03 per lead |
| Deduplication database | Supabase | Free tier |
| Lead delivery | Google Sheets | Free |
| Outreach | Gmail API | Free |
| Hosting | Google Cloud VM | Low cost |

**Estimated running cost:** $1–3 per day at 20 leads per day

---

## USP — Why This Is Different

| Feature | Traditional Agencies | Lead Marketplaces | SmartOutreach AI |
|---|---|---|---|
| Lead source | Manual prospecting | Shared database | Exclusive outbound discovery |
| Website audit | None | None | Real Google PageSpeed scores |
| Email personalisation | Generic templates | Generic templates | AI-written per business |
| Daily effort required | 2–3 hours | Still manual | Zero — fully automated |
| Duplicate prevention | Manual tracking | None | Supabase deduplication |
| Cost | $500–$2,000/month | $200–$800/month | ~$1–3/day to run |

**SmartOutreach AI is the only tool that combines outbound discovery, real website auditing, contact enrichment, and AI personalised outreach in one fully automated pipeline.**

---

## How to Run

### Prerequisites

- n8n self-hosted instance (Google Cloud, Railway, or local)
- SerpAPI account — [serpapi.com](https://serpapi.com)
- Google Cloud project with PageSpeed API enabled
- Apify account — [apify.com](https://apify.com)
- OpenAI API key — [platform.openai.com](https://platform.openai.com)
- Supabase project — [supabase.com](https://supabase.com)
- Google account (Sheets + Gmail OAuth)

### Step 1 — Set up Supabase

Run this SQL in your Supabase SQL Editor:

```sql
create table leads (
  id            bigserial primary key,
  lead_url      text unique,
  business_name text,
  client_id     integer default 1,
  status        text default 'new',
  created_at    timestamptz default now()
);

create table clients (
  id               bigserial primary key,
  client_name      text,
  niche            text,
  city             text,
  score_threshold  integer default 55,
  active           boolean default true,
  created_at       timestamptz default now()
);

create table error_log (
  id         bigserial primary key,
  node_name  text,
  error_msg  text,
  created_at timestamptz default now()
);

alter table leads enable row level security;

create policy "Allow anonymous inserts"
on leads for insert to anon with check (true);

create policy "Allow anonymous reads"
on leads for select to anon using (true);
```

### Step 2 — Configure n8n Credentials

Add these credentials in your n8n Credentials vault:

| Credential Name | Type | What to paste |
|---|---|---|
| `SerpAPI_Production` | HTTP Query Auth | Your SerpAPI key |
| `Supabase_LeadAudit` | Supabase | Project URL + anon key |
| `OpenAI_Production` | OpenAI | Your OpenAI API key |
| `Apify_Production` | Apify | Your Apify token |
| `Google Sheets` | OAuth2 | Authorise via Google |
| `Gmail` | OAuth2 | Authorise via Google |

### Step 3 — Import Workflows

1. In n8n click **+** to create new workflow
2. Click the three-dot menu → **Import from file**
3. Import `Workflow1_LeadDiscovery.json`
4. Repeat for `Workflow2_WorkerSO_AI.json`

### Step 4 — Configure Global Config

Open **Global Config** node in Workflow 1 and update the keywords array with your target niche and city:

```json
[
  "solar panel repair Houston TX",
  "solar panel installation Houston TX",
  "solar energy company Houston TX"
]
```

### Step 5 — Set up Google Sheet

Create a Google Sheet with these exact column headers in Row 1:

```
Client_Name | Website_Link | Performance_Score | Target_Email | linkedIn_URL | AI_Message
```

Copy the Sheet ID from the URL and update the **Append or update row in sheet** node.

### Step 6 — Run

Open the form trigger URL in your browser → click Submit → watch leads appear in your Google Sheet within minutes.

Or wait for the Schedule Trigger to run automatically.

---

## Project Structure

```
SmartOutreach-AI/
├── workflows/
│   ├── Workflow1_LeadDiscovery.json
│   └── Workflow2_WorkerSO_AI.json
├── database/
│   └── schema.sql
├── docs/
│   └── architecture.png
└── README.md
```

---

## Roadmap

- [ ] Multi-client support — one run per client from Supabase clients table
- [ ] No-website business detection — third pipeline for businesses with no online presence
- [ ] Lead scoring — rank leads 1–100 based on score, reviews, and competitor density
- [ ] Slack notification — daily top 3 leads sent to client Slack channel
- [ ] Self-serve onboarding form — clients configure their own niche and city
- [ ] White-label PDF audit reports
- [ ] Follow-up email sequences — day 3 and day 7 auto-drafts

---

## Current Limitations

- Processes one niche and location per run (multi-niche via keyword array)
- Apify scraping adds 2–5 minutes per lead — full run takes 15–30 minutes for 10 leads
- Gmail drafts require manual sending — auto-send is a planned upgrade
- Single client setup — multi-client dashboard is in roadmap

---

## Built By

Built by a solo developer as an MVP lead generation automation product targeting web agencies and freelance developers.

Currently accepting free pilot clients in exchange for feedback.

**Contact:** [your email here]
**LinkedIn:** [your LinkedIn here]

---

## License

MIT License — free to use, modify, and distribute with attribution.
