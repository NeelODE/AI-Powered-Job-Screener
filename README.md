# 🚀 AI-Powered Job Hunter & Personalized Email Digest

Never miss a relevant job opportunity again! This n8n workflow automates your job search, so you can focus on what matters: acing the interview.

This powerful workflow scrapes job postings from **LinkedIn** and **Naukri.com**, uses a Large Language Model (like Google's Gemini) to analyze and rate each job against your personal resume, and delivers a beautifully formatted email digest with only the best matches directly to your inbox.

![Workflow Screenshot](placeholder.png)
_**(Tip: Replace `placeholder.png` with a screenshot of your n8n workflow!)**_

---

## ✨ What It Does

This workflow is designed to be your personal, automated job search assistant. It follows a simple yet powerful process:

1.  **Scrape:** Automatically fetches the latest job postings from LinkedIn and Naukri.com using Apify scrapers for keywords and locations you define.
2.  **De-duplicate:** Removes any duplicate job listings to keep the results clean.
3.  **Analyze & Rate:** For each unique job, an AI agent (powered by Google Gemini) reads the job description and compares it against your hardcoded resume. It then assigns a **relevance score from 1 to 100** and provides a detailed **justification** for its rating.
4.  **Filter:** Only the jobs that meet a minimum relevance score (e.g., 65/100 or higher) are allowed to pass to the next stage.
5.  **Consolidate & Format:** All high-scoring jobs are collected and formatted into a clean, easy-to-read HTML table.
6.  **Notify:** A single, consolidated email is sent to your Gmail account, containing all the relevant job opportunities, their details, your relevance score, and the AI's justification.

## 🔑 Key Features

-   **Multi-Platform Scraping:** Pulls jobs from both LinkedIn and Naukri.com.
-   **AI-Powered Resume Matching:** Leverages the power of Google Gemini to intelligently match job requirements with your skills and experience.
-   **Personalized Scoring:** Get a custom relevance score (1-100) and justification for every job.
-   **Automated Filtering:** Set your own threshold to only see jobs that are truly a good fit.
-   **Daily Email Digest:** Receive one clean, consolidated email instead of dozens of notifications.
-   **Highly Customizable:** Easily change the job search keywords, location, your resume, AI model, and filtering threshold.
-   **Low-Code & Easy to Deploy:** Built on n8n, making it accessible and easy to modify without extensive coding.

---

## ⚙️ How It Works (Workflow Breakdown)

The workflow consists of several key nodes:

-   **Manual Trigger:** Starts the workflow. Can be easily replaced with a **Cron Node** for daily execution.
-   **HTTP Request (Apify):** Two nodes that call the Apify API to run job scrapers for LinkedIn and Naukri.com and fetch the results.
-   **Remove Duplicates:** Ensures each job posting is processed only once.
-   **JOB MATCHING AI Agent:** The core of the workflow. These nodes send the job description and your resume to the AI with a specific prompt, asking it to return a structured JSON object containing the job details, a rating, and a justification.
-   **If Node:** Filters out jobs with a rating below a set threshold (default is 65).
-   **Merge Node:** Combines the filtered results from both job sources.
-   **HTML & Aggregate Nodes:** These nodes format the data for each job into a nice HTML block and then combine all blocks into a single output for the email body.
-   **Gmail Node:** Sends the final, formatted email to your specified address.

---

## 🛠️ Setup & Configuration

To get this workflow running for yourself, follow these steps:

**Prerequisites:**
*   An active **n8n** instance (Cloud or self-hosted).
*   An **Apify** account and API token.
*   A **Google Cloud Project** with the Gemini API enabled and an API Key.
*   **Google OAuth2 Credentials** for the Gmail API.

**Configuration Steps:**

1.  **Import the Workflow:** Copy the contents of the `linkedin Data Proccessor 1.1.json` file and paste it into your n8n canvas.

2.  **Configure Apify Scrapers (HTTP Request Nodes):**
    *   Open the **"HTTP Request NAUKRI"** and **"HTTP Request linkedin"** nodes.
    *   In the URL, replace the placeholder Apify token (`apify_api_...`) with your own.
    *   In the Body, modify the JSON to change `keywords`, `location`, `experience`, etc., to match your job search criteria.

3.  **Update Your Resume (AI Agent Nodes):**
    *   This is the most important step for personalization!
    *   Open the **"JOB MATCHING AI Agent linkedin"** and **"JOB MATCHING AI Agent naukri"** nodes.
    *   Go to the `Options` tab and find the `System Message` field.
    *   Scroll down to find the hardcoded resume text for "Malay Bhayani". **Replace this entire resume with your own.** Try to maintain a similar structure for best results.

4.  **Set Up AI Credentials:**
    *   Open the **"Google Gemini Chat Model"** node.
    *   Under "Credential for Google (PaLM) API," select your pre-configured Google API credential or create a new one using your Gemini API key.

5.  **Adjust the Filtering Threshold (If Nodes):**
    *   Open the **"If"** and **"If1"** nodes.
    *   In the "Conditions," you can change the `rightValue` from `65` to your desired minimum rating score.

6.  **Configure Gmail Node:**
    *   Open the **"Send a message1"** node.
    *   Under "Credential for Gmail API," configure your Google OAuth2 credentials.
    *   In the `Send To` field, enter your email address.

7.  **Activate and Automate:**
    *   Save the workflow.
    *   Replace the **"When clicking ‘Execute workflow’"** node with a **Cron Node** to schedule it to run daily, weekly, or as often as you like!

---

## 🚀 How to Use & Customize

-   **To Run:** Once configured, simply click "Execute workflow" or wait for your Cron schedule to trigger it.
-   **To Customize:**
    -   **Add More Job Boards:** Duplicate the HTTP Request and AI Agent nodes to add other sources like Indeed or Glassdoor (if they have a suitable scraper on Apify).
    -   **Dynamic Resume:** Instead of hardcoding your resume, use a `Google Drive` or `Read from File` node to fetch your latest resume each time the workflow runs.
    -   **Different Notifications:** Replace the `Gmail` node with a `Slack`, `Discord`, or `Telegram` node to get notifications where you prefer.
    -   **Track Your Applications:** Add a `Google Sheets` or `Airtable` node to automatically log the jobs you've been notified about, creating an application tracker.

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/your-username/n8n-ai-job-hunter-workflow/issues).
