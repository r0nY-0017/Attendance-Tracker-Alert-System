# 📋 Automated Attendance Alert System
### n8n + Google Sheets + Gmail

An automated workflow built with **n8n** that monitors employee attendance from a Google Sheet daily and sends personalized email alerts to employees who are **Late**, **Absent**, or on **Leave** — with different messages for each status.

---
<img width="1253" height="560" alt="Screenshot 2026-03-17 003440" src="https://github.com/user-attachments/assets/05894caa-b1be-4d13-bfb0-4226f02c2da0" />



<img width="1898" height="937" alt="Screenshot 2026-03-17 004314" src="https://github.com/user-attachments/assets/41e6f0bd-eb95-4a8b-8799-99e462f61bdd" />



## ✨ Features

- 🕘 **Daily Auto-Run** — Triggers every morning at 11:00 AM automatically
- 📅 **Smart Date Detection** — Finds today's exact date column from the sheet (handles future empty columns correctly)
- 📧 **3 Different Email Templates** — Unique messages for Late, Absent, and Leave statuses
- ✅ **Skips Present employees** — No unnecessary emails
- 🔄 **Fully Automated** — Zero manual effort after setup

---

## 🗂️ Project Structure

```
attendance-alert/
├── attendance_workflow.json     # n8n workflow — import this
├── README.md                    # You are here
```

---

## 📊 Google Sheet Format

Your sheet must follow this structure:

| EMP ID | Name | Email | 03/16/2026 | 03/17/2026 | 03/18/2026 |
|--------|------|-------|------------|------------|------------|
| XXXXX | Md Mehedi Hasan | mehedi@gmail.com | Present | Late | |

**Column rules:**
- Columns A, B, C → `EMP ID`, `Name`, `Email` (exact spelling required)
- Date columns → format must be `MM/DD/YYYY` (e.g. `03/17/2026`)
- Status values → `Present`, `Late`, `Absent`, `Leave`, `Weekend`

---

## ⚙️ How It Works

```
Schedule Trigger (11AM)
        │
        ▼
Google Sheets — Read all rows
        │
        ▼
Code Node — Find today's date column → check each employee's status
        │
        ├── Late?    ──▶  ⚠️ Send Late Warning Email
        │
        ├── Absent?  ──▶  🚨 Send Absent Alert Email
        │
        ├── Leave?   ──▶  📋 Send Leave Reminder Email
        │
        └── Present  ──▶  ✅ Skip (no email)
```

---

## 📧 Email Templates

### ⚠️ Late Warning
- **Subject:** `⚠️ Late Attendance Alert — MM/DD/YYYY`
- **Message:** Politely reminds the employee about being late and warns about repeated lateness affecting performance review.

### 🚨 Absent Alert
- **Subject:** `🚨 Absent Alert — MM/DD/YYYY`
- **Message:** Notifies the employee of their absence and requests them to contact HR within 24 hours with a valid reason.

### 📋 Leave Reminder
- **Subject:** `📋 Leave Confirmation — MM/DD/YYYY`
- **Message:** Confirms the employee is on leave and reminds them to ensure it is pre-approved by HR.

---

## 🚀 Setup Guide

### Prerequisites
- [n8n](https://n8n.io/) installed (self-hosted or cloud)
- Google account with access to Google Sheets
- Gmail account for sending emails

### Step 1 — Import Workflow
1. Open n8n → click **"New Workflow"**
2. Click `⋯` menu → **"Import from File"**
3. Select `attendance_workflow.json`

### Step 2 — Connect Google Sheets
1. Click the **Google Sheets** node
2. Under **Credentials** → click **"Create New"**
3. Authenticate with your Google account
4. Replace `YOUR_GOOGLE_SHEET_ID_HERE` with your actual Sheet ID

> 🔍 Find your Sheet ID in the URL:
> `https://docs.google.com/spreadsheets/d/`**`THIS_IS_YOUR_ID`**`/edit`

### Step 3 — Connect Gmail
1. Click any **Gmail** node (there are 3 — Late, Absent, Leave)
2. Under **Credentials** → click **"Create New"**
3. Authenticate with your Gmail account
4. Apply the same credential to all 3 Gmail nodes

### Step 4 — Activate
1. Click **"Save"**
2. Toggle the workflow to **Active**
3. Done! It will run every morning at 11:00 AM ✅

---

## 🧠 Core Logic — Code Node

The Code node finds **today's exact date column** dynamically:

```javascript
// Build today's date string in MM/DD/YYYY format
const now = new Date();
const mm = String(now.getMonth() + 1).padStart(2, '0');
const dd = String(now.getDate()).padStart(2, '0');
const yyyy = now.getFullYear();
const todayStr = `${mm}/${dd}/${yyyy}`;

// Find matching column in the sheet (handles both "3/16/2026" and "03/16/2026")
const todayKey = dateKeys.find(k => {
  const parts = k.split('/');
  const kMM = parts[0].padStart(2, '0');
  const kDD = parts[1].padStart(2, '0');
  return `${kMM}/${kDD}/${parts[2]}` === todayStr;
});
```

This ensures future empty columns are ignored and only today's data is processed.

---

## 🛠️ Customization

| What to change | Where |
|---|---|
| Trigger time (default 9 AM) | **Schedule Trigger** node → `triggerAtHour` |
| Sheet name (default Sheet1) | **Google Sheets** node → Sheet Name |
| Email language/content | Inside each **Gmail** node → message body |
| Add more statuses (e.g. Half-Day) | **Code Node** → add new `emailType` condition |

---

## 🔧 Troubleshooting

**No emails being sent?**
- Check that today's date column exists in the sheet with the correct format (`MM/DD/YYYY`)
- Make sure the `Email` column has valid email addresses
- Test manually: open the workflow → click **"Test Workflow"**

**Wrong column being read?**
- Ensure date headers in your sheet are plain text, not formatted as date cells
- The column header must exactly match `MM/DD/YYYY` format

**Gmail authentication error?**
- Re-connect your Gmail credential in all 3 Gmail nodes
- Make sure Gmail API is enabled in your Google Cloud Console

---

## 📌 Notes

- The workflow skips employees marked as `Present` or `Weekend` — no email is sent
- Empty status cells are treated as `Absent`
- All emails are sent from the authenticated Gmail account
- n8n timezone should match your local timezone for accurate 11 AM trigger

---

## 📄 License

This project is open for personal and internal business use. Feel free to modify the workflow and email templates to fit your organization's needs.

---

<p align="center">Built with ❤️ using <a href="https://n8n.io">n8n</a> • Google Sheets • Gmail</p>
