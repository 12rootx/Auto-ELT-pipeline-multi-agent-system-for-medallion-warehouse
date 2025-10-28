# 🧠 Auto ELT Agent

> **Automated ELT pipeline generator** that creates complete data warehouses from **natural language descriptions** using **LangGraph for multi-agent system**, **dlt for ingestion**, and **dbt for transformation**.

---

## 🚀 Overview

Describe your desired data pipeline in plain English — and let the **Auto ELT Agent** handle the rest.  
It automatically:

- 🔍 **Analyzes** your data sources (local files or Azure Blob Storage)
- 🏗️ **Designs** a complete **Medallion architecture** (Bronze → Silver → Gold → Views)
- 🧱 **Generates** `dbt` models for all layers (silver, gold, views, snapshots)
- ⚙️ **Sets up** ingestion pipelines with `dlt`
- ❄️ **Deploys** to **Snowflake**, with proper incremental/full-refresh strategies
- 🧑‍💻 **Includes human-in-the-loop approvals** for key design decisions

---

## 🏗️ Architecture

**Medallion Data Flow:**
```
Bronze (Raw) → Silver (Cleaned) → Gold (Business-Ready) → Views (Data Marts)
```

### Generated Components

| Layer | Description |
|--------|-------------|
| **Bronze** | Raw data ingestion via `dlt` |
| **Silver** | Cleaned and standardized models (deduplication, normalization) |
| **Gold** | Fact and dimension tables (star schema) |
| **Snapshots** | SCD Type 2 tracking for historical data |
| **Views** | Aggregated, business-friendly data marts |

---

## 📁 Project Structure

```
elt_pipeline/
├── main.py                        # Entry point
├── graph/                         # LangGraph workflow definition
│   ├── graph.py                   # Main workflow graph
│   ├── state.py                   # AgentState model
│   └── routers.py                 # Conditional routing logic
├── agents/                        # Agent implementations
│   ├── interpreter.py             # Parse user instructions
│   ├── source_inspector.py        # Analyze data sources
│   ├── architecture_designer.py   # Design warehouse blueprint
│   ├── silver_builder.py          # Generate Silver layer models
│   ├── gold_builder.py            # Generate Gold layer models
│   ├── view_builder.py            # Create data marts and views
│   ├── reviewers.py               # Validation and quality review
│   └── nodes/                     # Specialized workflow nodes
│       ├── human_node.py
│       ├── ELT_config_node.py
│       ├── design_reuse_checker.py
│       ├── dlt_ingestor.py
│       └── dbt_transformer.py
├── utils/                         # Helper utilities
│   ├── helper_functions.py
│   ├── dbt_configs.py
│   ├── ELTConfigurator.py
│   ├── agent_tools.py
│   └── save_sql.py
├── configs.py                     # Model and directory configurations
├── design_plans/                  # Auto-generated architecture designs
└── dbt_project/                   # Auto-generated dbt project
```

---

## ⚙️ Quick Start

### 1. Create Environment

```bash
conda create -n elt_agent python=3.11 -y
conda activate elt_agent
```

### 2. Install Dependencies

```bash
# Install from requirements
pip install -r requirements.txt

# Or install manually
pip install langgraph langchain-openai dlt pyyaml python-dotenv dbt-core snowflake-connector-python
```

### 3. Configure Environment Variables

Create a `.env` file in the root directory:

```bash
OPENAI_API_KEY=sk-your-openai-key-here

# Optional: Snowflake credentials
SNOWFLAKE_ACCOUNT=your-account
SNOWFLAKE_USER=your-username
SNOWFLAKE_PASSWORD=your-password
SNOWFLAKE_DATABASE=your-database
SNOWFLAKE_WAREHOUSE=your-warehouse
SNOWFLAKE_ROLE=your-role
```

### 4. Configure DLT Secrets

In `.dlt/secrets.toml`, include:

```toml
[destination.snowflake.credentials]
database = "your_database"
username = "your_username"
password = "your_password"
host = "your_account.snowflakecomputing.com"
warehouse = "your_warehouse"
role = "your_role"

[sources.filesystem.credentials]
type = "azure"
container = "your_container_name"
connection_string = "your_connection_string"
```

### 5. Run the System

```bash
python main.py
```

---

## 💬 Example Usage

**Example:**

```python
Please ingest the following datasets into Snowflake and build the ELT pipeline: 

Tables: 
  census_lga_2016_g01: /Users/rootx/DataScience_Std/LANGGRAPH/de_agent/datasets/Census LGA/2016Census_G01_NSW_LGA.csv
  census_lga_2016_g02: /Users/rootx/DataScience_Std/LANGGRAPH/de_agent/datasets/Census LGA/2016Census_G02_NSW_LGA.csv
  nsw_lga_code: /Users/rootx/DataScience_Std/LANGGRAPH/de_agent/datasets/NSW_LGA/NSW_LGA_CODE.csv
  nsw_lga_suburb: /Users/rootx/DataScience_Std/LANGGRAPH/de_agent/datasets/NSW_LGA/NSW_LGA_SUBURB.csv
  listings: /Users/rootx/DataScience_Std/LANGGRAPH/de_agent/datasets/listings/*.csv
```

### What Happens Next

1. 🔍 Inspects all datasets and infers schema  
2. 🏗️ Designs the **Medallion architecture** automatically  
3. 🧱 Generates dbt models for Bronze, Silver, Gold, and Views  
4. 👥 Requests human approval for schema and transformation logic  
5. ⚡ Sets up incremental/full-refresh strategies  
6. 🚀 Generates a fully deployable dbt project

---

## 🧩 Example Generated dbt Project

```
dbt_project/
├── models/
│   ├── sources.yml
│   ├── silver/
│   │   ├── slv_customers.sql
│   │   └── slv_orders.sql
│   ├── gold/
│   │   ├── fact_orders.sql
│   │   └── dim_customers.sql
│   └── views/
│       └── vw_customer_orders.sql
├── snapshots/
│   └── snp_customers.sql
└── dbt_project.yml
```

---

## 🔧 Customization

### Modify Agent Prompts

Edit `agents/prompts.py` to customize:
- Agent behavior
- Output formats
- Design heuristics

### Configure LLMs

Update LLM settings in `configs.py`:

```python
# Example configurations
llm = ChatOpenAI(model="gpt-4o", temperature=0)
llm_en = ChatOpenAI(model="gpt-4.1", temperature=0)  # Enhanced version
```

---

## 🧠 Key Features Recap

✅ Natural language → Complete ELT pipeline  
✅ Automated architecture generation  
✅ Human approval checkpoints  
✅ Seamless integration: dlt + dbt + Snowflake  
✅ Modular agent-based design  
✅ Reusable configuration and design plans  

---

**Author:** *Anna Chen*  
**Tech Stack:** LangGraph · LangChain · dlt · dbt · Snowflake · Python 3.11
