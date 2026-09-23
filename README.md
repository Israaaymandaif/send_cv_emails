# Send CV Emails

An automated job-application tool that turns an Excel contact list into a personalized, trackable email campaign — sending a CV to each recruiter individually via Gmail, logging delivery status back into the same sheet, and running entirely inside Google Colab with no local installation.

---

## Overview

This project started as a manual task — copying a CV into dozens of individual emails — and was turned into a resumable, spreadsheet-driven automation that can be safely re-run at any time without duplicate sends.

| | |
|---|---|
| **Environment** | Google Colab (browser-based, no local install) |
| **Language** | Python |
| **Spreadsheet I/O** | `openpyxl` |
| **Email Delivery** | Gmail SMTP (SSL) via `smtplib` |
| **Authentication** | Gmail App Password, entered securely at runtime |
| **Data Store** | Excel tracker (`.xlsx`) — doubles as input list and status log |

---

## Core Features

### 1. Excel-Driven Sending
- The recruiter list, and every recruiter's send status, live in a single Excel file — no code changes needed to add, remove, or re-order recipients
- Each row tracks: email address, whether it's been sent, the date sent, and any failure reason
- Adding a new company is as simple as adding a new row before the next run

### 2. Resumable, Duplicate-Safe Runs
- Before sending, the script checks each row's status column and **skips anyone already marked as sent**
- The tracker file is saved to disk immediately after every single email, not just at the end — so if the run is interrupted (browser closed, connection drop, hitting the per-run cap), nothing is lost and nothing gets double-sent
- This makes the tool safe to run in batches across multiple sessions until the full list is contacted

### 3. Rate-Limited, Spam-Safe Delivery
- A configurable delay between sends (default 20 seconds) spaces out the traffic to look like natural sending behavior rather than a bulk blast
- A per-run cap (default 40 emails) keeps each session well within Gmail's sending limits for personal accounts
- Emails are sent **individually** (one `To:` address per message), not as a mass BCC, so each recruiter only ever sees their own address

### 4. Zero Local Setup
- Runs entirely inside a Google Colab notebook — the CV and Excel tracker are uploaded through the notebook's own file-picker UI
- Gmail credentials are entered through a masked runtime prompt (`getpass`) and are **never written into the notebook file or committed to the repository**
- No Python installation, virtual environment, or terminal required on the user's machine

### 5. Per-Recipient Error Handling
- If a single send fails (invalid address, malformed domain, etc.), the script logs the specific error into the tracker's notes column and **continues with the rest of the list** instead of stopping the whole run
- This means one bad email address never blocks the remaining recruiters from being contacted

---

## Tech Stack

- **Python** — core scripting logic
- **`openpyxl`** — reading and writing the Excel tracker in place
- **`smtplib` + `email.message`** — authenticated Gmail SMTP delivery over SSL
- **Google Colab (`google.colab.files`)** — in-browser file upload/download, replacing local file I/O
- **`getpass`** — secure, hidden credential entry at runtime

---

## Project Evolution

1. **Manual process** — copying the CV and pasting it into individual emails one company at a time
2. **Local script prototype** — a standalone Python script (`smtplib` + `openpyxl`) requiring a local Python install and a hardcoded config section
3. **Colab migration** — rebuilt as a notebook to remove the local-install requirement entirely, moved credentials from hardcoded variables to a secure runtime prompt, and added file upload/download cells so the whole workflow runs in the browser

---

## Files

| File | Purpose |
|---|---|
| `send_cv_emails_colab.ipynb` | Main notebook — upload files, authenticate, send, download updated tracker |
| `job_emails_tracker.xlsx` | Recruiter contact list and send-status log (input **and** output) |
| `Israa_Daif_CV_Engineering.pdf` | CV attached to every outgoing email |
| `README.md` | This file |

---

## Notes

This repository is kept **private** — it contains a real personal recruiter contact list and is built around one person's active job search rather than as a general-purpose tool. Gmail credentials are never stored in code; they're supplied fresh each time the notebook is run, which is also why the repository is safe to keep even if visibility settings ever change.
