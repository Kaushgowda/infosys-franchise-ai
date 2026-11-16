# FranchiseOps AI — Milestone 3 (Final Application)

**Infosys Springboard 7.0 · Batch 4 · Milestone 3**
FastAPI + SQLite + LangGraph agents + Hybrid RAG + AI Copilot, running on Google Colab (T4 GPU).

---

## 1. What this project is

FranchiseOps AI is an agentic operations platform for a franchise business (outlets, staff, inventory, marketing, customer feedback, audits).
Six agents watch the live data, raise tickets, and a human approves anything that matters. A Copilot answers questions from the database (exact SQL) and from a policy knowledge base (hybrid RAG with citations).

> The app runs on **simulated** sales, stock, attendance and workforce data until a real feed is connected (Data Feed Center).

## 2. Features

| Area | What it does |
|---|---|
| **Login (from Milestone 2)** | Sign in, Administrator Login, sign up with security question, forgot password via security question or e-mailed OTP, bcrypt hashing, 2-hour JWT in an HttpOnly cookie, account locked after 5 wrong passwords (Admin unlocks) |
| **RBAC (from Milestone 2)** | 5 roles, page matrix and capabilities enforced on the server (HTTP 403), outlet scoping for Store Manager and Staff, Copilot topic limits per role |
| **Admin dashboard** | Users table (change role, assign outlet, lock/unlock, delete), add user, permission table, activity log |
| **6 agents** | Workforce, Outlets, Inventory, Marketing, Sentiment, Audit, orchestrated with LangGraph |
| **Command Center / Approvals** | Tickets, approval chains, SLA escalation, autonomy policy |
| **AI Copilot** | Exact read-only SQL answers (shows the SQL and row count), plus hybrid RAG over the knowledge base |
| **Hybrid RAG** | BM25 + dense embeddings fused with Reciprocal Rank Fusion, cross-encoder re-ranking, citations |
| **Languages** | NLLB-200 translation on the GPU (35 languages) |
| **Simulators + Monte Carlo** | What-if models with risk distributions |
| **Digital Twin** | Stack several shocks on a private copy of the data, see the money impact |
| **Data Feed Center** | Upload / folder / URL feeds, validated, bad rows quarantined |
| **Reports** | Print-ready PDFs and CSV exports, optional e-mail |
| **Privacy** | Retention, PII inventory and masking, data-subject export / erase |
| **Persistence** | Database and chats backed up to Google Drive and restored after a Colab reset |

## 3. Roles and access (RBAC)

Same rules as Milestone 2, enforced in `rbac.py`. Page access:

| Role | Copilot | Workforce | Outlets | Inventory | Marketing | Sentiment | Audit | Approvals | Twin | Reports | Notifications | Data feeds | Command | Admin |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Admin | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Franchise Owner | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ |
| Store Manager | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ |
| Staff | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ |
| Auditor | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ | ✗ | ✓ | ✗ |

Every role also has My Profile. The Milestone 3 pages (Approvals to Command) were mapped to roles by the same rules.

- **Run agents / resolve:** Admin and Franchise Owner. **Approve and Data feeds:** Admin and Franchise Owner. **Admin dashboard:** Admin only.
- **Auditor** is read-only.
- **Store Manager and Staff** see only their own outlet (set by an Admin). A scoped user with no outlet sees no data.
- The server answers **403** for any page or action a role may not use, even if the URL is typed by hand.
- The **Copilot** refuses topics the role may not see and other outlets' data.

## 4. Architecture

```
Browser (single-page app, server-driven blocks)
   │  HttpOnly JWT cookie
FastAPI  server.py ── auth.py (login) ── rbac.py (who may do what) ── adminpanel.py
   │                 ── copilot_pro.py / qa.py / rag.py / gpu.py (Qwen2.5, NLLB-200, embedder)
SQLite  (users, outlets, staff, inventory, tickets, approvals, audit_log ...)
   ▲
worker.py  background process: simulator, agent cycles, escalation, feeds
Google Drive: database backup + RAG output (synthetic_kb, bm25_indexes, faiss_indexes)
```

## 5. How to run

1. Run **`FranchiseOpsAI_RAG_Builder_Milestone3.ipynb`** first. It writes `MyDrive/FranchiseOps_AI/synthetic_kb/all_chunks.jsonl` and the FAISS / BM25 indexes.
2. Open this notebook on a **T4 GPU** runtime and run the cells top to bottom.
3. Add the Colab Secrets (notebook access on):

   | Secret | Required | Use |
   |---|---|---|
   | `ADMIN_USERNAME` | yes | Admin username |
   | `ADMIN_PASSWORD` | yes | Admin password |
   | `JWT_SECRET` | yes | 32+ characters |
   | `EMAIL_ADDRESS`, `EMAIL_PASSWORD` | for OTP / reports | Gmail address and app password |
   | `HF_TOKEN` | recommended | Model downloads |
   | `NGROK_AUTHTOKEN` | fallback | Public link if the Colab proxy fails |
   | `PORTAL_ADMIN_USERNAME`, `PORTAL_ADMIN_PASSWORD` | optional | Milestone 1 "Administrator Login" form (defaults `infosys_admin` / `infosys@123`) |

4. **Part 30** starts FastAPI, restores the Drive database and imports the Drive RAG chunks. **Part 31** creates the public link.

### Signing in

- Staff, Owner, Manager, Auditor: **Sign In** with e-mail and password.
- Admin: **Administrator Login** with `ADMIN_USERNAME` and `ADMIN_PASSWORD`.
- Demo accounts (password `Demo@1234`): `owner@`, `manager@`, `staff@`, `auditor@infosys.com`.
- New sign-ups get the **Staff** role with no outlet until an Admin assigns one.

> Change the default Milestone 1 administrator password (`PORTAL_ADMIN_PASSWORD`) and the demo passwords before any real use.

## 6. Verification checklist (for screenshots)

- [<img width="1600" height="680" alt="image" src="https://github.com/user-attachments/assets/e7756fa6-2a86-46b7-a016-c0909cc6a9df" />
 ] Sign in as two roles; open a page one of them cannot (shows the **403** page)
- [<img width="1600" height="801" alt="image" src="https://github.com/user-attachments/assets/38a342ea-bbfa-446b-8ba4-4993686db10c" />
 ] Copilot: one SQL question (SQL and row count shown) and one RAG question (citations)
- [ <img width="1600" height="714" alt="image" src="https://github.com/user-attachments/assets/ac9db6d1-dd3a-4f7c-9bef-6e2d5f015bf5" />
] 
## 7. Notes and limits

- Never commit Colab secrets, database files or notebook outputs to GitHub.
- The GPU is needed for the LLM, translation and embeddings. Without it the app runs but slowly.
- The Freight variant of the code is included but this notebook launches the Franchise app.
- The Customer role from the earlier Milestone 3 draft was replaced by **Auditor**, to match Milestone 2.

---

## Team

| Name | Responsibility |
|---|---|
| Mrunali | RAG Builder |
| Moghitha | AI Copilot |
| Kaushik B | Agent simulator |
| Adithya | Presentation (PPT) |
| Sujitha | Presentation (PPT) |
