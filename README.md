# 📚 Library AI Agent — LangGraph ReAct Agent on IBM watsonx.ai

![Python](https://img.shields.io/badge/Python-3.11-blue?style=flat-square&logo=python)
![IBM watsonx.ai](https://img.shields.io/badge/IBM-watsonx.ai-052FAD?style=flat-square&logo=ibm)
![LangGraph](https://img.shields.io/badge/LangGraph-ReAct%20Agent-orange?style=flat-square)
![LangChain](https://img.shields.io/badge/LangChain-IBM-green?style=flat-square)
![Granite](https://img.shields.io/badge/Model-Granite%203.3%208B%20Instruct-blueviolet?style=flat-square)
![Google Search](https://img.shields.io/badge/Tool-Google%20Search-red?style=flat-square&logo=google)
![RAG](https://img.shields.io/badge/Tool-RAG%20Query-yellow?style=flat-square)

> A **university library AI assistant** built as a LangGraph ReAct agent on **IBM watsonx.ai Agent Lab** — powered by **IBM Granite 3.3 8B Instruct**. The agent uses **Google Search** and **RAG (Retrieval-Augmented Generation)** tools to help students find study materials, course-specific books, and trending resources by topic and academic level. The project covers agent creation, local testing, AI Service deployment to an IBM watsonx.ai deployment space, and live preview via the watsonx.ai Agent Preview interface.

---

## 📌 Problem Statement

Students often struggle to efficiently discover the right study resources — the right book, the right level, and the right course alignment — from large and overwhelming library catalogs. This project builds an AI agent that:

- **Understands student queries** — handles course-specific, topic-based, and level-based (beginner/intermediate/advanced) book requests
- **Searches live web sources** using Google Search for trending and up-to-date resources
- **Grounds answers in document knowledge** using a RAG Vector Index specifically built around Python resources
- **Guides students conversationally** — asks clarifying questions about academic level and course code before surfacing recommendations
- **Deploys as a live AI Service** on IBM watsonx.ai, accessible via REST API or the Agent Preview chat interface

---

## 🎯 Objectives

- Configure and authenticate a LangGraph ReAct agent using **IBM watsonx.ai** and **LangChain IBM**
- Use **`ibm/granite-3-3-8b-instruct`** as the foundation model for agent reasoning
- Equip the agent with two tools: **GoogleSearch** (web search) and **RAGQuery** (vector index retrieval)
- Define a structured **system prompt (LibraAI instructions)** covering query clarification, response formatting, and safety guardrails
- Package the agent as an **AI Service function** (`gen_ai_service`) with request/response JSON schemas
- **Promote** the vector index asset from project to deployment space
- **Store and deploy** the AI Service to an IBM watsonx.ai deployment space using the Deployment Notebook
- **Test** the deployed agent via the watsonx.ai REST API and the live Agent Preview UI

---

## 📂 Project Files

| File | Description |
|---|---|
| `library_ai_agent.py` | Standalone Python script — authentication, tools, agent creation, and interactive CLI loop |
| `LibraryAIAgentStandardNotebook.ipynb` | Agent Lab standard notebook — agent setup, tool binding, graph visualisation, and single-turn invocation |
| `LibraryAIAgentDeploymentNotebook.ipynb` | Deployment notebook — AI Service packaging, promotion, storage, deployment, and REST API testing |

---

## 🤖 Agent Configuration

### Model

| Property | Value |
|---|---|
| Model ID | `ibm/granite-3-3-8b-instruct` |
| Provider | IBM watsonx.ai |
| Temperature | 0 (standard notebook) / 0.3 (Python script) |
| Max Tokens | 2000 (standard) / 1000 (script) |
| Top P | 1 (standard) / 0.9 (script) |
| Frequency Penalty | 0 |
| Presence Penalty | 0 |

### Tools

| Tool | Type | Description |
|---|---|---|
| `GoogleSearch` | Utility Agent Tool (watsonx Toolkit) | Live web search — used for trending topics, recent resources, and broad queries not covered by the RAG index |
| `RAGQuery` | Utility Agent Tool (watsonx Toolkit) | Vector index retrieval — searches the Python knowledge document index to ground answers in specific library content |

### System Prompt (LibraAI Instructions)

The agent operates under a structured system prompt that enforces:

- **Query clarification** — always asks for course code and edition preference before responding
- **Structured response format** — course-tagged replies with title, author, availability, location, and call number
- **Safety guardrails** — never stores full student IDs (masks last 4 digits), requires authentication for reservations
- **Persistence** — tries multiple tool formulations and query languages before declaring a query unsolvable
- **Markdown rendering** — formats code snippets, tables, links, and images using markdown syntax

---

## 🔬 Architecture & Methodology

```
IBM watsonx.ai Agent Lab
       │
       ▼
Standard Notebook (LibraryAIAgentStandardNotebook.ipynb)
       │   ├── IBM Cloud API Key Authentication
       │   ├── APIClient + ChatWatsonx initialisation
       │   │       └── model: ibm/granite-3-3-8b-instruct
       │   ├── Tool Creation
       │   │       ├── GoogleSearch (Toolkit utility tool)
       │   │       └── RuntimeContext setup
       │   ├── LangGraph ReAct Agent
       │   │       ├── create_react_agent(chat_model, tools, MemorySaver, instructions)
       │   │       └── Graph visualisation via Mermaid API
       │   └── Single-turn invocation
       │           └── agent.invoke(messages, thread_id="42")
       │
       ▼
Deployment Notebook (LibraryAIAgentDeploymentNotebook.ipynb)
       │
       ├── 1. Credentials & space setup
       │       └── Credentials(url, api_key) → APIClient → set default space
       │
       ├── 2. Promote assets to deployment space
       │       └── Vector Index (RAG) promoted from project → space
       │
       ├── 3. Define AI Service function (gen_ai_service)
       │       ├── Recreates ChatWatsonx + tools inside the service closure
       │       ├── RAGQuery tool bound to promoted vector_index_id
       │       ├── GoogleSearch tool for live web queries
       │       └── ReAct agent with LibraAI system instructions
       │
       ├── 4. Local test
       │       └── RuntimeContext → local_function(context) → print response
       │
       ├── 5. Store AI Service
       │       ├── Software spec: runtime-24.1-py3.11
       │       ├── Request schema: { messages: [{role, content}] }
       │       └── client.repository.store_ai_service(metadata, gen_ai_service)
       │
       ├── 6. Deploy AI Service
       │       ├── Deployment name: Library AI Agent Deployment Notebook
       │       ├── Sample questions configured for Agent Preview UI
       │       └── client.deployments.create(ai_service_id, metadata)
       │
       └── 7. Test deployed service
               └── client.deployments.run_ai_service(deployment_id, payload)
```

---

## 🖥️ Agent Preview & Sample Interactions

The deployed agent is accessible via the **watsonx.ai Agent Preview** interface with a pre-configured welcome screen and four sample questions:

| Sample Question | Agent Behaviour |
|---|---|
| *Give me personalized study resources* | Clarifies academic level and subject/course before suggesting tailored resources |
| *What are some beginner/intermediate/advanced books for Data Science* | Returns a levelled book list — beginner through advanced — sourced via Google Search |
| *What are the best resources for learning Machine Learning* | Recommends curated ML books and online platforms (Coursera, edX) with brief descriptions |
| *Show me books currently in high demand in Generative AI* | Uses Google Search to surface trending Generative AI books, reports, and articles with links |

### Agent Preview Screenshots

| Screenshot | Description |
|---|---|
| `Home_Page.png` | Agent welcome screen with description, banner image, and four sample question cards |
| `Greeting.png` | Agent greeting response — "Hi, I am Library AI Agent. How can I help you?" |
| `Sample_Question_1.png` | Personalized resources query — agent asks for academic level and course clarification |
| `Sample_Question_2.png` | Data Science books by level — structured beginner/intermediate/advanced response |
| `Sample_Question_3.png` | Machine Learning resources — curated book list with descriptions |
| `Sample_Question_4.png` | Generative AI high-demand books — live Google Search results with reference links |
| `Deployment.png` | Deployed agent in IBM watsonx.ai deployment space — Online status, Preview tab active |

---

## 💡 Key Insights

- **LangGraph ReAct architecture** enables the agent to reason through multi-step queries — deciding when to search the web versus when to query the RAG vector index before returning a response
- **MemorySaver checkpointing** preserves conversation history within a session (`thread_id`), allowing the agent to maintain context across follow-up questions in a single conversation thread
- **Dual-tool design** — GoogleSearch handles open-ended and trending queries while RAGQuery grounds responses in curated library content, giving the agent both breadth and depth
- **AI Service packaging** wraps the entire agent (model + tools + instructions) into a portable, deployable closure — enabling seamless promotion from project to deployment space without rewriting agent logic
- **Structured system instructions** ensure consistent, safe, and formatted responses across all student queries — the agent never returns raw unformatted output
- **IBM Granite 3.3 8B Instruct** provides a compact yet capable reasoning backbone, well-suited for tool-use and instruction-following in an educational assistant context

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.11 | Core programming language |
| IBM watsonx.ai | Foundation model hosting, Agent Lab, deployment spaces |
| `ibm-watsonx-ai` | API client, Toolkit, Tool, RuntimeContext, Credentials |
| `langchain-ibm` | ChatWatsonx — LangChain-compatible watsonx.ai chat model |
| `langchain-core` | HumanMessage, AIMessage, StructuredTool |
| `langgraph` | create_react_agent, MemorySaver — ReAct agent framework |
| Google Search Tool | Utility agent tool via watsonx Toolkit — live web retrieval |
| RAGQuery Tool | Utility agent tool via watsonx Toolkit — vector index retrieval |
| Jupyter Notebook | Interactive development and deployment orchestration |

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install ibm-watsonx-ai langchain-ibm langgraph langchain-core requests
```

### Running the Python Script

```bash
# Clone the repository
git clone https://github.com/PJ2001-IND/Library-AI-Agent.git

# Navigate to the project directory
cd Library-AI-Agent

# Run the interactive CLI agent
python library_ai_agent.py
```

> ⚠️ You will be prompted for your **IBM Cloud API Key** on startup. Obtain your key from [IBM Cloud IAM](https://cloud.ibm.com/docs/account?topic=account-userapikey&interface=ui).

### Running the Notebooks

```bash
# Launch Jupyter
jupyter notebook

# Open Standard Notebook for agent testing
# LibraryAIAgentStandardNotebook.ipynb

# Open Deployment Notebook to deploy as an AI Service
# LibraryAIAgentDeploymentNotebook.ipynb
```

> ⚠️ **Before running the Deployment Notebook:** Update `space_id`, `source_project_id`, and `vector_index_id` with your own IBM watsonx.ai project and deployment space values. The vector index must be created and available in your watsonx.ai project before promotion.

---

## 📁 Project Structure

```
📦 Library-AI-Agent
 ┣ 🐍 library_ai_agent.py                          # Standalone agent script with CLI loop
 ┣ 📓 LibraryAIAgentStandardNotebook.ipynb         # Agent Lab standard notebook
 ┣ 📓 LibraryAIAgentDeploymentNotebook.ipynb       # AI Service deployment notebook
 ┣ 🖼️  Home_Page.png                               # Agent preview — welcome screen
 ┣ 🖼️  Greeting.png                                # Agent preview — greeting response
 ┣ 🖼️  Deployment.png                              # Deployed agent in watsonx.ai space
 ┣ 🖼️  Sample_Question_1.png                       # Personalized resources query
 ┣ 🖼️  Sample_Question_2.png                       # Data Science books by level
 ┣ 🖼️  Sample_Question_3.png                       # Machine Learning resources
 ┣ 🖼️  Sample_Question_4.png                       # Generative AI trending books
 ┣ 📄 requirements.txt                             # Python dependencies
 ┗ 📄 README.md                                    # Project documentation
```

---

## ⚠️ Disclaimer

> This project is built purely for **educational and demonstrative purposes** as an AI agent case study on IBM watsonx.ai. The Library AI Agent uses mock tool implementations in `library_ai_agent.py` and is designed to showcase LangGraph ReAct agent architecture, IBM watsonx.ai Agent Lab workflows, and AI Service deployment — not to serve as production library management or student advisory tooling. The deployment notebook contains hardcoded space and project IDs specific to the author's IBM Cloud environment; replace these with your own values before running.

---

## 🔭 Future Scope

- Integrate with a **real Integrated Library System (ILS) API** (e.g., Alma, Koha, or WorldCat) to replace mock tools with live catalog search, real-time availability checks, and actual hold placement
- Add a **`place_hold` tool** to allow authenticated students to reserve books directly through the agent
- Implement **student authentication** using IBM App ID or a university SSO provider, replacing mock user IDs with verified sessions
- Extend the **RAG vector index** beyond Python to cover all major subjects — CS, Physics, Mathematics, and more — for a comprehensive multi-discipline library assistant
- Build a **Streamlit or Gradio front-end** as an alternative to the watsonx.ai Agent Preview, deployable on a university intranet
- Add **multi-turn memory persistence** using a database-backed checkpointer (PostgreSQL or Redis) instead of in-memory MemorySaver for production-grade session continuity
- Incorporate **usage analytics** — track which topics, books, and queries are most popular to inform library acquisitions and collection development

---

## 👤 Author

**Praasuk Jain**
- GitHub: [@PJ2001-IND](https://github.com/PJ2001-IND)
- LinkedIn: [praasuk-jain](https://www.linkedin.com/in/praasuk-jain-425b6b1a3/)

---

> ⭐ If you found this project useful, consider giving it a star!
