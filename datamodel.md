# 🧾 ZeroInvoice Data Model – Relational + Vector

## 📁 Overview

| Layer               | Purpose                                         |
| ------------------- | ----------------------------------------------- |
| **Metadata Fields** | Capture structured relational data (filterable) |
| **Vector Content**  | Enables similarity search and MCP context       |
| **Namespaces**      | Logical separation of entities in ZeroDB        |
| **MCP Contexts**    | Manage assistant memory per user or session     |

---

## 📂 Namespaces

| Namespace      | Description                              |
| -------------- | ---------------------------------------- |
| `clients`      | Stores client info + notes               |
| `invoices`     | Full invoice data + search metadata      |
| `payments`     | Logged payments and status metadata      |
| `mcp_messages` | MCP messages for assistant memory        |
| `logs`         | Audit trail: user actions and timestamps |
| `drafts`       | Invoice drafts created by assistant      |

---

## 📘 Entities

---

### 1. `Client`

| Field        | Type    | Required | Notes                       |
| ------------ | ------- | -------- | --------------------------- |
| `id`         | string  | ✅        | Unique ID (UUID or hash)    |
| `name`       | string  | ✅        | Company or individual name  |
| `email`      | string  | ✅        | Contact email               |
| `phone`      | string  | ❌        | Optional                    |
| `notes`      | string  | ❌        | Stored as vector + metadata |
| `created_at` | ISODate | ✅        | Timestamp                   |
| `user_id`    | string  | ✅        | Owner of client record      |

**Namespace**: `clients`
**Vector Source**: `notes`
**Use Case**: Recall clients based on fuzzy name, note content, or project keywords.

---

### 2. `Invoice`

| Field        | Type    | Required | Notes                             |      |      |           |
| ------------ | ------- | -------- | --------------------------------- | ---- | ---- | --------- |
| `id`         | string  | ✅        | `inv_YYYYMMDD_random`             |      |      |           |
| `client_id`  | string  | ✅        | Linked client ID                  |      |      |           |
| `user_id`    | string  | ✅        | Owner of invoice                  |      |      |           |
| `status`     | enum    | ✅        | \`draft                           | sent | paid | overdue\` |
| `amount`     | float   | ✅        | Total invoice amount              |      |      |           |
| `currency`   | string  | ✅        | Default: `USD`                    |      |      |           |
| `due_date`   | ISODate | ✅        | Due date                          |      |      |           |
| `line_items` | string  | ✅        | JSON string (for AI recall)       |      |      |           |
| `tags`       | array   | ❌        | Searchable tags (e.g., "monthly") |      |      |           |
| `created_at` | ISODate | ✅        | Creation timestamp                |      |      |           |

**Namespace**: `invoices`
**Vector Source**: `line_items + summary`
**Use Case**: Search invoices like “Web design over \$500 due this month”

---

### 3. `Payment`

| Field         | Type    | Required | Notes                           |
| ------------- | ------- | -------- | ------------------------------- |
| `id`          | string  | ✅        | `pay_20250723_xyz`              |
| `invoice_id`  | string  | ✅        | Link to invoice                 |
| `client_id`   | string  | ✅        | Link to client                  |
| `amount`      | float   | ✅        | Payment amount                  |
| `currency`    | string  | ✅        | Should match invoice            |
| `received_at` | ISODate | ✅        | Date of payment                 |
| `method`      | string  | ❌        | E.g., “Stripe”, “Bank Transfer” |
| `user_id`     | string  | ✅        | Owner                           |

**Namespace**: `payments`
**Vector Source**: Optional notes like "Partial via bank"
**Use Case**: Filter by date, method, and link to invoice summaries

---

### 4. `AssistantMessage` (MCP Memory)

| Field        | Type    | Required | Notes                            |             |
| ------------ | ------- | -------- | -------------------------------- | ----------- |
| `id`         | string  | ✅        | Auto-generated UUID              |             |
| `role`       | enum    | ✅        | \`user                           | assistant\` |
| `session_id` | string  | ✅        | Unique conversation thread ID    |             |
| `project_id` | string  | ✅        | ZeroDB project ID                |             |
| `content`    | string  | ✅        | Message body (embedded + stored) |             |
| `timestamp`  | ISODate | ✅        | Time sent                        |             |
| `user_id`    | string  | ✅        | Sender/owner of the message      |             |

**Namespace**: `mcp_messages`
**Vector Source**: `content`
**Use Case**: AI assistant memory, context recall for invoice suggestions

---

### 5. `AuditLog`

| Field       | Type    | Required | Notes                                  |
| ----------- | ------- | -------- | -------------------------------------- |
| `id`        | string  | ✅        | Log ID                                 |
| `user_id`   | string  | ✅        | Who performed the action               |
| `action`    | string  | ✅        | "create\_invoice", "send\_email", etc. |
| `entity_id` | string  | ✅        | ID of affected object                  |
| `timestamp` | ISODate | ✅        | When it happened                       |
| `details`   | string  | ❌        | JSON string or plain text              |

**Namespace**: `logs`
**Vector Source**: Optional summary of the action (for semantic recall)
**Use Case**: Admin view of changes, query by time/user/entity

---

### 6. `InvoiceDraft` (AI Generated)

| Field        | Type    | Required | Notes                               |
| ------------ | ------- | -------- | ----------------------------------- |
| `id`         | string  | ✅        | Auto-generated                      |
| `user_id`    | string  | ✅        | Generated for                       |
| `client_id`  | string  | ✅        | Drafted for this client             |
| `line_items` | string  | ✅        | JSON array of drafted items         |
| `total`      | float   | ✅        | Estimated total                     |
| `summary`    | string  | ✅        | Assistant-generated invoice summary |
| `session_id` | string  | ✅        | Linked to MCP session               |
| `timestamp`  | ISODate | ✅        | When generated                      |

**Namespace**: `drafts`
**Vector Source**: `summary + line_items`
**Use Case**: Retrieve assistant-generated drafts and compare versions

---

## 🧠 Vector Embedding Strategy

| Namespace      | Embedded Field(s)                      | Embedding Source          |
| -------------- | -------------------------------------- | ------------------------- |
| `clients`      | `notes`                                | In-app embedder           |
| `invoices`     | `line_items`, `summary`, `description` | In-app embedder           |
| `payments`     | Optional `notes`                       | In-app embedder (if used) |
| `mcp_messages` | `content`                              | MCP context-aware         |
| `logs`         | `details`                              | Optional                  |
| `drafts`       | `summary`, `line_items`                | Assistant output          |

**Embedding Method**:

* Use lightweight local model (`MiniLM`, `BGE`, etc.) or embed manually via prompt → vector
* Ensure 1536-d vector output
* Normalize and batch if needed

---

