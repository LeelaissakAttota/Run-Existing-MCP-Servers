# 🌐 Run Existing MCP Servers: STDIO & HTTP Protocols

![Language](https://img.shields.io/badge/Language-Python%203.12+-3776AB?style=flat-square&logo=python&logoColor=white)
![Framework](https://img.shields.io/badge/Framework-FastMCP-0052CC?style=flat-square)
![Protocol](https://img.shields.io/badge/Protocol-Model%20Context%20Protocol-6A0DAD?style=flat-square)
![Transport](https://img.shields.io/badge/Transport-STDIO%20%7C%20Streamable%20HTTP-00897B?style=flat-square)
![Server](https://img.shields.io/badge/Server-Context7-FF7C00?style=flat-square)
![Focus](https://img.shields.io/badge/Focus-MCP%20Transport%20Protocols-2E7D32?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square)

---

## 📌 Project Overview

This project is a **hands-on deep dive into MCP transport protocols** — 
demonstrating how to connect Python applications to real-world MCP servers 
using both **STDIO** and **Streamable HTTP** transport layers.

Using the **Context7 MCP server**, the project shows how LLMs can retrieve 
up-to-date coding documentation for libraries like `fastmcp`, `pandas`, 
and `scikit-learn` — making AI coding assistants truly current.

**Domain:** Agentic AI — MCP Transport Protocols  
**Language:** Python 3.12+  
**MCP Server:** Context7 (real-time library documentation for LLMs)  
**Transport:** STDIO (`npx`) + Streamable HTTP  

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────┐
│         Python Client / Jupyter Notebook             │
│                                                      │
│  from fastmcp import Client                          │
│  from fastmcp.client.transports import               │
│       StdioTransport, StreamableHttpTransport        │
└──────────┬─────────────────────────┬────────────────┘
           │                         │
    STDIO Transport          HTTP Transport
    (Local subprocess)       (Remote cloud)
           │                         │
    ┌──────▼──────┐         ┌────────▼────────┐
    │  npx runs   │         │  Context7 Cloud  │
    │  Context7   │         │  MCP Server      │
    │  on your    │         │  mcp.context7.   │
    │  machine    │         │  com/mcp         │
    └─────────────┘         └─────────────────┘
           │                         │
    Both expose identical MCP tools:
    ├── resolve-library-id   → Find library ID
    └── query-docs           → Get LLM-ready docs
```

---

## 📂 Project Structure

```
Run-Existing-MCP-Servers/
│
├── Run Existing MCP Servers.ipynb   # Complete Jupyter lab notebook
└── README.md
```

---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| Language | Python 3.12+ |
| MCP Client | FastMCP Client |
| STDIO Transport | StdioTransport (`npx @upstash/context7-mcp`) |
| HTTP Transport | StreamableHttpTransport |
| External Server | Context7 MCP Server |
| Notebook | Jupyter |
| Async | asyncio + async/await |

---

## 🔌 Transport Protocols Explained

### 1️⃣ STDIO Transport (Local)

Everything runs on your machine:

```python
stdio_transport = StdioTransport(
    command="npx",
    args=["-y", "@upstash/context7-mcp"]
)
stdio_client = Client(stdio_transport)
```

```
Your Python Code ←→ [StdioTransport] ←→ Context7 Server (your machine)
   (Client)              (Bridge)          (npx subprocess)
```

**How it works:**
- `npx` downloads & runs Context7 MCP server temporarily
- `StdioTransport` bridges Python ↔ server via STDIN/STDOUT
- Everything stays local — no network required

---

### 2️⃣ Streamable HTTP Transport (Remote)

Connects to cloud-hosted MCP server:

```python
http_transport = StreamableHttpTransport(
    url="https://mcp.context7.com/mcp"
)
http_client = Client(http_transport)
```

```
Your Python Code ←→ [StreamableHttpTransport] ←→ Context7 Cloud Server
   (Client)                 (Bridge)              (mcp.context7.com)
```

---

## 🔧 Context7 MCP Tools

| Tool | Input | Output |
|---|---|---|
| `resolve-library-id` | `libraryName: str`, `query: str` | Context7 library ID (e.g. `/pandas-dev/pandas`) |
| `query-docs` | `libraryId: str`, `query: str` | LLM-optimized documentation, code examples, API refs |

### Tool Calling Pattern

```python
# Step 1 — Find library ID
async with stdio_client as client:
    response = await client.call_tool("resolve-library-id", {
        "libraryName": "fastmcp",
        "query": "I want to create a new MCP server"
    })
library_id = response.content[0].text

# Step 2 — Get documentation
async with stdio_client as client:
    docs = await client.call_tool("query-docs", {
        "libraryId": library_id,
        "query": "How to create tools in FastMCP"
    })
```

---

## 📚 What This Lab Covers

| Topic | Detail |
|---|---|
| STDIO concepts | STDIN, STDOUT, STDERR — theory + examples |
| StdioTransport | Launching MCP servers as subprocesses via npx |
| StreamableHttpTransport | Connecting to remote cloud MCP servers |
| FastMCP Client | Async context manager pattern |
| list_tools() | Discovering available tools from server |
| call_tool() | Executing tools with parameters |
| Tool inspection | Accessing `.name`, `.description`, `.inputSchema` |
| Context7 workflow | resolve-library-id → query-docs pattern |
| Libraries queried | fastmcp, pandas, scikit-learn |
| Async patterns | `async with`, `await`, non-blocking requests |

---

## ⚙️ How to Run

**Step 1 — Install dependencies:**
```bash
pip install fastmcp
npm install -g npx   # For STDIO transport
```

**Step 2 — Open Jupyter Notebook:**
```bash
jupyter notebook "Run Existing MCP Servers.ipynb"
```

**Step 3 — Run cells in order:**
- STDIO section → connects via local subprocess
- HTTP section → connects via cloud endpoint
- Tool listing → see available Context7 tools
- Tool calling → get live library documentation

---

## 🔑 Key Concept — Same Interface, Different Transport

```python
# STDIO — local process
async with Client(StdioTransport("npx", ["-y", "@upstash/context7-mcp"])) as client:
    tools = await client.list_tools()   # Identical API!

# HTTP — remote cloud
async with Client(StreamableHttpTransport("https://mcp.context7.com/mcp")) as client:
    tools = await client.list_tools()   # Same API!
```

**Once connected, the MCP interface is identical regardless of transport!** ✅

---

## 🎓 Skills Demonstrated

- MCP transport protocol comparison — STDIO vs HTTP
- FastMCP Client with async context manager pattern
- StdioTransport — subprocess-based MCP server launching
- StreamableHttpTransport — remote cloud MCP connection
- Tool discovery with `list_tools()`
- Tool execution with `call_tool()` + parameters
- Tool schema inspection (name, description, inputSchema)
- Context7 two-step documentation workflow
- Real-time library documentation retrieval for LLMs
- STDIO fundamentals — STDIN, STDOUT, STDERR
- Async Python — asyncio, await, non-blocking I/O
- Jupyter notebook-based MCP development

---

## 📜 Certifications

| Certification | Issuer | Platform |
|---|---|---|
| IBM Data Science Professional Certificate | IBM | Coursera |
| IBM Generative AI Professional Certificate | IBM | Coursera |
| IBM Agentic AI with RAG Certificate | IBM | Coursera |
| IBM RAG and Agentic AI Professional Certificate | IBM | Coursera |

---

## 🤝 Connect with Me

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Leela%20A-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/leela-a)
[![Gmail](https://img.shields.io/badge/Gmail-attotaleelaissak@gmail.com-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:attotaleelaissak@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-Leelaissakattaota-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/Leelaissakattaota)
