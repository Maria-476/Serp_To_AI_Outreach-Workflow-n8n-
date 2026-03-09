
# B2B AI Lead Generation & Outreach Automation

## 📌 Project Overview

This project is an automated B2B lead generation and outreach system built in n8n. It is designed specifically for Web Development Agencies to find potential clients who have slow or poorly optimized websites.

The workflow automatically searches for local businesses, extracts their contact details, audits their website speed in real-time, and uses an AI Agent to write highly personalized cold email drafts based on their actual performance scores.

**Niche Adaptability:** Currently, the Global Configuration is set to target **Solar Panel Companies**. However, this system is highly adaptable and can be used for any service-based business where website performance is crucial for acquiring customers, such as:

* Plumbing Services
* Roofing Contractors
* HVAC (Heating and Cooling) Companies
* Landscaping Services
* Real Estate Agencies
* Law Firms

---

## ⚙️ Technical Architecture

The system is built using n8n and integrates multiple external APIs. Here is the step-by-step node architecture:

* **Trigger Nodes (Form / Schedule):** Initiates the workflow either manually via a web form interface or automatically on a daily schedule.
* **Global Config:** A manual setup node to define the target niche and search parameters dynamically.
* **SerpAPI (Google Search):** Fetches the top-ranking local businesses based on the configured keyword (e.g., "Solar repairs in Texas").
* **Split & Loop Nodes:** Processes the fetched data individually to ensure accurate data handling and avoid API timeouts.
* **Apify (Web Scraper):** Visits the target websites to extract public contact information, primarily email addresses.
* **Google PageSpeed Insights API:** Audits the target website's performance and retrieves the mobile/desktop load speed scores.
* **Filter (Low Performance):** Acts as a business logic gate, passing only the leads whose website score falls below a certain threshold (e.g., < 60%), identifying them as prime prospects for web development services.
* **AI Agent (OpenAI Chat Model):** Takes the live performance score and target niche to write a hyper-personalized, context-aware email draft explaining how a faster website can improve their specific business.
* **Google Sheets (Append/Update):** Logs the processed leads for record-keeping and automatically deduplicates entries to avoid spamming the same company.
* **Filter (Email Exists):** A fail-proof node that ensures only leads with successfully scraped email addresses move to the final stage.
* **Gmail (Create Draft):** Automatically generates ready-to-send emails in the user's Gmail Drafts folder.

---

## 💼 Business Value

This workflow operates as a "Lead-Generation as a Service" product.

* **Hyper-Personalization:** Instead of sending generic spam, the outreach is based on a real, live audit of the prospect's website, drastically increasing the open and reply rates.
* **Time Efficiency:** It reduces manual prospecting, data entry, and email drafting time by over 95%.
* **Cost-Effective:** By keeping the output as "Drafts," the agency owner retains full control to review the emails before sending, ensuring quality assurance without manual effort.

---

## 🚀 Scalability & Future Improvements

While this is a fully functional Proof of Concept (MVP), the architecture is designed to scale. For a dedicated client, the following features can be added:

1. **Automated Keyword Rotation:** Connecting a Google Sheet as a master list of keywords/cities. The system will automatically pick a new keyword every day, running continuously without human intervention.
2. **CRM Integration:** Replacing or supplementing Google Sheets with direct integrations to CRMs like HubSpot, GoHighLevel, or Pipedrive to create automated sales deals.
3. **Instant Notifications:** Adding Slack or Telegram nodes to alert the sales team instantly when a highly qualified lead (e.g., extremely low performance score) is found.
4. **Automated Follow-ups:** Expanding the Gmail logic to send the email directly and setting up a delayed trigger to automatically send a follow-up email if the prospect does not reply within 3 days.
