# Query MySQL via MCP & Local LLM

This guide shows how to:

* Run the **MySQL MCP server** (`@executeautomation/database-server`) on Windows
* Install and use the **MCP client CLI** (`llm` from `mcp-client-cli`) to talk to that server
* Use a **local Ollama model** or **OpenAI compatible API**

> All commands below are for **Windows Command Prompt (cmd.exe)**.

---

## Prerequisites

* **Python 3.12+ (64‑bit)** installed and on PATH
* **pipx** installed
* **Node.js 20+** installed (needed for `npx` to run the MCP server)
* **MySQL** running locally with a database `treedb` and a user you can use
* (Optional) **Ollama** installed if you want a local model (eg: qwen3:8b)

### Verify basics

```cmd
python --version
node --version
npm --version
```

### Install pipx (if you don’t have it)

```cmd
python -m pip install --user pipx
pipx ensurepath
```

Close & reopen your terminal so `%USERPROFILE%\.local\bin` is on PATH.

---

## Start the MySQL MCP server

Open **a dedicated Command Prompt** and run (leave it open):

```cmd
npx -y @executeautomation/database-server ^
  --mysql ^
  --host localhost ^
  --database treedb ^
  --port 3306 ^
  --user colab ^
  --password colab123
```

You should see logs like "MySQL connection established successfully" and "Starting MCP server…".

---

## Install the MCP Client CLI (`llm`)

Install with pipx:

```cmd
pipx install mcp-client-cli==1.0.4 --python 3.12
pip install -r requirements.txt  # (or) pipx inject mcp-client-cli -r requirements.txt
```

Check that `llm` is on PATH:

```cmd
llm --help
```

If `llm` is not found, ensure `%USERPROFILE%\.local\bin` is in your **User** PATH, then open a **new** terminal.

> To see what got installed into the pipx venv:

```cmd
pipx list
pipx runpip mcp-client-cli show langgraph
pipx runpip mcp-client-cli show langgraph-prebuilt
pipx runpip mcp-client-cli show langgraph-sdk
pipx runpip mcp-client-cli show langgraph-checkpoint
```

---

## Use a local **Ollama** model

Install and start Ollama, then pull a model (example: Qwen 3 8B):

   ```cmd
   ollama run qwen3:8b
   ```

## Update ~/.llm/config.json


   ```json
    {
        "systemPrompt": "You are a helpful assistant that can also use MCP tools.",
        "llm": {
            "provider": "openai",
            "model": "qwen3:8b",
            "base_url": "http://localhost:11434/v1",
            "api_key": "ollama",
            "temperature": 0.2
        },
        "mcpServers": {
            "mysql": {
                "command": "npx",
                "args": [
                    "-y",
                    "@executeautomation/database-server",
                    "--mysql",
                    "--host", "localhost",
                    "--database", "treedb",
                    "--port", "3306",
                    "--user", "colab",
                    "--password", "colab123"
                ]
            }
        }
    }
   ```

## (Optional) Use OPENAI API
Replace llm section in above json with this

   ```json
    "llm": {
        "provider": "openai",
        "model": "gpt-4.1-mini",
        "api_key": "YOUR_OPENAI_API_KEY",
        "temperature": 0.2
    }
   ```
---

## Sample DB Query

   ```cmd
   llm "Show the first 5 rows from the customers table in treedb."
   ```

   # Override config.json params
   ```cmd
   llm --model qwen3:14b "Show the first 5 rows from the customers table in treedb."
   ```



---


