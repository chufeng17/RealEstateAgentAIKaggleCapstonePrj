# Real Estate Agent Inbox Copilot

*A multi-agent AI system that interprets email conversations, extracts client insights, and infers daily tasks for real estate professionals.*

> **This repository contains my submission for the Google Agents Intensive Capstone Project (Enterprise Agents Track).**

---

# 📌 Problem

Real estate agents manage nearly all client communication through email. Critical signals—follow-ups, decisions, status updates, property feedback, and next steps—are buried inside long, unstructured threads.
Agents must manually:

* determine what stage each client is in
* extract tasks and follow-up actions
* remember past interactions
* track overdue or pending items

This is slow, error-prone, and difficult to scale.

---

# 🧠 Solution

**Real Estate Inbox Copilot** is a multi-agent workflow system built using the **Google Agent Development Kit (ADK)**.
It processes full email histories and transforms them into structured operational state:

* contact classification
* inferred pipeline stage
* canonical set of active tasks (derived deterministically)
* context-aware summaries
* long-term client memory

The system reads the **entire email history** for each contact and computes the current task list from scratch.
Completed tasks disappear automatically when later emails indicate progression, ensuring reproducible, deterministic behavior.

The included synthetic datasets (`initial` and `after_sync`) demonstrate how client state evolves over time.

---

# 🏗️ Architecture

Below is the high-level architecture of the system:

```
                       ┌────────────────────────────┐
                       │ Raw Email JSON Dataset     │
                       └──────────────┬─────────────┘
                                      │
                                      ▼
                         Inbox Ingestion Agent
                  (normalize threads & message structure)
                                      │
                                      ▼
                       Contact Classification Agent
                    (lead type, pipeline stage inference)
                                      │
                                      ▼
                          Task & Agenda Agent
      (deterministic reasoning over full email history → active tasks)
                                      │
                                      ▼
                         ADK Memory (per-contact state)
                                      │
                                      ▼
                           Assistant / Chat Agent
      (answers context-heavy questions using Memory + RAG + structure)
                                      │
                                      ▼
                       Frontend UI (Next.js Dashboard)
```

### Components

* **Backend:** FastAPI (Python)
* **Frontend:** Next.js (TypeScript)
* **Database:** SQLite
* **Embedding Store:** Local vector store
* **Agents:** Implemented using the Google ADK (custom multi-agent workflow)
* **Memory:** ADK Memory per contact
* **RAG:** Embedding-based retrieval over email bodies

---

# 🔄 Technical Workflow

1. **Email Ingestion**
   Sample email JSON is loaded and normalized into structured threads.

2. **Contact Classification**
   ADK agent determines whether each participant is a new lead, active search, under contract, nurture, etc.

3. **Task Inference**
   The Task & Agenda Agent reads *all messages* for each contact and generates a canonical set of active tasks.

   * If later emails indicate completion, tasks simply no longer appear.
   * Overdue and pending states are inferred contextually.

4. **Memory Storage**
   Long-term client context is stored using ADK Memory.

5. **Assistant/Chat Agent**
   Provides grounded responses using:

   * memory
   * RAG retrieval
   * structured contact/task state
     Example: *“What’s the story with Emma?”*

---

# 🛠️ Setup Instructions

### 1. Install Backend Dependencies

```bash
cd backend
pip install -r requirements.txt
```

### 2. Configure Environment

Copy `.env.example` → `.env` and fill in required fields.

### 3. Run Backend

```bash
cd backend
uvicorn app.main:app --reload
```

### 4. Install Frontend Dependencies

```bash
cd frontend
npm install
```

### 5. Run Frontend

```bash
cd frontend
npm run dev
```

### 6. Create Demo Account

```bash
curl -X POST "http://localhost:8000/api/v1/auth/register" \
     -H "Content-Type: application/json" \
     -d '{
           "email": "alex.chan@remaxmetrohomes.com",
           "password": "password123",
           "name": "Alex Chan",
           "role": "admin"
         }'
```

---

# 🧪 Demo Reset (Testing Only)

During development or evaluation, you may want to reset the application back to a clean state while keeping user accounts intact. The system provides both a UI-based reset and an API-based reset.

Option A — Reset from the Frontend (Recommended)

Go to:

http://localhost:3000/settings


On the Settings page, click the Reset Demo Data button.

This triggers the backend reset endpoint and clears all demo-related data:

contacts

email threads

email messages

tasks

embeddings

Your user accounts remain untouched.

This is the easiest way to switch between datasets or restart the project state during testing.

---

# 📂 Testing With Initial & After-Sync Datasets

### Load Initial Dataset

1. Set:

   ```
   DATASET_PATH=./data/sample_emails_initial.json
   ```
2. Reset demo:

   ```
   /admin/reset-demo
   ```
3. Sync:

   ```
   /sync/emails
   ```

### Load After-Sync Dataset

1. Set:

   ```
   DATASET_PATH=./data/sample_emails_after_sync.json
   ```
2. Sync again:

   ```
   /sync/emails
   ```

The system recomputes all client state from the full email history.

---

🎥 Submission & Demo Videos
📌 Capstone Submission Video

https://youtu.be/AuTQniQl-6Q

📌 Full System Demo Video

https://youtu.be/6yZnrJEaKrs
