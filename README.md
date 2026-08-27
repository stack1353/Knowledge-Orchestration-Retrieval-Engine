# 🧠 KORE: Knowledge Orchestration & Retrieval Engine

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg?logo=python&logoColor=white)](https://www.python.org/)
[![Kafka](https://img.shields.io/badge/Apache%20Kafka-7.8-black.svg?logo=apachekafka&logoColor=white)](https://kafka.apache.org/)
[![Neo4j](https://img.shields.io/badge/Neo4j-5.12-008CC1.svg?logo=neo4j&logoColor=white)](https://neo4j.com/)
[![ChromaDB](https://img.shields.io/badge/ChromaDB-Vector_Store-FF6F00.svg)](https://www.trychroma.com/)
[![CrewAI](https://img.shields.io/badge/CrewAI-Multi--Agent-orange.svg)](https://www.crewai.com/)
[![Streamlit](https://img.shields.io/badge/Streamlit-UI-FF4B4B.svg?logo=streamlit&logoColor=white)](https://streamlit.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)

> **An event-driven, hybrid Graph-Vector RAG & Multi-Agent orchestration platform for enterprise engineering intelligence, automated compliance auditing, and root-cause incident forensics.**

---

## 📌 Overview

Modern engineering organizations produce massive streams of fragmented knowledge across code repositories, pull requests, issue trackers, CI/CD pipelines, and incident discussion channels. Traditional vector-only RAG systems suffer from:
1. **Relational Blindness:** Incapable of answering *"Who approved PR #505 before INC-2024 occurred?"* or *"Which microservice owns this database dependency?"*
2. **Data Swamps:** Single-bucket vector stores pollute policy documents with ephemeral Slack messages and commit diffs.
3. **Hallucination Risk:** Standard LLMs invent plausible-sounding commit hashes and reviewers without factual verification.

**KORE (Knowledge Orchestration & Retrieval Engine)** addresses this with a dual-memory architecture:
- **Structural Memory (Neo4j Property Graph):** Explicitly maps organizational topology, code ownership, PR review chains, service dependencies, and cause-effect links.
- **Semantic Memory (ChromaDB Multi-Collection):** Segregates unstructured text into dedicated collections (`company_policies`, `jira_tickets`, `git_changes`, `slack_conversations`).
- **Autonomous Multi-Agent Swarm (CrewAI):** Role-specialized agents (Forensic Researcher, Policy Sentinel, Incident Commander, Technical Writer) operate with strict anti-hallucination protocols to investigate, audit, and report.

---

## 🏗️ System Architecture

```
┌────────────────────────────────────────────────────────────────────────┐
│                          EVENT SOURCES                                 │
│     GitHub (PRs)  │  Jira (Tickets)  │  Git (Commits)  │  Slack Chats  │
└────────────┬──────────────┬──────────────────┬──────────────┬──────────┘
             │              │                  │              │
             ▼              ▼                  ▼              ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        APACHE KAFKA (KRaft)                            │
│  raw-git-prs  │  raw-jira-tickets  │  raw-git-commits │ raw-slack-chats│
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                 EVENT INGESTION ENGINE (kafka_consumer.py)             │
│        • Entity Correlation  • State Tracking  • Dual Indexing         │
└───────────────────┬────────────────────────────────┬───────────────────┘
                    │                                │
     [Graph Nodes & Edges]               [Domain-Specific Chunks]
                    │                                │
                    ▼                                ▼
       ┌────────────────────────┐       ┌────────────────────────┐
       │     Neo4j Graph DB     │       │   ChromaDB Vector DB   │
       │                        │       │                        │
       │  • User -> WROTE -> PR │       │  • company_policies    │
       │  • PR -> FIXES -> Issue│       │  • jira_tickets        │
       │  • Service Dependency  │       │  • git_changes         │
       │  • Incident Cause Link │       │  • slack_conversations │
       └───────────┬────────────┘       └───────────┬────────────┘
                   │                                │
                   └───────────────┬────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                       MULTI-AGENT INTELLIGENCE                         │
│                                                                        │
│   Interactive Pipeline (main_crew.py)  Autonomous Watcher (runner.py)  │
│   ┌───────────────────────────────┐    ┌───────────────────────────┐   │
│   │ • Query Triage Officer        │    │ • Policy Sentinel (SEC)   │   │
│   │ • Senior Forensic Researcher  │    │ • Real-time Threat Audit  │   │
│   │ • Technical Reporter          │    │ • 2-Tier Secret Scanning  │   │
│   └───────────────────────────────┘    └───────────────────────────┘   │
│                                                                        │
│   KoreTools:                                                           │
│   • PR State Checker (Reviewers/Mergers)  • Ticket Root Cause Checker  │
│   • Multi-Collection Search               • Recent Changes Tracker     │
│   • Expert Finder                         • Compliance Regex Verifier  │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      STREAMLIT ENTERPRISE UI (app.py)                  │
│       • Live Alert Feed (Critical / Warning / Pass)  • System Stats    │
│       • Interactive Forensic Chat                    • Query History   │
└────────────────────────────────────────────────────────────────────────┘
```

---

## ✨ Key Features

### 1. Hybrid Graph-Vector RAG
- **Graph Traversal via Cypher:** Maps authors, reviewers, merged PRs, Jira tickets, microservices, and dependencies.
- **Domain-Segregated Embeddings:** Prevents context cross-contamination by routing queries only to relevant ChromaDB collections using Cohere `embed-english-v3.0`.

### 2. Autonomous Multi-Agent Swarm (CrewAI)
| Agent | Role | Goal / Specialty |
| :--- | :--- | :--- |
| 🛡️ **Query Triage Officer** | Request Routing | Directs user questions to specific forensic tools and specialists. |
| 🔬 **Senior Forensic Researcher** | Fact Verification | Zero-tolerance for speculation; queries Graph DB directly for exact reviewers and commits. |
| 📝 **Technical Reporter** | Synthesis & Reporting | Produces executive reports with TL;DR, evidence bullet points, citations, and confidence ratings. |
| 🚨 **Policy Sentinel** | Continuous Auditing | Monitors real-time PRs for security leaks (AWS keys, private keys, passwords) and policy violations (e.g., Friday deploy freeze). |
| ⚡ **Incident Commander** | Outage Forensics | Determines blast radius, affected services, and candidate suspect commits during P0/P1 incidents. |

### 3. Two-Tier Real-Time Security & Compliance Scanning
- **Tier 1 (Fast Regex / <50ms):** Immediate pre-commit/PR scanning for AWS access keys, RSA private keys, and credential patterns.
- **Tier 2 (Deep LLM Audit):** Contextual policy verification against organizational guidelines (e.g., `POL-001` Friday deployment freeze, `CODE-200` minimum reviewers).

### 4. Enterprise Streamlit Dashboard
- **Live System Activity Indicator:** Real-time pulse check of backend services.
- **Alert Feed with Filtering:** Live stream of security warnings, policy violations, and approval passes.
- **Interactive Forensic Chat:** Natural language query interface with step-by-step agent tool execution feedback.
- **Query History:** Historical audit trail of past queries and agent responses.

---

## 📂 Project Structure

```
KORE/
├── docker-compose.yml           # Infrastructure: Kafka (KRaft), Kafka UI, Neo4j, ChromaDB
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment variable template
├── README.md                    # Project documentation
│
├── db/
│   └── init.sql                 # Database initialization scripts
│
└── src/
    ├── config.py                # Global configurations
    ├── test_backend.py          # Kafka round-trip test script
    │
    ├── brain/                   # AI Agent Core & Tooling
    │   ├── agents.py            # CrewAI agent definitions & strict forensic prompts
    │   ├── tools.py             # Tools: PR checker, Ticket RCA, ChromaDB search, Expert finder
    │   ├── main_crew.py         # Interactive worker consuming 'agent-jobs' via Kafka
    │   └── autonomous_runner.py # Background autonomous watcher publishing alerts
    │
    ├── ingestion/               # Event Stream Ingestion
    │   ├── init_kafka.py        # Initializes all Kafka topics
    │   ├── kafka_consumer.py    # Ingests raw streams into Neo4j and ChromaDB
    │   └── indexer.py           # Document indexing helpers
    │
    ├── simulation/              # Event Generation & Seeding
    │   ├── events.json          # Pre-packaged DevOps event sequence
    │   ├── generator.py         # Kafka event producer with timestamp injection
    │   ├── scenarios.py         # Scenario runner with configurable intervals
    │   └── seed_brain.py        # Seeds Neo4j org graph & ChromaDB company policies
    │
    └── ui/
        └── app.py               # Streamlit enterprise dashboard
```

---

## 🛠️ Technology Stack

| Layer | Technologies |
| :--- | :--- |
| **Event Streaming** | Apache Kafka 7.8 (KRaft Mode), `kafka-python` |
| **Knowledge Graph** | Neo4j 5.12 (APOC Plugin enabled), Cypher, `langchain-neo4j` |
| **Vector Database** | ChromaDB (HTTP Client), Multi-Collection Strategy |
| **Embeddings & LLM** | Cohere (`embed-english-v3.0`), Google Gemini (`gemini-1.5-flash`), LiteLLM |
| **Agent Orchestration** | CrewAI 0.x |
| **Frontend UI** | Streamlit |
| **Containerization** | Docker, Docker Compose |

---

## 🚀 Getting Started

### Prerequisites
- [Docker & Docker Compose](https://docs.docker.com/get-docker/) installed and running
- [Python 3.10+](https://www.python.org/downloads/)
- API Keys for **Google Gemini** (`GOOGLE_API_KEY`) and **Cohere** (`COHERE_API_KEY`)

---

### 1. Clone & Setup Environment

```bash
# Clone repository
git clone https://github.com/stack1353/Knowledge-Orchestration-Retrieval-Engine.git
cd Knowledge-Orchestration-Retrieval-Engine

# Create virtual environment
python -m venv .venv

# Activate virtual environment
# Windows (PowerShell):
.\.venv\Scripts\Activate.ps1
# Linux / macOS:
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

---

### 2. Configure Environment Variables

Create a `.env` file in the root directory:

```env
# AI Model API Keys
GOOGLE_API_KEY="your-google-gemini-api-key"
COHERE_API_KEY="your-cohere-api-key"

# Infrastructure Endpoints
KAFKA_BOOTSTRAP_SERVERS="localhost:9092"
NEO4J_URI="bolt://localhost:7687"
NEO4J_USER="neo4j"
NEO4J_PASSWORD="password"
CHROMA_HOST="localhost"
SIMULATION_FILE="src/simulation/events.json"
```

---

### 3. Start Infrastructure Services

Launch Kafka, Kafka UI, Neo4j, and ChromaDB via Docker Compose:

```bash
docker compose up -d
```

Verify service availability:
- **Neo4j Browser:** [http://localhost:7474](http://localhost:7474) *(Credentials: `neo4j` / `password`)*
- **Kafka UI:** [http://localhost:8080](http://localhost:8080)
- **ChromaDB API:** [http://localhost:8000/api/v1/heartbeat](http://localhost:8000/api/v1/heartbeat)

---

### 4. Initialize Topics & Seed Knowledge

```bash
# Step 4a: Create Kafka topics
python -m src.ingestion.init_kafka

# Step 4b: Seed Organizational Graph & Policies
python -m src.simulation.seed_brain
```

---

### 5. Run Ingestion & Agent Pipelines

Open separate terminal windows (with active `.venv`) to run the platform components:

#### Terminal 1: Event Ingestor (Neo4j & ChromaDB Consumer)
```bash
python -m src.ingestion.kafka_consumer
```

#### Terminal 2: Interactive AI Brain (Processes User Queries)
```bash
python -m src.brain.main_crew
```

#### Terminal 3: Autonomous Watcher (Real-time Compliance & Alerts)
```bash
python -m src.brain.autonomous_runner
```

#### Terminal 4: Streamlit Dashboard UI
```bash
streamlit run src/ui/app.py
```

---

### 6. (Optional) Run DevOps Simulation

Simulate a live stream of GitHub PRs, Jira status changes, Slack discussions, and incident triggers:

```bash
python -m src.simulation.scenarios
```

---

## 💡 Example Queries to Test

Once the system is running and seeded, enter these queries into the **Streamlit UI**:

| Query | What It Tests |
| :--- | :--- |
| `Who reviewed PR #505 and what was the outcome?` | **Graph Traversal:** Verifies reviewer relationship vs author in Neo4j. |
| `What caused ticket INC-2024 and who wrote the offending commit?` | **Root Cause Analysis:** Traverses Ticket -> Commit -> User edges. |
| `Can we deploy a hotfix to production on Friday at 4 PM?` | **Policy RAG:** Searches `company_policies` collection for `POL-001` rules & exceptions. |
| `Who is the primary expert for the PaymentGateway service?` | **Expert Finder:** Interrogates service ownership and team graph nodes. |
| `Who fixed the memory leak in the payment system and what had happened?` | **Hybrid Search:** Combines ChromaDB context search with Neo4j commit verification. |

---

## 🔒 Security & Compliance Protocols

KORE implements strict safety and governance checks:
- **Zero-Speculation Protocol:** Researchers are explicitly instructed never to infer reviewers from Slack mentions or invent ticket identifiers.
- **Credential Interception:** Automated regex scanners halt PR events exposing AWS keys (`AKIA...`), RSA private keys, or plaintext passwords before deployment.
- **Audit Logging:** Every query, tool execution, and alert is timestamped and indexed into Kafka event streams for non-repudiation.

---

## 📜 License

Distributed under the [MIT License](LICENSE). See `LICENSE` for more information.