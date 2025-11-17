# FinBuddy — Technical README

## Overview

FinBuddy is a modular, multi-agent, LLM-powered financial analysis system
designed to transform raw banking transaction CSV files into categorized data,
behavioral insights, personalized recommendations, and a structured financial report.

The system supports **hybrid LLM execution**:
- **OpenAI GPT-4.1 / GPT-4.1-mini** (cloud)
- **LM Studio local models (e.g., gpt2-smashed)** as automatic fallback

This ensures FinBuddy runs even when offline, rate-limited, or developing locally.

---

## Architecture

FinBuddy uses a clean, extensible multi-agent architecture:

### **Agents**
- **CategorizerAgent** – Classifies each transaction into spending categories.
- **InsightsAgent** – Detects patterns, anomalies, monthly trends, and spending behaviors.
- **RecommenderAgent** – Generates personalized financial advice.
- **ReporterAgent** – Produces the final structured financial summary & report.

### **System Components**
- **Orchestrator** – Controls the pipeline flow between agents.
- **CSV Tool** – Ingests, validates, cleans, and preprocesses CSV files.
- **Session Manager** – Tracks state within a single run.
- **MemoryBank** – Cross-run persistent memory for long-term learning.
- **Hybrid LLM Client** – Routes prompts to OpenAI or LM Studio based on availability.

---

## Hybrid LLM Execution

FinBuddy includes a **HybridClient** that automatically chooses the LLM backend:

### Priority Order
1. **OpenAI (cloud)**  
2. **LM Studio local API**  
   - Example endpoint:  
     `http://192.168.50.230:1234/v1`
   - Example model:  
     `"gpt2-smashed"`

### LM Studio Python Integration

FinBuddy uses the OpenAI Python client to communicate with LM Studio:

```python
import openai

openai.api_base = "http://192.168.50.230:1234/v1"
openai.api_key = "not-needed"

response = openai.ChatCompletion.create(
    model="gpt2-smashed",
    messages=[{"role": "user", "content": "Hello from FinBuddy"}]
)

print(response.choices[0].message.content)
````

### Special Behavior
The system stops automatically if LM Studio returns the placeholder text:

_Returning 200 anyway_

The system also stops after two consecutive failed runs from any agent.

All agents include progress bars and verbose print statements for full visibility into the pipeline.


FinBuddy — Multi-Agent Financial Intelligence System










FinBuddy is a multi-agent, modular, LLM-powered financial analysis system that transforms raw banking transactions into:

Clean categorized data

Spending pattern insights

Personalized recommendations

A structured financial report

The system is designed for reliability, extensibility, and offline-friendly execution using a hybrid OpenAI + LM Studio model fallback system.

🚀 Features
🧠 Multi-Agent Architecture

FinBuddy uses four coordinated LLM agents:

CategorizerAgent — Cleans and classifies transaction descriptions

InsightsAgent — Detects anomalies & spending trends

RecommenderAgent — Generates personalized financial advice

ReporterAgent — Produces the final structured report

🔁 Hybrid Cloud + Local LLM Execution

If OpenAI quota fails → automatically falls back to LM Studio
(local model: gpt2-smashed)

🧩 Tooling & Observability

Detailed console logs

Per-agent progress bars

Fail-fast logic (stops after 2 consecutive failures)

LM Studio “Returning 200 anyway” detection

💾 Session & Memory

Session logs for each run

Long-term MemoryBank comparing behaviors across sessions

🏗️ System Architecture
High-Level Agent Pipeline
                    ┌───────────────────────────┐
                    │      User's CSV File      │
                    └─────────────┬─────────────┘
                                  │
                                  ▼
                         ┌────────────────┐
                         │    CSV Tool    │
                         └───────┬────────┘
                                 │ Raw DataFrame
                                 ▼
                     ┌────────────────────────────┐
                     │      Categorizer Agent     │
                     └─────────────┬──────────────┘
                                   │ Categorized DF
                                   ▼
                     ┌────────────────────────────┐
                     │       Insights Agent       │
                     │     + MemoryBank           │
                     └─────────────┬──────────────┘
                                   │ Insights
                                   ▼
                     ┌────────────────────────────┐
                     │     Recommender Agent      │
                     └─────────────┬──────────────┘
                                   │ Recommendations
                                   ▼
                     ┌────────────────────────────┐
                     │       Reporter Agent        │
                     └─────────────┬──────────────┘
                                   │ Final Report
                                   ▼
                           ┌─────────────────┐
                           │    CLI Output    │
                           └─────────────────┘
| Component     | Technology                                        |
| ------------- | ------------------------------------------------- |
| LLMs          | OpenAI GPT-4.1 / GPT-4.1-mini / LM Studio (local) |
| Language      | Python 3.10+                                      |
| Libraries     | pandas, tqdm, python-dotenv, requests, openai     |
| Architecture  | Multi-agent orchestrated pipeline                 |
| Observability | Logging, progress bars, verbose tracing           |

📦 Installation





















