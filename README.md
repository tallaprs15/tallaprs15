# Hi, I'm Srikant 👋

**Co-founder, [Excelerate Education](https://www.excelerateme.com.au)** · AI engineering · Full-stack · Australia

I build production AI systems for real users: document and vision intelligence, RAG, agentic workflows, and the LLM operations around them (routing, cost, telemetry, evaluation), delivered across web, iOS and Android.

> 🔒 Most of my work is commercial and lives in private repos. **I'm happy to walk through the code live or give a reviewer temporary read access.**

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
<img src="https://img.shields.io/badge/React_Native-61DAFB?logo=react&logoColor=black"/>
<img src="https://img.shields.io/badge/Expo-000020?logo=expo&logoColor=white"/>
<img src="https://img.shields.io/badge/Stripe-635BFF?logo=stripe&logoColor=white"/>
<img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white"/>
</p>

---

## 🚀 Key projects

| Project | What it is | Built with | Status |
|---|---|---|---|
| **[ExcelerateMe](https://www.excelerateme.com.au)** | AI exam-practice platform for VCE and selective-entry students: AI marking of handwritten work, an exam-paper generator, personalised learning paths. I co-founded it and am one of its two main engineers (470+ commits). | Python microservices on Cloud Run · Gemini · OpenAI · Groq · Firestore · Stripe | ✅ Live, paid |
| **ExcelerateMe mobile** | iOS and Android companion app: practice, photo submission of working, offline sync, push notifications. | React Native · Expo · TypeScript · SQLite | 🚀 App Store submitted |
| **[Excelerate Tutor Hub](https://www.tutorhub.excelerateme.com.au/)** | AI platform for tutoring organisations and schools: question banks, AI-generated exams and study notes, natural-language performance reports. | FastAPI · Milvus · LangChain + MCP · CrewAI | ✅ Live |
| **[Agent a Day](https://github.com/tallaprs15/agent-a-day)** | A new open-source AI agent every day, built with Google ADK on public MCP servers. Every agent has guardrails, a cost meter and tests, and they're all evaluated live every night on a [public dashboard](https://tallaprs15.github.io/agent-a-day/). | Google ADK · Gemini · MCP · GitHub Actions | [![agents passing](https://img.shields.io/endpoint?url=https%3A%2F%2Ftallaprs15.github.io%2Fagent-a-day%2Fbadge.json)](https://tallaprs15.github.io/agent-a-day/) |
| **[CCAR-P study guide](https://github.com/tallaprs15/ccar-p-study)** | Free, open study notes and interactive practice exams for the Claude Certified Architect – Professional exam, including a timed 63-item full-length paper. | VitePress · Vue · TypeScript | ✅ [Live site](https://ccar-p-study.netlify.app/) |
| **Explainer reels pipeline** | Turns one data file into a narrated, caption-synced short video that explains a worked exam solution. | Remotion · React · neural TTS · ffmpeg | ✅ Working |
| **[FlickRole](https://github.com/tallaprs15/flickrole-site)** | Swipe-based job discovery for Australia, with a feed that learns on the device (Bayesian learning-to-rank) and AI resume import. | SwiftUI · Jetpack Compose · Supabase · Gemini | 🚀 App Store submitted |

---

## 🏗️ ExcelerateMe at a glance

```mermaid
flowchart LR
  C["🎓 Web · 🧑‍🏫 Tutor Hub<br/>📱 iOS · 🤖 Android"] --> GW[API Gateway<br/>JWT · entitlements]
  GW --> S["37 Cloud Run services"]
  S --> AI["LLM gateway<br/>OpenAI · Gemini · Groq"]
  S --> R["Search & RAG<br/>Vertex AI Search · Milvus"]
  S --> D[(Firestore · GCS)]
  J["8 batch jobs<br/>PDF → questions"] --> D
```

## 🧠 AI engineering highlights

- **Multi-model LLM gateway.** 24 AI features, each mapped to a model in config, so I can swap or A/B-test models in production without a redeploy. Automatic fallback across vendors, per-call cost tracking and Langfuse tracing.
- **Document intelligence.** An event-driven pipeline turns exam PDFs (LaTeX, diagrams, nested parts) into structured, curriculum-tagged questions. It checks that every question was captured, repairs gaps with a stronger model, and runs a QC audit.
- **Vision marking.** Photos of handwritten working are marked against marking schemes asynchronously (Cloud Tasks), with moderation and credit checks first.
- **RAG and search.** Vertex AI Search filtered to what each student has paid for, embedding-based duplicate detection, and tenant-isolated Milvus retrieval for tutors.
- **Agents with guardrails.** An MCP-based exam-paper builder, a 9-agent learning-path planner (Google ADK), and a tutor reporting agent with PII redaction.
- **Cost control.** Cheap models by default with escalation only when needed, batch APIs, caching, atomic per-user credit metering, and scale-to-zero infrastructure.

Day to day I build with **Claude Code**, **Cursor** and **Antigravity**.

<sub>Earlier work: Terraform/AWS infrastructure as code, CI/CD pipelines · [Agentic AI course](https://github.com/tallaprs15/nebius-agentic-ai-builder-course) · [Flask API tutorial](https://github.com/tallaprs15/python-flask-api-tutorial)</sub>

---

<p align="center">
<a href="https://www.linkedin.com/in/stallapragada/"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white&style=for-the-badge"/></a>
<a href="mailto:srikant.t@gmail.com"><img src="https://img.shields.io/badge/Email-EA4335?logo=gmail&logoColor=white&style=for-the-badge"/></a>
<a href="https://excelerateeducation.substack.com"><img src="https://img.shields.io/badge/Engineering_blog-FF6719?logo=substack&logoColor=white&style=for-the-badge"/></a>
<a href="https://www.excelerateme.com.au"><img src="https://img.shields.io/badge/ExcelerateMe-6D28D9?style=for-the-badge"/></a>
</p>
