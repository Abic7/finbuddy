# FinBuddy --- Technical README

## Overview

FinBuddy is a multi-agent LLM-powered financial analysis system designed
to transform raw transaction CSV files into categorized data, insights,
recommendations, and a structured financial report.

## Architecture

FinBuddy is built using a modular multi-agent architecture:

### Agents

-   **CategorizerAgent**: Performs transaction classification.
-   **InsightsAgent**: Detects trends, anomalies, and spending patterns.
-   **RecommenderAgent**: Generates personalized financial guidance.
-   **ReporterAgent**: Produces structured financial summaries.

### System Components

-   **Orchestrator**: Coordinates the agent pipeline.
-   **CSV Tool**: Handles ingestion, cleaning, and preprocessing.
-   **Session Manager**: Maintains per-run session state.
-   **MemoryBank**: Stores long-term behaviors across runs.

## Tech Stack

-   **LLMs**: OpenAI GPT-4.1 & GPT-4.1-mini
-   **Python 3.10+**
-   **Modular agent pipeline**
-   **Observability**: Logging and tracing throughout execution

## Installation

``` bash
git clone <your-repo-url>
cd finbuddy
pip install -r requirements.txt
```

## Usage

Run the full pipeline:

``` bash
python main.py data/sample_transactions.csv
```

Pipeline steps: 1. Load & clean CSV 2. Classify transactions 3. Extract
behavioral insights 4. Generate recommendations 5. Produce structured
financial report

## File Structure

    finbuddy/
    │
    ├── agents/
    │   ├── categorizer.py
    │   ├── insights.py
    │   ├── recommender.py
    │   └── reporter.py
    │
    ├── tools/
    │   ├── csv_tool.py
    │   └── memory.py
    │
    ├── core/
    │   ├── orchestrator.py
    │   └── session_manager.py
    │
    ├── data/
    ├── main.py
    └── README.md

##Architucture
High Level Arch
                    ┌───────────────────────────┐
                    │      User's CSV File      │
                    └─────────────┬─────────────┘
                                  │
                                  ▼
                         ┌────────────────┐
                         │   CSV Tool     │
                         │ (tools/csv...) │
                         └───────┬────────┘
                                 │  Raw DataFrame
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

## Extending the System

### Adding a new agent

1.  Create a file in `agents/`
2.  Implement standardized agent interface
3.  Register agent in Orchestrator

### Adding new tools

-   Follow existing conventions in `tools/`
-   Ensure tool is injectable into agents

## Logging & Debugging

-   Central logging system tracks:
    -   Inputs/outputs of each agent
    -   Pipeline execution timing
    -   Memory usage and updates

## License

MIT License (customize as needed)
