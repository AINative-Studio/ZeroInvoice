# 🏁 ZeroInvoice Frontend Sprint Plan (MVP)

**Sprints**: 3 Total (5 days each)
**Team**: 1–2 Frontend Engineers, 1 Product Designer (optional), 1 QA Agent (automated)

---

## 🔄 Sprint 1: Foundation & Core Screens

### 🎯 Goal:

Set up the base architecture, authentication, ZeroDB API integration, and implement core client/invoice UIs.

### 🔧 Tasks:

* ✅ Setup React/Next.js project with Tailwind CSS
* ✅ Implement auth flow: Login, Register, JWT handling
* ✅ Save/load ZeroDB API key from `.env` or secure localStorage
* ✅ Build global layout + navigation bar
* ✅ Create Dashboard placeholder with KPI stubs
* ✅ Implement `ClientList` and `ClientForm`

  * Add/edit/delete UI
  * Upsert to ZeroDB with vector embedding
* ✅ Implement `InvoiceList` and `InvoiceForm`

  * Static form → dynamic form with line items
  * Store metadata + content in ZeroDB
* ✅ Add API error/success toasts
* ✅ Add Loader component

### 🧪 QA Acceptance:

* Auth working with token persistence
* Invoice and client forms correctly submit to ZeroDB
* Vector metadata is stored

---

## 🔄 Sprint 2: Semantic Search, PDF, and Payments

### 🎯 Goal:

Enable invoice/document search, add payment logging, PDF export, and reminders logic.

### 🔧 Tasks:

* ✅ Build global `SearchInput` with semantic query support

  * Integrate with `/vectors/project/{id}/search`
  * Filter by namespace: invoices, clients
* ✅ Add `PaymentEntryForm`

  * Link to invoice metadata
  * Store in `payments` namespace
* ✅ Display invoice balance remaining
* ✅ Generate PDF view for invoices

  * Use `html2pdf.js`
  * Add preview/download button
* ✅ Email send logic (via optional FastAPI proxy)
* ✅ Schedule reminders (UI only, backend stub)
* ✅ Build `Toast`, `ConfirmDialog`, `Pagination`

### 🧪 QA Acceptance:

* Search returns relevant invoices or clients
* Payments reduce invoice balances
* PDF download works for invoice preview

---

## 🔄 Sprint 3: AI Assistant, MCP, and Polish

### 🎯 Goal:

Enable AI assistant with draft generation, refine UX, and complete audit log + settings.

### 🔧 Tasks:

* ✅ Implement `AssistantPanel` (chat + prompt input)
* ✅ Create MCP session via `POST /mcp/contexts`
* ✅ Store user/assistant messages with embeddings
* ✅ Implement invoice draft preview from chat
* ✅ Save assistant-generated draft to `drafts` namespace
* ✅ Add audit log view
* ✅ Add `SettingsPage` to input/view ZeroDB API Key
* ✅ Add mobile responsiveness and polish
* ✅ Final pass on error handling, empty states, loaders

### 🧪 QA Acceptance:

* Assistant returns invoice drafts with relevant memory
* MCP context retrieval working for multi-turn chat
* Settings page updates key without reload

---

## ✅ Deliverables at End of Sprints

* 🔹 **Sprint 1**: Auth + Client/Invoice creation working (static+metadata)
* 🔹 **Sprint 2**: Vector search, PDF, payments integrated
* 🔹 **Sprint 3**: Assistant workflows, polish, and responsiveness

## 🔁 Deployment

* Deploy preview builds daily to Vercel/Netlify
* Feature flags for assistant and draft modules
* Final QA test by agent before release
