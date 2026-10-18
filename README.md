# Hi, I'm Srikant 👋

**Co-founder, [Excelerate Education](https://www.excelerateme.com.au)** · AI engineering · Full-stack · Australia

I build production AI systems for real users: vision and document intelligence, RAG, agentic workflows, and the LLM operations around them (cost, routing, telemetry, evaluation), delivered across web, iOS and Android.

> 🔒 Most of my work is commercial and lives in private repos. The diagrams below describe the real systems. **I'm happy to walk through the code live or give a reviewer temporary read access.**

<p align="center"><img src="assets/stats.svg" alt="37 microservices · 8 AI batch jobs · 24 LLM features · 6 agent frameworks · 4 apps built · 21 subjects" width="100%"/></p>

<p align="center">
<img src="https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white"/>
<img src="https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white"/>
<img src="https://img.shields.io/badge/Cloud_Run-4285F4?logo=googlecloud&logoColor=white"/>
<img src="https://img.shields.io/badge/Vertex_AI-4285F4?logo=googlecloud&logoColor=white"/>
<img src="https://img.shields.io/badge/Firestore-FFCA28?logo=firebase&logoColor=black"/>
<img src="https://img.shields.io/badge/Gemini-8E75B2?logo=googlegemini&logoColor=white"/>
<img src="https://img.shields.io/badge/OpenAI-412991?logo=openai&logoColor=white"/>
<img src="https://img.shields.io/badge/Groq_·_Llama-F55036"/>
<img src="https://img.shields.io/badge/Google_ADK-34A853?logo=google&logoColor=white"/>
<img src="https://img.shields.io/badge/MCP-000000"/>
<img src="https://img.shields.io/badge/LangGraph-1C3C3C?logo=langchain&logoColor=white"/>
<img src="https://img.shields.io/badge/CrewAI-FF5A50"/>
<img src="https://img.shields.io/badge/Milvus-00A1EA"/>
<img src="https://img.shields.io/badge/Langfuse-0A0A0A"/>
<img src="https://img.shields.io/badge/React_Native-61DAFB?logo=react&logoColor=black"/>
<img src="https://img.shields.io/badge/Expo-000020?logo=expo&logoColor=white"/>
<img src="https://img.shields.io/badge/Stripe-635BFF?logo=stripe&logoColor=white"/>
<img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white"/>
</p>

---

## 🏗️ ExcelerateMe: the platform

An AI exam-practice and tutoring platform for VCE and selective-entry students. It is live at **[excelerateme.com.au](https://www.excelerateme.com.au)** and has paid subscriptions. I co-founded it with Bipin Mehta and have made **470+ commits** across the AI, backend and infrastructure.

```mermaid
flowchart LR
  subgraph Clients
    SW[🎓 Student web]
    TW[🧑‍🏫 Tutor Hub web]
    IOS[📱 iOS]
    AND[🤖 Android]
  end
  subgraph Edge
    GW[API Gateway<br/>JWT · entitlements · rate limits]
  end
  subgraph Core["Cloud Run · 37 services"]
    Q["Question & search"]
    M[AI marking]
    P[Paper builder agent]
    LP[Learning-path agents]
    R[Tutor reports agent]
    S["Subscriptions & credits"]
  end
  subgraph AI["AI layer"]
    LLM[LLM gateway<br/>OpenAI · Gemini · Groq]
    VS[Vertex AI Search<br/>+ embeddings]
    MV[(Milvus)]
  end
  subgraph Data
    FS[(Firestore)]
    GCS[(Cloud Storage)]
    CT[[Cloud Tasks]]
  end
  SW & TW & IOS & AND --> GW --> Core
  Core --> LLM
  Q --> VS
  R --> MV
  M --> CT
  Core --> FS
  M --> GCS
  S --> STRIPE[Stripe]
```

---

## 🧠 LLM gateway: many models, one interface

I don't depend on a single vendor. One shared gateway exposes **24 named AI features**, and each feature is assigned a model through Firestore config. That config is re-read every 60 s, so I can **swap or A/B-test models in production without a redeploy**.

```mermaid
flowchart LR
  F["Feature call<br/>e.g. answer_verification"] --> CFG{{"Feature → model<br/>config (60 s cache)"}}
  CFG --> CR{"Credit gate<br/>balance check"}
  CR -->|ok| PRI[Primary model]
  PRI -->|"error / 5xx"| FB[Fallback model<br/>lazy, single hop]
  PRI & FB --> LED[("Atomic credit debit<br/>+ append-only ledger")]
  PRI & FB --> TEL["Telemetry: tokens · latency<br/>cost USD · fallback_used"]
  TEL --> LF[Langfuse traces]
```

| Workload | Models | Why |
|---|---|---|
| Answer marking, generation, notes | **GPT-5.4-mini** primary → **Gemini 3 Flash** fallback | Quality per dollar, with a second vendor for resilience |
| Vision: handwriting, diagrams, PDFs | **Gemini Flash** (Vertex), **Gemini Pro** for escalation only | Multimodal at scale; Pro used only where Flash fails |
| Bulk classification / tagging | **Groq gpt-oss-120b** · **Llama 4 Scout** via the **Batch API** | Sub-second inference; batch is about 50% cheaper |
| Embeddings | **gemini-embedding-001** (768-d) · fastembed MiniLM | Similar-question search and duplicate detection |
| Relevance scoring | **TypeSafe Jev** | Typed judgement scores that keep generated papers on topic |
| Document layout | **Azure Document Intelligence** · **Google Document AI** | Figure cropping and page classification |

---

## 📄 Document intelligence: turning exam PDFs into structured questions

Exam papers are full of LaTeX, diagrams and nested parts (a → i, ii). A 16-stage, event-driven pipeline turns them into curriculum-tagged questions with quality scores.

```mermaid
flowchart TB
  subgraph S1["① Ingest & plan"]
    direction LR
    U[PDF upload] -->|Eventarc| WF[Cloud Workflow] --> J[Cloud Run Job] --> A["Probe · classify<br/>blueprint · subject"] --> PL["Planner agent<br/>maps question anchors"]
  end
  subgraph S2["② Extract & self-repair"]
    direction LR
    X["⚡ Parallel extraction<br/>per PDF slice · Flash"] --> COV{"Coverage<br/>verified?"}
    COV -->|missing anchors| REP["Repair on single-question<br/>slices · Pro escalation"]
    REP --> COV
  end
  subgraph S3["③ Enrich & quality-gate"]
    direction LR
    D[Dedupe] --> T[Topic classification] --> DG["Diagram detection · vision<br/>300 DPI crop · link"] --> QC["4-tier QC audit<br/>LaTeX · numeric invariance"] --> DB[(Firestore)]
    QC -.->|still missing| H[Held for review]
  end
  S1 --> S2
  S2 -->|all anchors covered| S3
```

**Engineering details**
- 🎯 **Coverage check, then repair:** after extraction, the expected question anchors are compared against the output. Gaps are re-extracted on single-question slices using the stronger model.
- 🔢 **Numeric invariance check:** catches the LLM silently changing numbers when it rewrites LaTeX.
- 🧯 **Self-healing runs:** a reconciler runs every 5 minutes and fails any run whose heartbeat has gone stale. Curriculum mappings go to a proposals collection first, can be promoted with `--execute`, and can be rolled back.
- 🧾 **OMR + handwriting:** reads scanned answer sheets and photos of student working, matched to each question part.

---

## ✍️ AI marking of handwritten work

```mermaid
sequenceDiagram
  autonumber
  participant App as Student (web / iOS / Android)
  participant API as Answer service
  participant GCS as Cloud Storage
  participant CT as Cloud Tasks
  participant W as Verification worker
  participant LLM as Vision LLM
  App->>API: Photos of working (up to 5)
  API->>GCS: Store images
  API->>CT: Enqueue verification (async)
  API-->>App: 202 Accepted, instant UI
  CT->>W: Deliver task (authenticated)
  W->>W: Regex guard → LLM moderation → credit gate
  W->>LLM: Question diagrams + student pages + marking scheme
  LLM-->>W: Marks per part + feedback
  W->>W: Normalise to 0–100 rubric scale
  Note over CT,W: Errors return 5xx and Cloud Tasks retries
```

---

## 🔎 RAG and search

```mermaid
flowchart TB
  subgraph SS["Student search"]
    Q1[Query] --> E1{Entitlement<br/>scope filter} --> VAS[Vertex AI Search<br/>validated questions]
    NJ[Nightly reindex · Cloud Scheduler] --> VAS
  end
  subgraph SIM["Similar questions & dedupe"]
    QT[Question text] --> EMB["gemini-embedding-001<br/>768-d · batches of 100"]
    EMB --> COS["Cosine top-20<br/>matrix product"]
    COS -->|"≥ 0.985 & different paper"| DUP[Duplicate flag]
  end
  subgraph TH["Tutor Hub notes RAG"]
    PDF[Curriculum PDFs] --> SEG["TOC / layout-aware<br/>chunking · PyMuPDF"]
    SEG --> MIL[("Milvus · IVF_FLAT<br/>tenant-isolated")]
  end
```

---

## 🤖 Agentic workflows

Agents run the most critical flows, each with **hard guardrails**.

| Agent system | Framework | What it does | Guardrails |
|---|---|---|---|
| **Paper Builder ("Build with AI")** | MCP server (12 tools) + custom tool loop | Builds a full exam paper from a conversation | Max 14 steps / 30 turns · JWT + entitlement checks · **the model never sees answers or IDs** · up to 4 auto-repair quality rounds |
| **Learning-path planner** | Google ADK · **9 agents** | Goal intake → diagnostic → curriculum map → plan → validate → coach → adapt | Plan-validator agent checks the plan before it is shown |
| **Exam-intelligence agent** | Google ADK on Vertex Agent Engine | Analyses past papers end to end with about 30 tools | Idempotency keys · switches to the other extraction method if one returns nothing |
| **Tutor report agent** | LangChain + MCP adapters → **FastMCP (30 tools)** | Natural-language reports on student performance | Tool set limited per scope · PII redaction · prompt-injection filters |
| **Study-notes crew** | CrewAI · 10 roles | Researcher → author → reviewer → image reviewer → formatter | Reviewer stages before publishing |
| **Extractor v1** | LangGraph + DSPy | Extraction graph with a quality-check → retry loop | DSPy-optimised prompts |

```mermaid
flowchart LR
  T[Tutor / student prompt] --> AG[Agent loop<br/>≤14 steps]
  AG -->|tool call| MCP[[MCP tools<br/>search · add · swap · score]]
  MCP --> J{"Jev relevance score<br/>topic_fit × pref_fit"}
  J -->|low| SW[Auto-swap question] --> MCP
  J -->|ok| PAPER[📄 Exam paper]
```

---

## 💰 AIOps: cost and performance

| Lever | Implementation | Impact |
|---|---|---|
| **Model tiering** | Flash by default, Pro only for repair | about 80% cheaper and 3× faster per extraction |
| **Batch inference** | Groq Batch API for tagging (4.2k questions in 212 requests) | about 50% off bulk jobs |
| **Caching** | MCQ explanations cached on question docs · quality results keyed by content fingerprint · embeddings reused when text is unchanged | No repeat calls for the same content |
| **Credits metering** | SDK-level wrappers debit every OpenAI / Gemini / Groq call atomically, with a ledger | Per-user cost control that can be audited |
| **Pricing table** | Per-model USD/token in YAML, cost computed per call, unpriced models flagged | Real-time unit economics |
| **Rate limiting** | Firestore fixed-window limiter shared across Cloud Run instances (fails open) | Abuse protection without Redis |
| **Async everything** | Cloud Tasks for marking · bounded concurrency (12 parallel, 10 s / 25 s timeouts) | Fast UI, predictable latency |
| **Scale to zero** | Cloud Run min 0 / max 3 for most services | Pay only for traffic |
| **Observability** | Langfuse traces + OpenInference · background telemetry writer (tokens, latency, cost, fallback) | Every LLM call can be traced |
| **Delivery** | 17 multistage Dockerfiles · dev → UAT → prod on separate GCP projects · Expo EAS for mobile | Safe, repeatable releases |

---

## 📱 Apps

| | Student web | Tutor Hub | iOS | Android |
|---|:-:|:-:|:-:|:-:|
| Stack | Python services + JS | FastAPI + Jinja | React Native · Expo | React Native · Expo |
| AI | Marking, search, paper builder, learning paths | Exams, AI notes, report agent | Photo marking, practice | Photo marking, practice |
| Status | ✅ Live | ✅ Built · in UAT | 🚀 App Store submitted (Oct 2026) | ✅ Built |

Also: 🎬 an **automated reels pipeline** (Remotion + neural TTS) that turns a single data file into a narrated, word-synced Instagram Reel explaining a worked exam solution.

---

## 🛠️ AI-native engineering workflow

| Tool | How I use it |
|---|---|
| **Claude Code** | Main coding agent. Project `CLAUDE.md`, custom skills (input-guard, integration, video pipeline), MCP servers wired in. **120+ AI-co-authored commits** |
| **Cursor** | Project rules (`.cursor/rules`) and skills for editing in the IDE |
| **Antigravity** | Agent-first IDE for parallel tasks |

---

## 🧪 Other projects

- **FlickRole:** swipe-based job discovery for Australia. Native **Swift** iOS and **Kotlin** Android apps on **Supabase**. [Site](https://github.com/tallaprs15/flickrole-site)
- **[Agentic AI builder course](https://github.com/tallaprs15/nebius-agentic-ai-builder-course)** · Google ADK and OpenAI Agents experiments · LLM fine-tuning
- Earlier: Terraform/AWS infrastructure as code, CI/CD pipelines, and a [Flask API tutorial](https://github.com/tallaprs15/python-flask-api-tutorial)

---

<p align="center">
<a href="https://www.linkedin.com/in/stallapragada/"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white&style=for-the-badge"/></a>
<a href="mailto:srikant.t@gmail.com"><img src="https://img.shields.io/badge/Email-EA4335?logo=gmail&logoColor=white&style=for-the-badge"/></a>
<a href="https://excelerateeducation.substack.com"><img src="https://img.shields.io/badge/Engineering_blog-FF6719?logo=substack&logoColor=white&style=for-the-badge"/></a>
<a href="https://www.excelerateme.com.au"><img src="https://img.shields.io/badge/ExcelerateMe-6D28D9?style=for-the-badge"/></a>
</p>
