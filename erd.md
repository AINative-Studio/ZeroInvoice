# 📊 Entity Relationship Diagram (ERD) – ZeroInvoice (ZeroDB)

## 🧾 Overview

This ERD represents logical relationships between vector-based entities in ZeroDB for the ZeroInvoice application.

---

## 🧩 Entities & Relationships

### 1. `Client`

* `id` (PK)
* `name`
* `email`
* `phone`
* `notes`
* `created_at`
* `user_id` (FK → User)

**Relationships:**

* Has many `Invoices`
* Has many `Payments`

---

### 2. `Invoice`

* `id` (PK)
* `client_id` (FK → Client)
* `user_id` (FK → User)
* `status`
* `amount`
* `currency`
* `due_date`
* `line_items`
* `tags`
* `created_at`

**Relationships:**

* Belongs to `Client`
* Has many `Payments`
* Related to `AuditLog`
* Related to `InvoiceDraft`

---

### 3. `Payment`

* `id` (PK)
* `invoice_id` (FK → Invoice)
* `client_id` (FK → Client)
* `amount`
* `currency`
* `received_at`
* `method`
* `user_id`

---

### 4. `AuditLog`

* `id` (PK)
* `user_id` (FK → User)
* `action`
* `entity_id`
* `timestamp`
* `details`

**Relationships:**

* Belongs to `User`
* Refers to any entity (`entity_id` = Invoice, Payment, etc.)

---

### 5. `AssistantMessage`

* `id` (PK)
* `role`
* `session_id`
* `project_id`
* `content`
* `timestamp`
* `user_id`

**Relationships:**

* Grouped by session for MCP
* Linked to `InvoiceDraft` generation

---

### 6. `InvoiceDraft`

* `id` (PK)
* `user_id`
* `client_id` (FK → Client)
* `line_items`
* `total`
* `summary`
* `session_id` (FK → AssistantMessage)
* `timestamp`

**Relationships:**

* Belongs to `Client`
* Generated from `AssistantMessage` MCP context
