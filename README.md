# 🧠 Auto ELT Pipeline Agentic System

> **Automated ELT pipeline generator** that creates complete data warehouses from **natural language descriptions** using **LangGraph for multi-agent system**, **dlt for ingestion**, and **dbt for transformation**.

---

## 🚀 Overview

Describe your desired data pipeline in plain English — and let the **Auto ELT Agent** handle the rest.  
It automatically:

- 🔍 **Analyzes** your data sources (local files or Azure Blob Storage)
- 🏗️ **Designs** a complete **Medallion architecture** (Bronze → Silver → Gold → Views)
- 🧱 **Generates** `dbt` models for all layers (silver, gold, views, snapshots), and reusable, adujstable design plan object.
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
| **Snapshots** | SCD Type 2 tracking for historical data |
| **Gold** | Fact and dimension tables (star schema) |
| **Views** | Aggregated, business-friendly data marts |

---

## 📁 Project Structure


```
system_dir/
├── main.py                        # Execution point
├── graph/                  
│   ├── graph.py                   # Main LangGraph workflow graph
│   ├── state.py                   # AgentState model
│   └── routers.py                 # Conditional routing logic
├── agents/                        
│   ├── interpreter.py             # Parse user prompt
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
├── configs.py                     # Model and output directory configurations
├── design_plans/                  # Auto-generated dbt models after human confirmation on view_builder, for future adjustment/debugging
└── dbt_project/                   # Auto-generated dbt project from dbt_transformer
```

---

## 📁 Agentis System Workflow
<img width="853" height="590" alt="image" src="https://github.com/user-attachments/assets/4f2583dd-e70e-4dfb-819e-349bae0ceac3" />

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
```

### 3. Configurations
#### 3.1 Environment Variables

Create a `.env` file in the root directory:

```bash
OPENAI_API_KEY=sk-your-openai-key-here

# Destination: Snowflake credentials
SNOWFLAKE_ACCOUNT=your-account
SNOWFLAKE_USER=your-username
SNOWFLAKE_PASSWORD=your-password
SNOWFLAKE_DATABASE=your-database
SNOWFLAKE_WAREHOUSE=your-warehouse
SNOWFLAKE_ROLE=your-role
```

#### 3.2. Configure DLT Secrets

In `.dlt/secrets.toml`, include:

```toml
[destination.snowflake.credentials]
database = "your_database"
username = "your_username"
password = "your_password"
host = "your_host"
warehouse = "your_warehouse"
role = "your_role"

[sources.filesystem.credentials]
type = "azure"
container = "your_container_name"
connection_string = "your_connection_string"
```

#### Reference:
1. dlt hub docs: https://dlthub.com/docs/intro
2. dbt docs: https://docs.getdbt.com/docs/introduction

### 5. Run the System

```bash
cd your_system_dir
```

```bash
python main.py
```

---

## 💬 Example Usage

**Example:**

```python
Please ingest the following datasets into Snowflake and build the ELT pipeline: 

Tables: 
  census_lga_2016_g01: ./datasets/Census LGA/2016Census_G01_NSW_LGA.csv
  census_lga_2016_g02: ./datasets/Census LGA/2016Census_G02_NSW_LGA.csv
  nsw_lga_code: ./datasets/NSW_LGA/NSW_LGA_CODE.csv
  nsw_lga_suburb: ./datasets/NSW_LGA/NSW_LGA_SUBURB.csv
  listings: ./datasets/listings/*.csv
```

### What Happens Next

1. 🔍 Inspects all datasets and infers schema  
2. 🏗️ Designs the **Medallion architecture** automatically  
3. 🧱 Generates dbt models for Bronze, Silver, Gold, and Views, along with reusable, adujstable design plan object.
4. 👥 Requests human approval for schema and transformation logic  
5. ⚡ Sets up incremental/full-refresh strategies  
6. 🚀 Generates a fully dbt project 

---

## 🧩 Example Generated dbt Project

```
dbt_project/
├── macros/
├── models/
│   ├── sources.yml
│   ├── silver/
│   │   ├── slv_customers.sql
│   │   └── slv_orders.sql
│   ├── gold/
│   │   ├── fact_orders.sql
│   │   └── dm_customers.sql
│   └── views/
│       └── vw_customer_orders.sql
├── snapshots/
│   └── snp_customers.sql
│── packages.yml
│── profiles.yml
└── dbt_project.yml
```
#### Check dbt lineage graph

```bash
cd ./dbt_project
```

```bash
dbt docs serve
```
---

## 🔧 Customization

### Modify Agent Prompts

Edit `agents/prompts.py` to customize:
- Agent behavior
- Output formats
- Design heuristics

### Configure LLMs and output_dir

Update LLM settings and output_dir in `configs.py`:

```python
# Current configurations for optimal output

# Basic level for agents: intepreter, source_inspector, dataset_explorer, pipeline_orchestrator
llm = ChatOpenAI(model="gpt-4o", temperature=0)

# Med level for agents: all model builders
llm_md = ChatOpenAI(model="gpt-4.1", temperature=0)

# Enhanced level for agents: architecture_designer, reviewer
llm_en = ChatOpenAI(model="gpt-5", reasoning_effort="low", temperature=0)
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
