# ⚡ Deterministic Lead Identity Resolution & GHL CRM Routing — n8n Workflow

An automated end-to-end identity resolution engine built with n8n. It ingests inbound lead payloads across multi-channel sources (Meta Ads, Web Forms, VAPI, LinkedIn), deterministically resolves duplicates via dual GoHighLevel (GHL) phone/email lookups, updates CRM records, and maintains an audit-ready 3-tab Google Sheet database.

---

## ⚙️ What It Does

> 💡 **Workflow Overview**
> 
> * **1. Lead Ingestion & Normalization:** Captures real-time incoming webhooks, extracts standardized contact details, and constructs idempotency keys.
> * **2. Idempotency Screening:** Queries the master database to bypass already processed execution IDs and prevent duplicate processing.
> * **3. Dual Stream GHL Search:** Executes parallel lookups against GoHighLevel CRM using normalized phone numbers and email addresses.
> * **4. Deterministic Matching:** Routes leads through a 3-way decision tree (`create_new`, `update_existing`, `manual_review`) to handle clean merges or conflict flags.
> * **5. Multi-Tab Audit Logging:** Appends mapping IDs, processing status, and resolution decisions across a centralized 3-tab Google Sheet ledger.

---

## 🖼️ System Screenshots

| Workflow Architecture | Dual GHL Search & Match Logic |
| :---: | :---: |
| <img src="./Screenshot%202026-09-15%20204057.png" width="100%" /><br><br><img src="./Screenshot%202026-09-15%20201134.png" width="100%" /><br><br><img src="./Screenshot%202026-09-15%20194428.png" width="100%" /> | <img src="./Screenshot%202026-09-15%20210509.png" width="100%" /><br><br><img src="./Screenshot%202026-09-15%20205238.png" width="100%" /><br><br><img src="./Screenshot%202026-09-15%20205059.png" width="100%" /> |

| Centralized Master Database |
| :---: |
| <img src="./Screenshot%202026-09-16%20010914.png" width="100%" /><br><br><img src="./Screenshot%202026-09-16%20010854.png" width="100%" /><br><br><img src="./Screenshot%202026-09-16%20010842.png" width="100%" /> |

---

## ⚡ Features & System Capabilities

| Feature | Description |
| :--- | :--- |
| 📥 **Multi-Source Ingestion** | Handles webhooks from Meta Ads, Web Forms, VAPI voice calls, and LinkedIn campaigns. |
| 🛡️ **Idempotency Guard** | Generates `source:source_record_id` keys to ensure zero duplicate downstream executions. |
| 🔍 **Dual Lookup Matching** | Runs simultaneous GHL API calls for both Email and Phone fields to detect cross-channel matches. |
| 🔀 **3-Way Routing Engine** | Automatically creates new contacts, updates single matches, or flags multi-match conflicts. |
| 📊 **3-Tab Master Ledger** | Centralizes external mapping, processing logs, and decision tracking in a single Google Sheet. |

---

## 📋 Master Database Schema

**Google Sheet Name:** `GHL Lead Identity Resolution DB`

| Tab Name | Key Columns | Purpose / Description |
| :--- | :--- | :--- |
| 🔗 **`External ID Mapping`** | `source`, `source_record_id`, `ghl_contact_id`, `created_at` | Maps external lead IDs to resolved GoHighLevel Contact IDs. |
| 📝 **`Processing Log`** | `idempotency_key`, `record_id`, `processed_at`, `status` | Records incoming payload attempts for execution auditing. |
| ⚖️ **`Decision Log`** | `idempotency_key`, `processed_at`, `status` | Logs routing outcomes (`create_new`, `update_existing`, `manual_review`). |

---

## 🔄 Workflow Execution Pipeline

| Step | Phase | Action / Node Executed | Description |
| :---: | :--- | :--- | :--- |
| **01** | **Ingestion** | `Webhook Trigger` | Receives live HTTP POST payloads from lead sources. |
| **02** | **Normalization** | `Extract Data Code Node` | Standardizes phone formats, extracts record IDs, and formats payload fields. |
| **03** | **Idempotency** | `Check Idempotency Node` | Reads `Processing Log` tab to check if the incoming record key was already handled. |
| **04** | **GHL Search** | `GHL Phone & Email Search` | Queries GoHighLevel CRM contacts API simultaneously using phone and email. |
| **05** | **Resolution** | `Evaluate GHL Match Node` | Compares lookup results to determine `create_new`, `update_existing`, or `manual_review`. |
| **06** | **CRM Action** | `Create / Update GHL Contact`| Executes API write commands to GoHighLevel CRM based on resolution state. |
| **07** | **Logging** | `Google Sheets Nodes` | Writes record entries to `External ID Mapping`, `Processing Log`, and `Decision Log`. |
| **08** | **Response** | `Respond to Webhook` | Delivers structured JSON status response back to the caller. |

---

## 🛠️ Tech Stack & Integration Ecosystem

| Tool / Technology | Role in Workflow |
| :--- | :--- |
| ⚡ **n8n** | Primary workflow orchestration and dynamic decision routing engine |
| 🎯 **GoHighLevel (GHL)** | Target CRM system for contact creation, lookup, and updates |
| 📊 **Google Sheets API** | Master database holding mapping, processing, and decision logs |
| 📜 **JavaScript (ES6+)** | Idempotency key building, array filtering, and fallback expression logic |

---

## 💡 Practical Use Cases

| Business Scenario | Problem Solved | Operational Impact |
| :--- | :--- | :--- |
| **Multi-Channel Lead Campaigns** | Duplicate contacts created when leads submit forms across Meta Ads and Web | 100% accurate contact unification in CRM |
| **Cross-Data Conflicts** | Phone matches Contact A, but Email matches Contact B | Prevents dirty overwrites by routing to `manual_review` |
| **Webhook Retry Storms** | External platforms retrying webhooks and causing duplicate CRM rows | Instant execution bypass via strict idempotency checks |

---

## 🚀 Setup & Execution Guide

| Step | Task | Details |
| :---: | :--- | :--- |
| **01** | **Import Workflow** | Open n8n ➔ Click **Import from file** ➔ Upload `workflows/lead-identity-resolution.json`. |
| **02** | **Setup Master Sheet** | Create `GHL Lead Identity Resolution DB` with 3 tabs: `External ID Mapping`, `Processing Log`, `Decision Log`. |
| **03** | **Set OAuth Credentials**| Attach **GoHighLevel API** and **Google Sheets OAuth2** credentials in n8n. |
| **04** | **Activate System** | Switch workflow toggle to **Active** to start processing incoming lead webhooks in real time. |

---

## 🎬 Live Demo & Walkthrough

> ### 🚀 [▶️ Watch Full Workflow Execution Demo]([YOUR_LINKEDIN_LOOM_POST_URL](https://lnkd.in/p/dKH3Y9ui))
> **Platform:** LinkedIn / Loom  
> **What You'll See:** Real-time webhook ingestion ➔ Dual GHL API search ➔ Deterministic match decision ➔ CRM update ➔ 3-Tab Google Sheet audit logging.

---

## 📜 License

MIT License — Free to use, modify, and deploy for personal or commercial projects.
