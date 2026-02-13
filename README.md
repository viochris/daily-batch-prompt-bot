# 🎨 Daily Batch Prompt Generator Bot

![Python](https://img.shields.io/badge/Python-3.11%2B-blue?logo=python&logoColor=white)
![Prefect](https://img.shields.io/badge/Prefect-Orchestration-070E28?logo=prefect&logoColor=white)
![Gemini](https://img.shields.io/badge/AI-Gemini%202.5%20Flash-8E75B2?logo=google&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Database-34A853?logo=googlesheets&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active-success)

## 📌 Overview
**Daily Batch Prompt Generator Bot** is a **High-Performance Automation Tool** designed to keep your AI art pipelines fully stocked with fresh ideas.

Unlike the standard version which generates one prompt at a time, this bot leverages **Gemini 2.5 Flash** to generate **batches of 3 creative prompts** in a single API call and bulk-uploads them to Google Sheets. This efficiency ensures your content queue never runs dry, making it perfect for high-frequency art generation workflows.

### 🔗 The "Creative Ecosystem"
This project is designed to act as the **High-Volume Producer** for the **[Automated Image Pipeline](https://github.com/viochris/automated-image-pipeline)**.
* **Producer (This Bot):** Generates *multiple* ideas at once and bulk-fills the spreadsheet.
* **Consumer (Image Bot):** Reads from the same spreadsheet row-by-row, generates the art, and posts it to Telegram.
* **Flexible Scheduling:** You can run them as a single monolithic flow or schedule them sequentially (e.g., Prompt Gen at 06:00, Image Gen at 06:10) to ensure your content queue never runs dry.

### ⚡ Single-Prompt Variant
Don't need a high volume? Check out the **[Standard Prompt Generator](https://github.com/viochris/daily-prompt-generator-bot)**.
* **Standard Efficiency:** Focuses on generating **1 unique prompt** with maximum detail per API call.
* **Direct Output:** No complex parsing needed. The raw AI text is validated and sent directly to the database.
* **Simple Insert:** Uses `gspread`'s `append_row` for a lightweight and reliable database update.

## ✨ Key Features
### 🧠 Batch AI Engineering
* **3-in-1 Generation:** Maximizes API efficiency by instructing Gemini to output **3 unique prompts** in one response, separated by double newlines.
* **Smart Parsing:** The script automatically splits the raw AI text into a list of clean strings, filtering out any empty lines or formatting errors.

### 📊 Bulk Database Operations
* **Batch Insertion:** Uses `gspread`'s `append_rows()` (plural) function to write all generated prompts to the database in a **single HTTP request**. This is significantly faster and more reliable than looping through row insertions.
* **Workflow Logic:** Appends the batch directly to the "Process" worksheet, ready for immediate consumption.

### 🛡️ Robust Orchestration
* **Prefect Flows:** Wraps logic in resilient tasks with automatic **Retry Policies** (3 retries, 5s delay) to handle transient API glitches.
* **Secure Error Handling:** Implements strictly sanitized logging to prevent API keys or credential leaks during runtime errors.
* **Fail-Fast Mechanism:** Automatically halts the flow if the AI returns empty data or if the parsing logic fails.

## 🛠️ Tech Stack
* **Orchestrator:** Prefect (Workflow Management)
* **Language:** Python 3.11
* **AI Engine:** Google GenAI SDK (`gemini-2.5-flash`)
* **Database:** Google Sheets API (`gspread`)
* **Utilities:** Python-Dotenv

## 🚀 The Automation Pipeline
1.  **Trigger:** Scheduled run (Daily via GitHub Actions).
2.  **Generate (Task 1):** Gemini creates 3 unique prompts separated by newlines.
3.  **Parse & Validate:** Splits the text into a Python list `['Prompt 1', 'Prompt 2', 'Prompt 3']` and verifies integrity.
4.  **Bulk Store (Task 2):** Sends the entire list to Google Sheets using `append_rows`.

## ⚙️ Configuration (Environment Variables)
Create a `.env` file in the root directory:
```ini
GOOGLE_API_KEY=your_gemini_api_key
```
*Note: Requires `chatbot_key.json` for Google Service Account access.*

## 📦 Local Installation

1. **Clone the Repository**
```bash
git clone https://github.com/viochris/daily-batch-prompt-bot.git
cd daily-batch-prompt-bot
```

2. **Install Dependencies**
```bash
pip install prefect gspread python-dotenv google-genai
```

3. **Run the Automation**
```bash
python auto-multiple-prompt-generate.py
```

### 🖥️ Expected Output
You should see **Prefect** orchestrating the tasks in real-time:

```text
🚀 Starting Image Prompt Generator Flow...
06:00:01.123 | INFO    | Task run 'Generate Image Prompt' - 🎨 Requesting 3 creative prompts from Gemini...
06:00:02.456 | INFO    | Task run 'Generate Image Prompt' - ✅ Generated Prompts (Raw String): A futuristic cyberpunk...
06:00:03.789 | INFO    | Task run 'Append to Spreadsheet' - 📊 Connecting to Google Sheets...
06:00:04.223 | INFO    | Task run 'Append to Spreadsheet' - ✅ Successfully appended 3 prompts to the sheet.
06:00:04.556 | INFO    | Flow run 'Image Prompt Generator Flow' - ✅ Flow completed successfully.
```

## 🚀 Deployment Options
This bot supports two release methods depending on your infrastructure:
| Method | Description | Use Case |
| --- | --- | --- |
| **GitHub Actions** | **Serverless.** Uses `cron` scheduling in `.github/workflows/daily_batch_prompt.yml`. Runs on GitHub servers for free. | Best for daily/scheduled runs without paying for a VPS. |
| **Local / VPS** | **Always On.** Uses `main_flow.serve()` to run as a background service on your own server or Docker container. | Best if you need complex triggers or continuous operation. |

## ⚠️ Customization Note (Scaling Up)
This script is currently configured to generate **3 prompts** per run.

* **Want more?** You can easily increase this to 5, 10, or more by modifying the `prompt_instruction` variable in the script.
* **Caution:** If you scale up significantly (e.g., 20+ prompts), you may need to adjust the `max_output_tokens` in the Gemini configuration or refine the parsing logic to ensure the AI doesn't truncate the output or hallucinate formatting.

---

**Author:** [Silvio Christian, Joe](https://www.linkedin.com/in/silvio-christian-joe)
*"Automate the boring stuff, generate the beautiful stuff."*
