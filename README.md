# 🎯 Cold Email Auto-Generator

A Google Apps Script that automatically generates hyper-targeted B2B cold emails using AI, based on customer pain points — and saves them directly as Gmail drafts.

\---

## ✨ Features

* **Sniper Mode** — focuses on a single, most-relevant pain point per customer instead of generic pitching
* **3-layer pain point matching** — matches by Client URL → Industry → Category, in order of precision
* **AI-generated email copy** — calls Gemini API to write professional, concise cold emails under 120 words
* **Auto Gmail draft creation** — saves each generated email directly to your Gmail Drafts folder
* **Status tracking** — updates each customer row with `Done`, `No Data`, or `Error` after processing
* **Only processes `Waiting` rows** — safe to re-run without duplicating work

\---

## 📋 Prerequisites

* A Google account with access to Google Sheets and Gmail
* A [Gemini API Key](https://aistudio.google.com/app/apikey)
* Google Sheets set up with the required structure (see below)

\---

## 🗂️ Spreadsheet Structure

This script expects **two Google Spreadsheets**:

### Main Spreadsheet (where the script lives)

|Sheet Name|Required Columns|
|-|-|
|`Customer\\\_List`|`Company\\\_Name`, `Client\\\_URL`, `Industry`, `Category`, `Recommended\\\_Model`, `Status`, `Email\\\_Output`|
|`Product\\\_Master`|`Model`, `product\\\_name`, `Unique Selling Point`, `Benefits`, `Specs`|
|`Company\\\_Profile`|`Brand\\\_Name`, `Core\\\_Business`, `Manufacturing\\\_Value`, `ODM\\\_OEM\\\_Strength`|

### External Pain Point Database (separate spreadsheet)

|Sheet Name|Required Columns|
|-|-|
|`Pain\\\_Point\\\_DB`|`Client URL`, `Industry`, `Category`, `Pain`, `Solution`|

\---

## ⚙️ Setup

### 1\. Copy the script

Open your main Google Spreadsheet → **Extensions → Apps Script** → paste the script content.

### 2\. Fill in your credentials

At the top of the script, update the config section:

```javascript
const PAIN\\\_POINT\\\_DB\\\_ID = 'YOUR\\\_SPREADSHEET\\\_ID\\\_HERE';
const GEMINI\\\_API\\\_KEY   = 'YOUR\\\_GEMINI\\\_API\\\_KEY\\\_HERE';
const GEMINI\\\_MODEL     = 'gemini-2.5-flash';
```

> \\\*\\\*How to find a Spreadsheet ID:\\\*\\\* Open the spreadsheet → copy the long string from the URL between `/d/` and `/edit`.

### 3\. Grant permissions

On first run, Google will ask you to authorize access to Gmail and Google Sheets. Click **Allow**.

### 4\. Set customer status to `Waiting`

In `Customer\\\_List`, set the `Status` column to `Waiting` for any rows you want to process.

### 5\. Run

In the Apps Script editor, select the `generateEmails` function and click **Run**.

\---

## 🔄 Workflow

```
Customer\\\_List (Status = "Waiting")
        ↓
Match pain point from Pain\\\_Point\\\_DB
  └── 1st: exact Client URL match
  └── 2nd: same Industry
  └── 3rd: filter by Category
        ↓
Build prompt → Call Gemini API
        ↓
Write email to Email\\\_Output column
Create Gmail draft
Update Status → "Done"
```

\---

## 🤖 Email Generation Rules (Prompt Design)

The AI is instructed to:

* Write under **120 words**
* Focus on **one pain point only** — no invented issues
* Present the solution as a **direct answer** to that pain
* End with a **clear CTA**
* Skip filler phrases like "I hope this email finds you well"
* Omit company history and background
* Output email body only (no subject line)

The subject line is auto-generated as: `Kupo Mounting Solutions for {Company\\\_Name}`

\---

## ⚠️ Notes

* The script sleeps 2 seconds between API calls to avoid rate limiting
* `maxOutputTokens` is set to `8192` to prevent truncated output
* Safety filters are set to `BLOCK\\\_NONE` to prevent hardware-related terms (e.g., *mount*, *shoot*, *tight*) from being flagged
* If a customer has no matching pain point data, their status is set to `No Data` and they are skipped

\---

## 📁 File Structure

```
├── Code.gs               # Main Apps Script file
├── README.md             # This file
```

\---

## 🛠️ Customization

|What you want to change|Where to change it|
|-|-|
|AI model version|`GEMINI\\\_MODEL` constant|
|Email word limit|`under 120 words` in the prompt string|
|Email tone|Modify the `## Instructions` section of the prompt|
|Subject line format|`const subject = ...` line in `generateEmails()`|
|Number of pain points used|Change `relevantPains\\\[0]` to use more entries|

\---

## 📄 License

MIT License — free to use and modify.

