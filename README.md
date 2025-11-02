# 🚀 AI-Powered Job Hunter & Personalized Email Digest

Never miss a relevant job opportunity again! This n8n workflow automates your job search, so you can focus on what matters: acing the interview.

This powerful workflow scrapes job postings from **LinkedIn** and **Naukri.com**, uses a Large Language Model (like Google's Gemini) to analyze and rate each job against your personal resume, and delivers a beautifully formatted email digest with only the best matches directly to your inbox.

![Workflow Screenshot](workflow-screenshot.png)
_**(Remember to replace this with a screenshot of your n8n workflow!)**_

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
*   A **Google Account** to authorize Gmail access.

**Configuration Steps:**

1.  **Import the Workflow:** Copy the contents of the `linkedin Data Proccessor 1.1.json` file and paste it into your n8n canvas.

2.  **Configure Apify Scrapers (HTTP Request Nodes):**
    *   Open the **"HTTP Request NAUKRI"** and **"HTTP Request linkedin"** nodes.
    *   **Important:** In the URL, replace the placeholder Apify token (`apify_api_...`) with your own.
    *   In the Body, modify the JSON to change `keywords`, `location`, `experience`, etc., to match your job search criteria.

3.  **Update Your Resume (AI Agent Nodes):**
    *   This is the most important step for personalization!
    *   Open the **"JOB MATCHING AI Agent linkedin"** and **"JOB MATCHING AI Agent naukri"** nodes.
    *   Go to the `Options` tab and find the `System Message` field.
    *   Scroll down to find the hardcoded resume text. **Replace this entire resume with your own.** (Remember to scrub personal info like phone/email if you re-share the workflow).

4.  **Set Up AI Credentials (Google Gemini):**
    *   Open the **"Google Gemini Chat Model"** node.
    *   Under "Credential for Google (PaLM) API," select your pre-configured Google API credential or create a new one using your Gemini API key.

5.  **Adjust the Filtering Threshold (If Nodes):**
    *   Open the **"If"** and **"If1"** nodes.
    *   In the "Conditions," you can change the `rightValue` from `65` to your desired minimum rating score.

6.  **Configure Gmail Node:**
    *   Open the **"Send a message1"** node.
    *   In the `Send To` field, enter your email address.
    *   Under "Credential for Gmail API," you will need to create new OAuth2 credentials. This is the most complex step, so follow the guide below.

    <details>
    <summary><strong>➡️ Click here for a detailed guide on setting up Gmail OAuth2 Credentials.</strong></summary>

    This process involves creating a project in the Google Cloud Console, enabling the Gmail API, and generating credentials that n8n can use securely.

    #### **Step A: Create a Google Cloud Project**
    1.  Go to the [Google Cloud Console](https://cloud.google.com/) and sign in.
    2.  Click the project dropdown in the top-left corner and click **New Project**.
    3.  Give it a name like `n8n-gmail-automation` and click **Create**.
    4.  Ensure your new project is selected in the dropdown.

    #### **Step B: Enable the Gmail API**
    1.  Open the navigation menu (☰) and go to **APIs & Services > Enabled APIs & services**.
    2.  Click **+ ENABLE APIS AND SERVICES**.
    3.  Search for `Gmail API` and select it.
    4.  Click **Enable**.

    #### **Step C: Configure the OAuth Consent Screen**
    1.  From the navigation menu, go to **APIs & Services > OAuth consent screen**.
    2.  Choose **External** as the user type and click **Create**.
    3.  Enter an **App name** (e.g., `n8n Gmail Notifier`), select your email for **User support email**, and enter your email for **Developer contact information**.
    4.  Click **SAVE AND CONTINUE** through the next sections. You don't need to add scopes or test users here.
    5.  Once done, go back to the dashboard and find the "Publishing status" section. Click **PUBLISH APP** and confirm. This prevents your connection from expiring every 7 days.

    #### **Step D: Create and Add Credentials to n8n**
    1.  In the Google Cloud Console, go to **APIs & Services > Credentials**.
    2.  Click **+ CREATE CREDENTIALS** and select **OAuth client ID**.
    3.  Set the **Application type** to **Web application**.
    4.  Give it a name (e.g., `n8n Gmail Client`).
    5.  Now, go back to your n8n workflow. In the **"Send a message1"** node, under the credentials dropdown, select **- Create New -**. In the popup window, you will see an **OAuth Redirect URL**. Click the copy icon next to it.
    6.  Return to the Google Cloud Console. Under **Authorized redirect URIs**, click **+ ADD URI** and paste the URL from n8n.
    7.  Click **Create**. A popup will appear with your **Client ID** and **Client Secret**.
    8.  Copy the **Client ID** from Google and paste it into the **Client ID** field in n8n.
    9.  Copy the **Client Secret** from Google and paste it into the **Client Secret** field in n8n.
    10. Click **Sign in with Google**. Follow the prompts to authorize your account. You may need to bypass a "Google hasn't verified this app" warning, which is safe to do.
    11. Once authenticated, give your credential a name (e.g., `My Gmail OAuth`) and click **Save**. You're all set!

    </details>

7.  **Activate and Automate:**
    *   Save the workflow.
    *   To make it run automatically, replace the **"When clicking ‘Execute workflow’"** node with a **Cron Node**. Set the schedule (e.g., once a day at 8 AM) to receive your personalized job digest automatically!

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
