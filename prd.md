# 🧾 ZeroInvoice – PRD v1.1 (ZeroDB-Only Architecture)

**Product Name**: ZeroInvoice
**Type**: AI-native, multi-tenant Invoicing SaaS
**Built On**: **ZeroDB only** (no external DB or vector service)

---

## 1. 🎯 Executive Summary

ZeroInvoice is a 100% ZeroDB-native invoicing SaaS for freelancers and SMBs. It offers client and invoice management, semantic search, smart reminders, and AI drafting with ZeroDB's embedded vector database, metadata filtering, and context-aware assistant support via MCP — all with zero reliance on external models or relational databases.

---

## 2. 🔑 Key Capabilities (All Native to ZeroDB)

| Feature               | Backed By                     |
| --------------------- | ----------------------------- |
| Client & Invoice Data | Vector + Metadata             |
| Smart Search          | ZeroDB Vector Search          |
| Embedding Storage     | Generated in-app              |
| Invoice Drafting      | MCP Contexts + Assistant      |
| PDF + Email Export    | External (html2pdf.js + SMTP) |
| Reminders & Logs      | Namespace + metadata          |
| Multi-User Access     | ZeroDB Project Isolation      |
| Collaboration         | Vector Locking                |
| Assistant Memory      | MCP Contexts                  |
| Metadata Queries      | Embedded in vector records    |

---

## 3. 🧠 Core Features

All features below are MVP and run entirely on ZeroDB:

### ✅ 3.1 Client Management

* Add/edit/delete clients
* Store structured metadata + semantic notes in `clients` namespace

### ✅ 3.2 Invoice Management

* Create/edit/delete invoices with line items and due dates
* Store as vector records with invoice metadata
* No separate DB needed

### ✅ 3.3 Payment Tracking

* Log payments as vectors
* Link via `invoice_id` + `client_id` metadata
* Track balance status

### ✅ 3.4 AI-Powered Assistant (ZeroDB MCP)

* Auto-draft invoice from prior messages
* Suggest line items from conversation context
* Support 8192 token context windows

### ✅ 3.5 Smart Semantic Search

* "Invoices over \$1000 in June"
* "Unpaid Acme Corp invoices"
* Query by metadata + similarity

### ✅ 3.6 PDF Export & Email Send

* Render invoice as HTML → PDF
* Email via SMTP or SendGrid API

### ✅ 3.7 Reminders & Status Monitoring

* Filter overdue invoices by due date
* Trigger email reminders (metadata + cron)

### ✅ 3.8 Multi-User & Access Control

* ZeroDB Project per user or team
* Store user ID as metadata

### ✅ 3.9 Vector Locking

* Use `vector_id` locks for concurrent edits

### ✅ 3.10 Audit Trail

* All actions logged in `logs` namespace
* Metadata: `user_id`, `action`, `timestamp`

---

## 4. 🗃️ Namespaces & Data Structure

| Namespace      | Purpose                            |
| -------------- | ---------------------------------- |
| `clients`      | Client records + embedded notes    |
| `invoices`     | Invoice content with metadata      |
| `payments`     | Payment records linked to invoices |
| `mcp_messages` | Assistant context window           |
| `logs`         | Audit trail (who did what, when)   |

---

## 5. 🧾 Vector Metadata Schema (Invoice Example)

```json
{
  "id": "inv_20250723_abc",
  "type": "invoice",
  "client_id": "client_001",
  "user_id": "user_123",
  "amount": 1500.00,
  "currency": "USD",
  "status": "unpaid",
  "due_date": "2025-07-31",
  "created_at": "2025-07-23T10:00:00Z",
  "tags": ["monthly", "design"],
  "description": "Design retainer for July"
}
```

---

## 6. ⚙️ Architecture Overview (All ZeroDB)

**No PostgreSQL. No OpenAI.**

```
[ Web App UI (React) ]
       |
       V
[ FastAPI Proxy (optional for auth/email)]
       |
       V
[ ZeroDB Cloud API ]
   - Vector Search
   - Metadata Filtering
   - Context Memory (MCP)
   - Vector Locking
```

---

## 7. 🔌 API Calls Used

| Function            | Endpoint                                              |
| ------------------- | ----------------------------------------------------- |
| Create Project      | `POST /api/v1/public/projects`                        |
| Store Data          | `POST /api/v1/public/v1/vectors`                      |
| Semantic Search     | `POST /vectors/project/{id}/search`                   |
| Lock Resource       | `POST /vectors/locks`                                 |
| AI Assistant Memory | `POST/PUT /mcp/contexts`, `vectors` in `mcp_messages` |

---

## 8. 🧪 MVP Test Plan

* [ ] Add client → verify stored in `clients`
* [ ] Create invoice → validate metadata
* [ ] Run vector search by phrase and metadata
* [ ] Trigger reminder → check email sent
* [ ] Assistant draft → uses past context (MCP)
* [ ] PDF export → matches invoice content
* [ ] Multi-user separation via project\_id

---

## 9. 🧱 Tech Stack (Simplified)

| Layer        | Stack                              |
| ------------ | ---------------------------------- |
| Frontend     | React + Tailwind + html2pdf.js     |
| Backend      | FastAPI (API routing, email)       |
| Storage      | **ZeroDB only**                    |
| AI Assistant | MCP Context + Local prompt handler |
| Auth         | JWT via ZeroDB login               |

---

## 10. ⏱️ Rapid Sprint Plan (Agent-Executable)

| Hour | Focus                                  |
| ---- | -------------------------------------- |
| 1    | Project creation + Auth setup          |
| 2    | Client + Invoice forms → vector upsert |
| 3    | Payment logging + PDF rendering        |
| 4    | MCP-based assistant UI                 |
| 5    | Semantic search filters                |
| 6    | Reminder system + Audit log viewer     |

---

## ✅ Final Notes

* ZeroDB fully replaces PostgreSQL and OpenAI in this product.
* Embedding generation can be local (Python/JS) using lightweight models or MCP agents.
* All user + document data stored and queried through ZeroDB’s vector + metadata system.

---
