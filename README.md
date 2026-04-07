# 🕵️‍♂️ AI Code Auditor Bot (n8n + Gemini)

A powerful [n8n](https://n8n.io/) workflow that turns a Telegram bot into an automated Senior QA and Security Engineer. Simply send a `.zip` file of your codebase to the bot, and it will unpack, read, and analyze your code using Google's Gemini AI, returning a comprehensive security and health report right in your chat.

## ✨ Features

- **📱 Telegram Integration:** Seamlessly accepts `.zip` document uploads and returns beautifully formatted text reports.
- **🗜️ Automated Extraction:** Extracts ZIP files on the fly and reads the internal file structures.
- **🧠 Smart File Filtering:** Automatically ignores non-code files and heavy directories (like `node_modules/`, `.git/`, `dist/`, images, etc.) to save API tokens.
- **🤖 Gemini AI Powered:** Uses Google's advanced Gemini LLM to act as a senior web security and QA engineer.
- **🛡️ Deep Security Scanning:** Detects OWASP vulnerabilities, plaintext passwords, bad data types, and performance bottlenecks.
- **⚙️ n8n Binary Data Handling:** Custom JavaScript safely handles n8n's `filesystem-v2` binary data extraction natively.

## 📋 Prerequisites

To run this workflow, you will need:
1. An instance of **n8n** (Cloud or Self-Hosted).
2. A **Telegram Bot Token** (Create one via [@BotFather](https://t.me/botfather) on Telegram).
3. A **Google Gemini API Key** (Get one from Google AI Studio).

## 🚀 Installation & Setup

1. **Clone or Download** this repository.
2. Open your n8n instance and click **Add Workflow**.
3. Select **Import from File** and upload the `workflow.json` file (or copy-paste the JSON directly into the n8n editor).
4. **Configure Credentials:**
   - Open the **Telegram Trigger** node and add your Telegram Bot Token.
   - Open the **Gemini Analysis** (HTTP Request) node and replace the placeholder API key in the URL (`key=YOUR_API_KEY`) with your actual Gemini API key.
5. **Activate the Workflow:** Toggle the workflow to **Active** in the top right corner of n8n.

## 🛠️ How it Works

1. **Trigger:** The user sends a document to the Telegram bot.
2. **Validation:** The workflow checks if the document is a valid `application/zip` file. If not, it politely asks the user for a ZIP.
3. **Extraction:** n8n downloads the file and extracts the contents.
4. **Parsing (Code Node):** A custom JavaScript node loops through the extracted files, filters out junk directories, and concatenates the readable code into a bundle (capped at 90,000 characters to respect LLM limits).
5. **Prompt Building:** The code bundle is wrapped in a strict prompt instructing the AI to look for critical bugs, OWASP vulnerabilities, and quick wins.
6. **AI Analysis:** The prompt is sent to the Gemini API.
7. **Formatting & Delivery:** The AI's markdown response is sanitized for Telegram's markdown requirements and sent back to the user.

## 📊 Example Output

When you upload a ZIP file containing Node.js/Sequelize models, the bot replies with:

```text
✅ Website Test Complete

Overall Score: 50/100
Decoded Files: 3
Total Files Seen: 3

1) Overall health score out of 100
50/100 - The models are structured reasonably... However, a critical security flaw in password storage significantly reduces the score.

2) CRITICAL issues
User.js: Plaintext password storage. The password field in the User model is defined as DataTypes.STRING without any indication of hashing or salting...

3) HIGH severity bugs
ShoppingList.js: Inconsistent and imprecise data type for price. The price field uses DataTypes.FLOAT, which can lead to floating-point precision issues...

4) Security vulnerabilities with OWASP category
A02:2021-Cryptographic Failures: Storing User.password as DataTypes.STRING without hashing or salting. (CRITICAL)
```

## ⚙️ Configuration

If you want to adjust the files the bot reads or the size of the codebase it can handle, edit the **Analyze Files + Stack + OWASP** Code node:
- Modify `allowed` regex to add/remove supported file extensions.
- Modify `skip` regex to add ignored directories.
- Adjust `MAX_TOTAL_CHARS` based on your Gemini model's context window.

## 🤝 Contributing

Pull requests are welcome! If you find a way to optimize the prompt or improve the n8n binary file extraction, feel free to open an issue or submit a PR.
