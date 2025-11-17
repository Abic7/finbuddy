FinBuddy — Technical README
Overview

FinBuddy is a modular, multi-agent, LLM-powered financial analysis system designed to transform raw transaction CSV files into:

✔ Categorized financial data
✔ Behavioral insights
✔ Personalized recommendations
✔ A structured final financial report

FinBuddy can run using OpenAI cloud models, but also supports a hybrid fallback system using LM Studio local models when OpenAI quota is exceeded — ensuring the pipeline never breaks.

Architecture

FinBuddy uses a multi-agent pipeline.
Each agent performs one stage of the financial analysis:

Agents
1. CategorizerAgent
Classifies transactions using an LLM (cloud or local).
Handles ambiguous or messy descriptions.
Adds "Category" column to your dataframe.

2. InsightsAgent
Detects patterns in spending.
Spots anomalies.
Summarizes financial behavior.
Uses MemoryBank to compare across runs.

3. RecommenderAgent
Generates personalized financial advice.
Uses insight summaries to guide output.

4. ReporterAgent
Produces final structured financial report.
Clean, readable, and formatted for CLI users.

System Components
Orchestrator
Coordinates the entire agent pipeline execution.

CSV Tool
Handles file parsing, cleaning, and preprocessing.

Session Manager
Tracks logs, run-specific state, timestamps, and debugging output.

MemoryBank
Stores historical behavior across multiple executions.

Hybrid LLM Client (OpenAI + LM Studio)
FinBuddy includes a custom hybrid model loader:

Try OpenAI first
If quota errors → fallback to LM Studio automatically

LM Studio uses:
http://192.168.50.230:1234/v1
Uses OpenAI-compatible ChatCompletion API

LM Studio fallback is only used if:
OpenAI responds with insufficient_quota
OR network errors occur
OR LM Studio gives invalid output such as "Returning 200 anyway"

Tech Stack
Python 3.10+
OpenAI GPT-4.1 / GPT-4.1-mini
LM Studio (local model: gpt2-smashed)
Pandas
Modular agent pipeline
Progress bars and detailed print states
Observability: full verbose logging

Installation
git clone <your-repo-url>
cd finbuddy_agents
pip install -r requirements.txt

Create your .env file:
OPENAI_API_KEY="YOUR_KEY"
Name must be exactly .env

Usage
Process a transaction CSV:
python main.py data/sample_transactions.csv

Pipeline stages:
Load CSV
Categorize transactions
Generate insights
Produce recommendations
Create final report

File Structure
finbuddy_agents/
│
├── agents/
│   ├── categorizer_agent.py
│   ├── insights_agent.py
│   ├── recommender_agent.py
│   └── reporter_agent.py
│
├── tools/
│   ├── csv_tool.py
│   └── hybrid_llm_client.py
│
├── core/
│   ├── agent_orchestrator.py
│   ├── agent_session.py
│   ├── memory_bank.py
│
├── data/
│   └── sample_transactions.csv
│
├── main.py
└── README.md

Architecture Diagram
High-Level Pipeline
                    ┌───────────────────────────┐
                    │      User's CSV File      │
                    └─────────────┬─────────────┘
                                  │
                                  ▼
                         ┌────────────────┐
                         │   CSV Tool     │
                         │ (tools/csv...) │
                         └───────┬────────┘
                                 │ Raw DataFrame
                                 ▼
                     ┌───────────────────────────┐
                     │    Categorizer Agent      │
                     │ agents/categorizer_agent  │
                     └─────────────┬─────────────┘
                                   │ Categorized DF
                                   ▼
                     ┌───────────────────────────┐
                     │      Insights Agent       │
                     │ agents/insights_agent     │
                     │  + MemoryBank (core/)     │
                     └─────────────┬─────────────┘
                                   │ Insights
                                   ▼
                     ┌───────────────────────────┐
                     │   Recommender Agent       │
                     │ agents/recommender_agent  │
                     └─────────────┬─────────────┘
                                   │ Recommendations
                                   ▼
                     ┌───────────────────────────┐
                     │      Reporter Agent       │
                     │ agents/reporter_agent     │
                     └─────────────┬─────────────┘
                                   │ Final Report
                                   ▼
                           ┌─────────────────┐
                           │    CLI Output    │
                           │    (main.py)     │
                           └─────────────────┘

       ┌────────────────────────────────────────────────────────────┐
       │           Session (core/agent_session.py)                  │
       └────────────────────────────────────────────────────────────┘

       ┌────────────────────────────────────────────────────────────┐
       │           MemoryBank (core/memory_bank.py)                 │
       └────────────────────────────────────────────────────────────┘

Extending the System
Add a new Agent
   Create file under agents/
   Follow the same run(self, df, session) interface
   Register inside agent_orchestrator.py
Add new Tools
   Create inside tools/
   Make sure they're injectable and modular

Logging & Debugging
   FinBuddy logs:
      Agent start/end
      API fallback status
      Session steps
      Progress bars
      LM Studio raw responses
      Stops execution if:
         2 consecutive failures occur
         LM Studio returns "Returning 200 anyway"
      This makes debugging extremely transparent.
      

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





















